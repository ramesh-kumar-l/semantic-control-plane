# 04 — Roadmap

> Work strictly phase-by-phase. Never implement future-phase features. Each phase ends with the **Phase Gate Protocol**.

## Phase Status Overview
| Phase | Name | Status |
|---|---|---|
| 0 | Memory Bank Bootstrap | **Complete** |
| 1 | Memory Core | **Complete — Phase Gate approved** |
| 2 | Knowledge Graph | **Implemented — awaiting Phase Gate approval** |
| 3 | Semantic Query Engine | Not started |
| 4 | Trust Engine | Not started |
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

## Phase 4 — Trust Engine
Trust Scores · Confidence Models · Source Tracking · Verification · Contradiction Detection (`14-trust-model.md`).

## Phase 5 — Agent Runtime
Context Assembly · Tool Invocation · Memory Access · Agent Lifecycle.

## Phase 6 — Agent Flight Recorder
Replay · Debugging · Traceability · Root Cause Analysis.

## Phase 7 — Governance Layer
Policies · Compliance · Controls · Auditing.

## Phase Gate Protocol
At the end of every phase, STOP and produce:
**What Was Implemented · Design Review · Risks · Technical Debt · Test Results · Documentation Updates · Recommended Next Steps**, then ask **"Approve Phase Completion?"** and wait for approval before continuing.
