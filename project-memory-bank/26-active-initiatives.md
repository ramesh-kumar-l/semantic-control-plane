# 26 — Active Initiatives

> This file is the canonical **active-context** record for SCP.

_Last updated: 2026-06-21_

## Active
### INIT-005 — Phase 4 Trust Engine
- **Status:** Implemented — **awaiting Phase Gate approval.**
- **Delivered:** `scp/trust/` package; `SourceRegistry` (reliability weighting);
  `ConfidenceModel` (real per-source initial confidence — replaces the 0.5 placeholder);
  explainable `scoring` (`base = weighted blend of reliability/confidence/recency`, gated
  by a verification factor; `TrustAssessment` retains all components + a reconstructable
  explanation); signal-driven `VerificationPolicy` state machine; `ContradictionDetector`
  with reliability-weighted `reconcile`; `TrustEngine` service tying them together (pure,
  synchronous, no I/O, no storage access).
- **Replacement:** additive optional `confidence_model` callable injected into
  `MemoryCore`/`KnowledgeGraph` (default `None` preserves behavior); no Phase 1/2 → Phase 4
  import (dependency inversion). Phase 3 ranking left untouched — it improves automatically
  once stored confidence is real.
- **Exit met:** scores explainable/reproducible; 0.5 placeholder replaced in the wired path.
- **Decision recorded:** ADR-005.

## Completed
### INIT-004 — Phase 3 Semantic Query Engine
- **Status:** Complete (Phase Gate approved). `scp/query/` package; `Embedder` port +
  deterministic offline `HashingEmbedder`; `VectorStore` port + `InMemoryVectorStore`
  (derived, rebuildable cosine index); rule-based `planner`; trust-aware explainable
  `ranking`; `SemanticQueryEngine` hybrid retrieval. Exit met: hybrid recall@5 = 1.0 vs
  vector-only baseline 0.0; search p95 ≈ 58ms at 1k entities (ADR-004).
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
### INIT-006 — Phase 5 Agent Runtime
- **Status:** Proposed, blocked by Phase 4 gate approval.
- Context assembly · tool invocation · memory access · agent lifecycle (`04-roadmap.md`).
  First module to consume Memory + Graph + Query + Trust end-to-end on behalf of an agent.
- Do not start before Phase 4 passes its gate (`99` §4, `04-roadmap.md`).

## Backlog (not scheduled)
- Phases 5–7 per `04-roadmap.md`.
- Learned `Embedder`, ANN/pgvector `VectorStore`, Postgres / native-graph adapters
  (future ADRs) when retrieval quality or horizontal scale demands it.
