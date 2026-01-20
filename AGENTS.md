# PROJECT KNOWLEDGE BASE

## OVERVIEW
Recommendation system for the X "For You" feed. Combines in-network posts (Thunder) and out-of-network candidates (Phoenix retrieval) and ranks via a Grok-based transformer.

## STRUCTURE
```
./
├── candidate-pipeline/   # Rust pipeline traits and execution flow
├── home-mixer/           # Rust service orchestrating the feed pipeline
├── phoenix/              # Python ML models (retrieval + ranking)
└── thunder/              # Rust in-memory post store + ingestion
```

## WHERE TO LOOK
| Task | Location | Notes |
|------|----------|-------|
| Pipeline orchestration | home-mixer/ | Server and pipeline wiring |
| Candidate pipeline traits | candidate-pipeline/ | Source/Hydrator/Filter/Scorer/Selector/SideEffect |
| ML ranking + retrieval | phoenix/ | JAX/Haiku models |
| In-network post store | thunder/ | In-memory store + ingestion |

## CONVENTIONS
- Python formatting: ruff, line length 100 (`phoenix/pyproject.toml`).
- Scorers/hydrators must preserve candidate order and count (no dropping).

## COMMANDS
```bash
# Phoenix models
uv run run_ranker.py
uv run run_retrieval.py

# Phoenix tests
uv run pytest test_recsys_model.py test_recsys_retrieval_model.py
```

## NOTES
- No Cargo.toml present in this repo; Rust build/test commands are not documented here.
- `home-mixer/lib.rs` notes `clients`, `params`, `util` excluded from the open source release.
- Phoenix ranking enforces candidate isolation in attention masking.
