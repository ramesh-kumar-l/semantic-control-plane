# 03 — Current State

> This file is the canonical **implementation-status** record for SCP.

_Last updated: 2026-06-20_

## Repository
- `project-memory-bank/` (source of truth) + `adr/` (ADR-000..004).
- Python project scaffolded: `pyproject.toml`, `.gitignore`, `scp/` package, `tests/`.
- `.venv/` local dev environment (git-ignored).

## What Exists Now (code)
- **Phase 1 Memory Core — first vertical slice implemented** under `scp/memory/`:
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

## Verification (latest session — Phase 3)
- `ruff check` + `ruff format --check`: clean.
- `mypy --strict scp`: clean (32 files).
- `pytest`: **90 passed** (32 memory + 33 graph + 25 query: embeddings, vector store,
  planner, ranking, engine, and the hybrid-vs-baseline benchmark).
- **Exit criterion met:** hybrid retrieval recall@5 = 1.0 vs vector-only baseline 0.0 on
  the labeled fixture (`tests/query/test_benchmark.py`); gold answers are graph-adjacent
  to lexical seeds but share no query words.
- Performance (1000 entities, hybrid search depth-2): reindex ≈ 50ms;
  search p50 ≈ 39ms, p95 ≈ 58ms — under the 150ms NFR.
- Phase 1 + Phase 2 verification still green.

## Stack
- Python 3.12+ (dev/test ran on 3.14), async-first, `pydantic` v2, `aiosqlite`.
  Tooling: `ruff`, `mypy --strict`, `pytest` + `pytest-asyncio`. See ADR-001.

## Phase Status
- **Phase 0 (Memory Bank Bootstrap): Complete.**
- **Phase 1 (Memory Core): Complete (Phase Gate approved).**
  Storage + retrieval + lifecycle + consolidation + compression done, trust built in.
- **Phase 2 (Knowledge Graph): Complete (Phase Gate approved).**
  Entities + relationships + graph storage + traversal + queries done, trust built in.
- **Phase 3 (Semantic Query Engine): Implemented, awaiting Phase Gate approval.**
  Hybrid vector + graph retrieval, trust-aware ranking, query planning done; hybrid
  beats the vector-only baseline on the fixture set (ADR-004).
- All later phases: Not started.

## Known Constraints / Deferred
- Compression is deterministic truncation; semantic (LLM) summarisation deferred.
- Content is a string payload; structured payloads deferred.
- Graph properties are free-form `dict[str, str]`; typed property schemas deferred.
- Traversal is application-side BFS over the `neighbors` primitive; native-graph /
  recursive-CTE adapters are a future ADR behind the same `GraphStore` port (ADR-003).
- Embeddings are deterministic lexical feature-hashing (bag-of-words), not learned;
  a model-based `Embedder` is a future ADR behind the same port (ADR-004).
- The vector index is in-memory brute-force cosine and rebuildable from the graph;
  an ANN / pgvector / durable `VectorStore` adapter is a future ADR behind the same port.
- Query planning is rule-based; cost-based planning is a documented follow-up.
- Postgres adapters are a future ADR behind the same ports when multi-node scale is needed.
- Real trust *scoring* is Phase 4; confidence currently defaults to 0.5 placeholder, and
  Phase 3 ranking consumes it plus a documented verification factor (ADR-004).
