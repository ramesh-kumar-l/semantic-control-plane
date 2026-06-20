# 03 — Current State

> This file is the canonical **implementation-status** record for SCP.

_Last updated: 2026-06-20_

## Repository
- `project-memory-bank/` (source of truth) + `adr/` (ADR-000..006).
- Python project scaffolded: `pyproject.toml`, `.gitignore`, `scp/` package, `tests/`.
- `.venv/` local dev environment (git-ignored).

## What Exists Now (code)
- **Phase 1 Memory Core — implemented** under `scp/memory/`:
  - `models.py` — `MemoryRecord` with inseparable `TrustMetadata` + `TemporalContext`.
  - `enums.py` — memory type, source type, verification status, lifecycle state, provenance op.
  - `errors.py` — typed exceptions (not-found, duplicate, invalid-transition, consolidation).
  - `store.py` — `MemoryStore` **port** + `MemoryQuery`.
  - `backends/` — `InMemoryStore` (dev/test) + `SqliteStore` (durable, aiosqlite). See ADR-002.
  - `lifecycle.py` — pure transitions, consolidation, compression.
  - `core.py` — `MemoryCore` service (store/get/query/consolidate/compress/archive/expire).
- **Phase 2 Knowledge Graph — implemented** under `scp/graph/`:
  - `models.py` — `Entity` + `Relationship`, each carrying the **reused** Phase 1
    `TrustMetadata` + `TemporalContext` (trust travels with facts; ADR-003).
  - `enums.py` — entity type, relation type, traversal direction.
  - `errors.py` — typed exceptions (entity/relationship not-found, duplicates,
    dangling-relationship, invalid-traversal).
  - `store.py` — `GraphStore` **port** + `EntityQuery`/`RelationshipQuery` + `neighbors`.
  - `backends/` — `InMemoryGraphStore` + `SqliteGraphStore` (aiosqlite). See ADR-003.
  - `traversal.py` — pure BFS (`breadth_first`, `shortest_path`) over an adjacency fn.
  - `core.py` — `KnowledgeGraph` service (entity/relationship CRUD, queries, neighbors,
    traverse, find_path; `delete_entity` cascades incident relationships).
- **Phase 3 Semantic Query Engine — implemented** under `scp/query/`:
  - `embeddings.py` — `Embedder` port + deterministic offline `HashingEmbedder`
    (blake2b feature hashing → L2-normalized vector) + `cosine`. See ADR-004.
  - `vector_store.py` — `VectorStore` **port** + `VectorMatch` (a *derived*, rebuildable
    similarity index; Memory/Graph stay source of truth).
  - `backends/in_memory.py` — `InMemoryVectorStore` (brute-force cosine top-k).
  - `enums.py` — `RetrievalStrategy` (vector_only / graph_only / hybrid).
  - `errors.py` — `SemanticQueryError`, `EmptyQueryError`.
  - `models.py` — `RankingWeights`, `QueryPlan`, `ScoredResult` (explainable), `RetrievalResult`.
  - `ranking.py` — trust-aware, explainable fusion (semantic + graph proximity + trust);
    `trust_score` maps confidence × documented verification factor.
  - `planner.py` — rule-based strategy selection + seed sizing.
  - `engine.py` — `SemanticQueryEngine` service (index_entity/reindex/remove_entity/search);
    vector seeds expanded via Phase 2 `traverse`, fused and ranked. Uses only the
    `KnowledgeGraph` public API (no boundary crossing).
- **Phase 4 Trust Engine — implemented** under `scp/trust/` (pure, synchronous, no I/O,
  no storage access; deterministic + explainable). See ADR-005:
  - `enums.py` — `TrustSignal` (verification-driving signals) + `ContradictionKind`.
  - `errors.py` — `TrustEngineError`, `InvalidVerificationTransitionError`.
  - `models.py` — `TrustBearing` protocol; `TrustWeights`, `TrustComponents`,
    `TrustAssessment` (explainable, reproducible); `Claim`, `Contradiction`.
  - `registry.py` — `SourceRegistry` (per-type default reliability + per-identifier overrides).
  - `confidence.py` — `ConfidenceModel` (real per-source initial confidence; replaces 0.5).
  - `scoring.py` — pure math: `recency_factor`, `verification_factor`, and `assess`
    (`base = weighted blend of reliability/confidence/recency`, gated by verification).
  - `verification.py` — `VerificationPolicy` signal-driven state machine (CONTRADICTED→VERIFIED
    forbidden without REOPEN first).
  - `contradiction.py` — `ContradictionDetector` (value-mismatch / polarity-conflict).
  - `engine.py` — `TrustEngine` service (initial_confidence / assess / verify /
    detect_contradictions / reconcile).
- **Phase 1/2 extension (additive):** `MemoryCore` and `KnowledgeGraph` gained an optional
  `confidence_model` callable; default `None` preserves prior behavior. Wiring it to
  `TrustEngine.initial_confidence` replaces the 0.5 placeholder in the live path with no
  Phase 1/2 → Phase 4 import (dependency inversion). Phase 3 ranking is unchanged.
- **Phase 5 Agent Runtime — implemented** under `scp/agent/`. See ADR-006:
  - `enums.py` — `AgentStatus` (IDLE/RUNNING/PAUSED/STOPPED/FAILED) + `ToolStatus`.
  - `errors.py` — typed exceptions (not-found, not-running, tool errors, lifecycle, max-steps).
  - `models.py` — `AgentConfig`, `AgentContext`, `ContextItem`, `ToolCall`, `ToolResult`,
    `AgentStep`, `AgentState`.
  - `tools.py` — `Tool` structural protocol + `ToolRegistry` (non-fatal error capture).
  - `context.py` — `ContextAssembler` (semantic search + per-result trust assessment
    via `SemanticQueryEngine`, `TrustEngine`, `KnowledgeGraph`; returns `AgentContext`).
  - `lifecycle.py` — `AgentLifecycle` (in-memory; enforces the documented transition graph).
  - `runtime.py` — `AgentRuntime` service (create/run_step/pause/resume/stop; persists
    each step to `MemoryCore` as `EPISODIC` memory; all access via public APIs only).

## Verification (latest session — Phase 5)
- `ruff check` + `ruff format --check`: clean (all agent files).
- `mypy --strict scp/agent`: clean (8 source files).
- `pytest`: **155 passed** (32 memory + 33 graph + 25 query + 30 trust + 35 agent:
  lifecycle, tools, context, runtime, and integration tests).
- **Exit criterion met:** agent reads Knowledge Graph entity in context; agent writes
  EPISODIC memory after each step; context items carry real TrustEngine scores (not 0.5);
  full lifecycle IDLE→RUNNING→PAUSED→RUNNING→STOPPED exercised; tool invocation captured;
  max_steps exceeded → FAILED; no regressions in Phases 1–4.
- Phase 3 exit (hybrid recall@5 = 1.0 vs baseline 0.0) still green.
- Phase 4 exit (explainable/reproducible trust scores; 0.5 placeholder replaced) still green.

## Stack
- Python 3.12+ (dev/test ran on 3.14), async-first, `pydantic` v2, `aiosqlite`.
  Tooling: `ruff`, `mypy --strict`, `pytest` + `pytest-asyncio`. See ADR-001.

## Phase Status
- **Phase 0 (Memory Bank Bootstrap): Complete.**
- **Phase 1 (Memory Core): Complete (Phase Gate approved).**
  Storage + retrieval + lifecycle + consolidation + compression done, trust built in.
- **Phase 2 (Knowledge Graph): Complete (Phase Gate approved).**
  Entities + relationships + graph storage + traversal + queries done, trust built in.
- **Phase 3 (Semantic Query Engine): Complete (Phase Gate approved).**
  Hybrid vector + graph retrieval, trust-aware ranking, query planning done; hybrid
  beats the vector-only baseline on the fixture set (ADR-004).
- **Phase 4 (Trust Engine): Complete (Phase Gate approved).**
  Explainable trust scoring, confidence model, source registry, verification policy,
  contradiction detection; replaces the 0.5 confidence placeholder via additive
  injection (ADR-005). Approved when user directed Phase 5 continuation.
- **Phase 5 (Agent Runtime): Implemented, awaiting Phase Gate approval.**
  Context assembly, tool invocation, memory access, agent lifecycle (ADR-006).
  Every step persists to EPISODIC memory; trust scores are real + explainable.
- All later phases: Not started.

## Known Constraints / Deferred
- Compression is deterministic truncation; semantic (LLM) summarisation deferred.
- Content is a string payload; structured payloads deferred.
- Graph properties are free-form `dict[str, str]`; typed property schemas deferred.
- Traversal is application-side BFS; native-graph / recursive-CTE adapters future ADR.
- Embeddings are deterministic lexical feature-hashing; model-based `Embedder` future ADR.
- Vector index is in-memory brute-force; ANN / pgvector durable adapter future ADR.
- Query planning is rule-based; cost-based planning future follow-up.
- Postgres adapters future ADR when multi-node scale is needed.
- Trust weights and contradiction detection are documented heuristics (not learned).
- Agent lifecycle is in-memory only; a durable `AgentStore` port is a future ADR (ADR-006).
- Tool timeout enforcement is not yet implemented (follow-up from ADR-006).
