# phoenix/ AGENTS

## OVERVIEW
Python JAX/Haiku models for retrieval and ranking. Two-stage pipeline: retrieval (two-tower) then ranking (transformer with candidate isolation).

## WHERE TO LOOK
| Task | Location | Notes |
|------|----------|-------|
| Ranking model | recsys_model.py | Transformer-based ranker |
| Retrieval model | recsys_retrieval_model.py | Two-tower retrieval |
| Transformer core | grok.py | Grok-based model |
| Inference helpers | runners.py | Runner utilities |
| Entry scripts | run_ranker.py, run_retrieval.py | CLI entry points |
| Tests | test_recsys_model.py, test_recsys_retrieval_model.py | Model tests |

## CONVENTIONS
- Ruff line length 100 (`pyproject.toml`).
- Candidate isolation: candidates attend only to user/history and self.
- Attention softmax is fp32 for numerical stability.

## COMMANDS
```bash
uv run run_ranker.py
uv run run_retrieval.py
uv run pytest test_recsys_model.py test_recsys_retrieval_model.py
```
