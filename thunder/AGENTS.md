# thunder/ AGENTS

## OVERVIEW
Rust service for in-network post storage. Ingests post events and serves them via gRPC.

## WHERE TO LOOK
| Task | Location | Notes |
|------|----------|-------|
| Server entry point | main.rs | Bootstraps service |
| gRPC service | thunder_service.rs | Request handlers |
| In-memory store | posts/ | Post storage implementation |
| Kafka ingestion | kafka/ | Consumers |
| Kafka helpers | kafka_utils.rs | Setup/utilities |
| Deserialization | deserializer.rs | Event parsing |
| Config/args | config.rs, args.rs | Runtime config |
| Metrics | metrics.rs | Observability |

## CONVENTIONS
- `lib.rs` exports all public modules.
- Kafka event ordering is not guaranteed; deleted posts must be reconciled on init.
