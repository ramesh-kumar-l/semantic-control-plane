# 26 — Active Initiatives

> This file is the canonical **active-context** record for SCP.

_Last updated: 2026-06-21_

## Active
### INIT-004 — Phase 3 Semantic Query Engine
- **Status:** Implemented — **awaiting Phase Gate approval.**
- **Delivered:** `scp/query/` package; `Embedder` port + deterministic offline
  `HashingEmbedder`; `VectorStore` port + `InMemoryVectorStore` (derived, rebuildable
  cosine index); rule-based `planner`; trust-aware explainable `ranking`;
  `SemanticQueryEngine` service (index_entity/reindex/remove_entity/search) doing hybrid
  vector-seed + Phase 2 graph-expansion retrieval. 25 query tests (90 total); mypy
  --strict & ruff clean; search p95 ≈ 58ms at 1k entities.
- **Exit met:** hybrid recall@5 = 1.0 vs vector-only baseline 0.0 on the labeled fixture.
- **Decision recorded:** ADR-004 — deterministic hashing embedder + in-memory cosine
  index behind ports; hybrid = vector seeds + graph expansion; trust-aware ranking.

## Completed
### INIT-003 — Phase 2 Knowledge Graph
- **Status:** Complete (Phase Gate approved). `GraphStore` port + `InMemoryGraphStore`/
  `SqliteGraphStore` adapters (ADR-003); `Entity` + `Relationship` reusing Phase 1
  trust/temporal; pure BFS traversal; `KnowledgeGraph` service with cascade-delete.
### INIT-002 — Phase 1 Memory Core: Scaffold & First Slice
- **Status:** Complete (Phase Gate approved). `MemoryStore` port + adapters (ADR-002);
  `MemoryCore` service; trust + temporal on every record.
### INIT-001 — Bootstrap Memory Bank
- **Status:** Complete. Established the memory bank + Python stack (ADR-001).

## Next Candidate (awaiting approval)
### INIT-005 — Phase 4 Trust Engine
- **Status:** Proposed, blocked by Phase 3 gate approval.
- Real trust scores / confidence models / source registry / verification /
  contradiction detection (`14-trust-model.md`). Replaces the 0.5 confidence placeholder
  and the verification-factor stopgap used by Phase 3 ranking.
- Do not start before Phase 3 passes its gate (`99` §4, `04-roadmap.md`).

## Backlog (not scheduled)
- Phases 5–7 per `04-roadmap.md`.
- Learned `Embedder`, ANN/pgvector `VectorStore`, Postgres / native-graph adapters
  (future ADRs) when retrieval quality or horizontal scale demands it.
