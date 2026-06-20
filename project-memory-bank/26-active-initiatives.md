# 26 — Active Initiatives

> This file is the canonical **active-context** record for SCP.

_Last updated: 2026-06-20_

## Active
### INIT-003 — Phase 2 Knowledge Graph
- **Status:** Implemented — **awaiting Phase Gate approval.**
- **Delivered:** `scp/graph/` package; `GraphStore` port + `InMemoryGraphStore`/
  `SqliteGraphStore` adapters (ADR-003); `Entity` + `Relationship` models reusing
  Phase 1 trust/temporal primitives; pure BFS traversal (`breadth_first`,
  `shortest_path`); `KnowledgeGraph` service with entity/relationship CRUD, queries,
  neighbors, traverse, find_path, and cascade-delete. 33 graph tests (65 total);
  mypy --strict & ruff clean; traversal p95 ≪ 150ms.
- **Decision recorded:** ADR-003 — SQLite (two tables) behind a `GraphStore` port,
  application-side BFS traversal, trust primitives reused from Memory Core.

## Completed
### INIT-002 — Phase 1 Memory Core: Scaffold & First Slice
- **Status:** Complete (Phase Gate approved). `MemoryStore` port + adapters (ADR-002);
  `MemoryCore` service; trust + temporal on every record.
### INIT-001 — Bootstrap Memory Bank
- **Status:** Complete. Established the memory bank + Python stack (ADR-001).

## Next Candidate (awaiting approval)
### INIT-004 — Phase 3 Semantic Query Engine
- **Status:** Proposed, blocked by Phase 2 gate approval.
- Do not start before Phase 2 passes its gate (`99` §4, `04-roadmap.md`).

## Backlog (not scheduled)
- Phases 4–7 per `04-roadmap.md`.
- Postgres / native-graph adapters (future ADRs) when horizontal scale or deep
  traversal demands it.
