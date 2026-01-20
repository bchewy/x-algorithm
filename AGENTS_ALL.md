# AGENTS_ALL

## SUPER SIMPLE OVERVIEW
This repo is a recommendation system for the X "For You" feed. It takes posts from people you follow and posts you do not follow, scores them with a machine learning model, filters out bad or irrelevant items, and returns a ranked list.

The system has 4 main parts:
1) home-mixer (Rust): Orchestrates the pipeline and serves a gRPC API.
2) thunder (Rust): Stores recent posts in memory and serves in-network posts.
3) phoenix (Python): Machine learning models for retrieval and ranking.
4) candidate-pipeline (Rust): Reusable framework for the pipeline stages.

## END-TO-END FLOW (PLAIN ENGLISH)
1) A request arrives to home-mixer with a user id and context.
2) home-mixer gathers user context (recent actions, user features).
3) home-mixer fetches candidate posts from two sources:
   - Thunder: posts from accounts the user follows.
   - Phoenix retrieval: ML-based search for posts from the global pool.
4) Candidates are enriched with more data (post text, author info, media, etc.).
5) Filters remove ineligible posts (duplicates, too old, blocked authors, etc.).
6) Scorers add scores using ML predictions and additional heuristics.
7) The selector picks the top K posts by score.
8) Final filters run (visibility, deduping threads).
9) The ranked feed is returned.

## PIPELINE STAGES (SIMPLE)
The pipeline is made of stages in order:
1) Query Hydrators (user context)
2) Sources (candidate fetching)
3) Candidate Hydrators (data enrichment)
4) Filters (remove candidates)
5) Scorers (assign scores)
6) Selector (choose top K)
7) Post-selection filters
8) Side effects (cache, logging)

## WHERE EACH PART LIVES
- home-mixer/: pipeline wiring and gRPC service
- thunder/: in-memory post store and Kafka ingestion
- phoenix/: ML models (retrieval + ranking)
- candidate-pipeline/: traits and execution flow

## HOME-MIXER (ORCHESTRATION)
Purpose: Glue everything together and serve results.
Key files:
- home-mixer/server.rs: gRPC endpoint `ScoredPostsService::get_scored_posts`.
- home-mixer/candidate_pipeline/phoenix_candidate_pipeline.rs: pipeline wiring.
- home-mixer/query_hydrators/: user context fetch.
- home-mixer/sources/: Thunder and Phoenix retrieval sources.
- home-mixer/candidate_hydrators/: enrich candidates with metadata.
- home-mixer/filters/: eligibility and safety filters.
- home-mixer/scorers/: ML score + adjustments.
- home-mixer/selectors/: top-K selection.
- home-mixer/side_effects/: caching and logging.

Important note: home-mixer/lib.rs calls out `clients`, `params`, `util` as excluded from open source.

## THUNDER (IN-NETWORK POSTS)
Purpose: Store and serve posts from accounts a user follows.
Key files:
- thunder/main.rs: service entry point.
- thunder/thunder_service.rs: gRPC handlers.
- thunder/posts/post_store.rs: in-memory store.
- thunder/kafka/: consumes post events.

Important constraint:
- Kafka event ordering can be lost; deleted posts must be reconciled at init.

## PHOENIX (ML MODELS)
Purpose: Find and score posts using ML.
Two-stage pipeline:
1) Retrieval (two-tower model): find relevant posts quickly.
2) Ranking (transformer): score posts for the final order.

Key files:
- phoenix/recsys_retrieval_model.py: retrieval model.
- phoenix/recsys_model.py: ranking model.
- phoenix/grok.py: Grok-based transformer.
- phoenix/runners.py: inference helpers.
- phoenix/run_ranker.py, phoenix/run_retrieval.py: entry scripts.
- phoenix/test_recsys_model.py, phoenix/test_recsys_retrieval_model.py: tests.

Important constraints:
- Candidate isolation: candidates can only attend to user/history and themselves.
- Attention softmax runs in fp32 for stability.
- Ruff line length 100 (pyproject.toml).

## CANDIDATE-PIPELINE (FRAMEWORK)
Purpose: Provides reusable traits and execution flow for pipeline stages.
Key files:
- candidate-pipeline/candidate_pipeline.rs: orchestration.
- candidate-pipeline/source.rs, hydrator.rs, filter.rs, scorer.rs, selector.rs, side_effect.rs
- candidate-pipeline/query_hydrator.rs

Important constraints:
- Scorers and hydrators must preserve candidate order and count.
- Only filters can remove candidates.
- Update methods should only touch fields they own.

## COMMANDS
```bash
# Phoenix models
uv run run_ranker.py
uv run run_retrieval.py

# Phoenix tests
uv run pytest test_recsys_model.py test_recsys_retrieval_model.py
```

## SIMPLE MENTAL MODEL
Think of it like a funnel:
- Start with lots of possible posts.
- Enrich them with details.
- Filter out anything not allowed.
- Score the rest with ML.
- Pick the best ones.
- Return a ranked list.
