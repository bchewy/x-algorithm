# home-mixer/ AGENTS

## OVERVIEW
Rust service that assembles the For You feed using the candidate pipeline and exposes a gRPC endpoint.

## WHERE TO LOOK
| Task | Location | Notes |
|------|----------|-------|
| gRPC service implementation | server.rs | `ScoredPostsService::get_scored_posts` |
| Pipeline wiring | candidate_pipeline/phoenix_candidate_pipeline.rs | Uses `PhoenixCandidatePipeline::prod()` |
| Candidate sources | sources/ | Thunder + Phoenix retrieval |
| Candidate hydrators | candidate_hydrators/ | Enrich candidates |
| Query hydrators | query_hydrators/ | User context |
| Filters | filters/ | Eligibility + safety |
| Scorers | scorers/ | Ranking signals |
| Selection | selectors/ | Top-K selection |
| Side effects | side_effects/ | Post-selection actions |

## CONVENTIONS
- Keep business logic in pipeline stages, not `server.rs`.

## NOTES
- `home-mixer/lib.rs` notes `clients`, `params`, `util` are excluded from the open source release.
