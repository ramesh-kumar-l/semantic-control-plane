# 04 — Roadmap

> Work strictly phase-by-phase. Never implement future-phase features. Each phase ends with the **Phase Gate Protocol**.

## Phase Status Overview
| Phase | Name | Status |
|---|---|---|
| 0 | Memory Bank Bootstrap | **Complete** |
| 1 | Memory Core | **Complete — Phase Gate approved** |
| 2 | Knowledge Graph | **Complete — Phase Gate approved** |
| 3 | Semantic Query Engine | **Complete — Phase Gate approved** |
| 4 | Trust Engine | **Implemented — awaiting Phase Gate approval** |
| 5 | Agent Runtime | Not started |
| 6 | Agent Flight Recorder | Not started |
| 7 | Governance Layer | Not started |

> **History:** The operating prompt originally described Phase 1 as "potentially complete" against an empty repo — that was false. Phase 1 was started greenfield and the first vertical slice (storage, retrieval, lifecycle, consolidation, compression, with trust built in) is now implemented and tested (`03-current-state.md`), pending the Phase Gate.

## Phase 1 — Memory Core
Capabilities: Memory Storage · Retrieval · Consolidation · Compression · Lifecycle Management.
Trust metadata (source, provenance, confidence, verification, temporal context) is built in from the start (`11-memory-model.md`).

## Phase 2 — Knowledge Graph
Entity Management · Relationship Management · Graph Storage · Graph Traversal · Graph Query Engine.
Implemented under `scp/graph/` behind a `GraphStore` port (ADR-003); entities and
relationships carry the same first-class trust primitives as memories. Traversal is
application-side BFS (`breadth_first`, `shortest_path`). Semantic/vector retrieval over
the graph is explicitly **out of scope** here — it belongs to Phase 3.

## Phase 3 — Semantic Query Engine
Hybrid Retrieval · Vector Search · Graph Search · Ranking · Query Planning.
Implemented under `scp/query/` (ADR-004): a deterministic offline `HashingEmbedder`
behind an `Embedder` port; an `InMemoryVectorStore` behind a `VectorStore` port (a
derived, rebuildable cosine index); hybrid retrieval = vector seeds expanded via Phase 2
`traverse`; trust-aware, explainable ranking (semantic + graph proximity + trust); and a
rule-based planner choosing vector_only / graph_only / hybrid. Exit met: hybrid beats the
vector-only baseline on a labeled fixture (recall@5 1.0 vs 0.0).

## Phase 4 — Trust Engine
Trust Scores · Confidence Models · Source Tracking · Verification · Contradiction Detection (`14-trust-model.md`).
Implemented under `scp/trust/` (ADR-005) as a pure, deterministic, synchronous engine over
the trust primitives every item already carries: a `SourceRegistry` (reliability weighting),
a `ConfidenceModel` (real per-source confidence — replaces the 0.5 placeholder), explainable
`scoring` (`base = weighted blend of reliability/confidence/recency`, gated by a verification
factor; every `TrustAssessment` is reconstructable), a signal-driven `VerificationPolicy`
state machine, and a `ContradictionDetector` with reliability-weighted reconciliation. The
0.5 placeholder is replaced in the live path via an **additive** optional `confidence_model`
callable injected into `MemoryCore`/`KnowledgeGraph` (no Phase 1/2 → Phase 4 dependency).
Exit met: scores explainable/reproducible; the placeholder is gone when the engine is wired.

## Phase 5 — Agent Runtime
Context Assembly · Tool Invocation · Memory Access · Agent Lifecycle.

## Phase 6 — Agent Flight Recorder
Replay · Debugging · Traceability · Root Cause Analysis.

## Phase 7 — Governance Layer
Policies · Compliance · Controls · Auditing.

## Phase Gate Protocol
At the end of every phase, STOP and produce:
**What Was Implemented · Design Review · Risks · Technical Debt · Test Results · Documentation Updates · Recommended Next Steps**, then ask **"Approve Phase Completion?"** and wait for approval before continuing.
