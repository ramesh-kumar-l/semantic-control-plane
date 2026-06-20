# 26 — Active Initiatives

> This file is the canonical **active-context** record for SCP.

_Last updated: 2026-06-20 (Phase 6)_

## Active
### INIT-007 — Phase 6 Agent Flight Recorder
- **Status:** Implemented — **awaiting Phase Gate approval.**
- **Delivered:** `scp/recorder/` package; `ReplayEngine` (ordered step replay, range slicing);
  `TraceEngine` (entity → TraceAppearance → Trace; cross-agent and scoped); `DebugEngine`
  (root-cause report: trust-sorted context, tool outcomes, trust signals, related steps);
  `FlightRecorder` service (single entry point: record/replay/trace/root_cause);
  `RecordStore` port + `InMemoryRecordStore` backend.
- **Decoupling:** caller passes `AgentStep` to `recorder.record()` — recorder never touches
  `AgentRuntime` internals. Phase 5 code unchanged.
- **Exit met:** ordered replay, entity tracing, root-cause analysis implemented; 29 recorder
  tests (replay ×8, trace ×8, debug ×7, integration ×5); 184 total; 0 regressions.
- **Decision recorded:** ADR-007.

## Completed
### INIT-006 — Phase 5 Agent Runtime
- **Status:** Complete (Phase Gate approved by user directing Phase 6).
- `scp/agent/` package; `ContextAssembler` + `ToolRegistry` + `AgentLifecycle` + `AgentRuntime`;
  EPISODIC memory persistence per step; real TrustEngine scores in every ContextItem. ADR-006.
### INIT-005 — Phase 4 Trust Engine
- **Status:** Complete (Phase Gate approved by user proceeding to Phase 5).
- `scp/trust/` package; `SourceRegistry`; `ConfidenceModel` (replaces 0.5); explainable
  weighted-blend scoring; `VerificationPolicy` state machine; `ContradictionDetector`;
  `TrustEngine` service (pure, synchronous, no I/O). 0.5 placeholder replaced additively
  via `confidence_model` callable on `MemoryCore`/`KnowledgeGraph`. ADR-005.
### INIT-004 — Phase 3 Semantic Query Engine
- **Status:** Complete (Phase Gate approved). `scp/query/` package; `Embedder` port +
  `HashingEmbedder`; `VectorStore` port + `InMemoryVectorStore`; rule-based planner;
  trust-aware explainable ranking; `SemanticQueryEngine` hybrid retrieval. Exit met:
  hybrid recall@5 = 1.0 vs vector-only baseline 0.0. ADR-004.
### INIT-003 — Phase 2 Knowledge Graph
- **Status:** Complete (Phase Gate approved). `GraphStore` port + adapters; `Entity` +
  `Relationship` reusing Phase 1 trust/temporal; pure BFS traversal; `KnowledgeGraph`
  service with cascade-delete. ADR-003.
### INIT-002 — Phase 1 Memory Core: Scaffold & First Slice
- **Status:** Complete (Phase Gate approved). `MemoryStore` port + adapters; `MemoryCore`
  service; trust + temporal on every record. ADR-002.
### INIT-001 — Bootstrap Memory Bank
- **Status:** Complete. Established the memory bank + Python stack. ADR-001.

## Next Candidate (awaiting approval)
### INIT-008 — Phase 7 Governance Layer
- **Status:** Proposed, blocked by Phase 6 gate approval.
- Policies · Compliance · Controls · Auditing.
- Policy gates enforced on trust thresholds + verification status; full audit trail.
- Do not start before Phase 6 passes its gate (`99` §4).

## Backlog (not scheduled)
- Learned `Embedder`, ANN/pgvector `VectorStore`, Postgres / native-graph adapters (future ADRs).
- Tool timeout enforcement (follow-up from ADR-006).
- Durable `AgentStore` port + adapter (follow-up from ADR-006).
- `SqliteRecordStore` durable backend (follow-up from ADR-007).
- Auto-record hook on `AgentRuntime` (follow-up from ADR-007).
