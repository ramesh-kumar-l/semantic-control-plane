# 26 — Active Initiatives

> This file is the canonical **active-context** record for SCP.

_Last updated: 2026-06-20_

## Active
### INIT-006 — Phase 5 Agent Runtime
- **Status:** Implemented — **awaiting Phase Gate approval.**
- **Delivered:** `scp/agent/` package; `ContextAssembler` (semantic search + trust assessment
  per result via SemanticQueryEngine + TrustEngine + KnowledgeGraph); `ToolRegistry` (structural
  `Tool` protocol; non-fatal error capture); `AgentLifecycle` (enforces documented transition
  graph: IDLE→RUNNING, RUNNING→{PAUSED,STOPPED,FAILED}, PAUSED→{RUNNING,STOPPED});
  `AgentRuntime` service (create/run_step/pause/resume/stop); each step persists to MemoryCore
  EPISODIC memory for auditability.
- **Trust integration:** context items carry full `TrustEngine.assess()` scores (not 0.5
  placeholder) with human-readable explanation retained in each `ContextItem`.
- **Exit met:** agent reads Knowledge Graph + writes MemoryCore; full lifecycle exercised;
  context items carry real trust scores; tool invocation captured; 35 agent tests, 155 total.
- **Decision recorded:** ADR-006.

## Completed
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
### INIT-007 — Phase 6 Agent Flight Recorder
- **Status:** Proposed, blocked by Phase 5 gate approval.
- Replay · Debugging · Traceability · Root Cause Analysis.
- Every agent decision reconstructable from recorded evidence.
- Do not start before Phase 5 passes its gate (`99` §4).

## Backlog (not scheduled)
- Phase 7 Governance Layer per `04-roadmap.md`.
- Learned `Embedder`, ANN/pgvector `VectorStore`, Postgres / native-graph adapters
  (future ADRs) when retrieval quality or horizontal scale demands it.
- Tool timeout enforcement (follow-up from ADR-006).
- Durable `AgentStore` port + adapter (follow-up from ADR-006).
