# 26 — Active Initiatives

> This file is the canonical **active-context** record for SCP.

_Last updated: 2026-06-20_

## Active
### INIT-002 — Phase 1 Memory Core: Scaffold & First Slice
- **Status:** Implemented — **awaiting Phase Gate approval.**
- **Delivered:** Python scaffold (`pyproject.toml`, `scp/`, `tests/`); `MemoryStore`
  port + `InMemoryStore`/`SqliteStore` adapters (ADR-002); `MemoryCore` service with
  store/get/query, lifecycle transitions, consolidation, and compression; trust +
  temporal metadata on every record. 32 tests passing; mypy --strict & ruff clean.
- **Decision recorded:** ADR-002 — SQLite as Phase 1 durable backend behind a port.

## Completed
### INIT-001 — Bootstrap Memory Bank
- **Status:** Complete. Established the memory bank + Python stack (ADR-001).

## Next Candidate (awaiting approval)
### INIT-003 — Phase 2 Knowledge Graph
- **Status:** Proposed, blocked by Phase 1 gate approval.
- Do not start before Phase 1 passes its gate (`99` §4, `04-roadmap.md`).

## Backlog (not scheduled)
- Phases 3–7 per `04-roadmap.md`.
- Postgres `MemoryStore` adapter (future ADR) when horizontal scale is needed.
