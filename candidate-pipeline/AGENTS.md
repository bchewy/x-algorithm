# candidate-pipeline/ AGENTS

## OVERVIEW
Rust framework for candidate pipelines. Defines stage traits and the execution flow used by home-mixer.

## WHERE TO LOOK
| Task | Location | Notes |
|------|----------|-------|
| Pipeline execution flow | candidate_pipeline.rs | Core orchestration |
| Public API surface | lib.rs | Module exports |
| Stage traits | source.rs, hydrator.rs, query_hydrator.rs, filter.rs, scorer.rs, selector.rs, side_effect.rs | Generic traits |
| Shared helpers | util.rs | Common utilities |

## CONVENTIONS
- Stage traits are generic over query and candidate types.
- Query-level hydration is separate from candidate hydration.
- Scorers and hydrators must preserve candidate order and count; use filters to drop.

## NOTES
- Primary consumer is `home-mixer/`.
