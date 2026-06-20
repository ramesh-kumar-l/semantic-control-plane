# 03 — Current State

> This file is the canonical **implementation-status** record for SCP.

_Last updated: 2026-06-20_

## Repository
- `project-memory-bank/` (source of truth) + `adr/` (ADR-000..003).
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

## Verification (latest session — Phase 2)
- `ruff check` + `ruff format --check`: clean.
- `mypy --strict scp`: clean (21 files).
- `pytest`: **65 passed** (32 memory + 33 graph: models, traversal, both backends, service).
- Performance (SQLite, 500-node chain): add_entity p95 ≈ 0.4ms, add_relationship
  p95 ≈ 1.4ms, traverse depth-10 p95 ≈ 4.7ms, find_path (60 hops) p95 ≈ 25.8ms —
  all well under the 150ms NFR.
- Phase 1 verification still green (store/get/query p95 ≤ 7ms).

## Stack
- Python 3.12+ (dev/test ran on 3.14), async-first, `pydantic` v2, `aiosqlite`.
  Tooling: `ruff`, `mypy --strict`, `pytest` + `pytest-asyncio`. See ADR-001.

## Phase Status
- **Phase 0 (Memory Bank Bootstrap): Complete.**
- **Phase 1 (Memory Core): Complete (Phase Gate approved).**
  Storage + retrieval + lifecycle + consolidation + compression done, trust built in.
- **Phase 2 (Knowledge Graph): Implemented, awaiting Phase Gate approval.**
  Entities + relationships + graph storage + traversal + queries done, trust built in.
- All later phases: Not started.

## Known Constraints / Deferred
- Compression is deterministic truncation; semantic (LLM) summarisation deferred.
- Content is a string payload; structured payloads deferred.
- Graph properties are free-form `dict[str, str]`; typed property schemas deferred.
- Traversal is application-side BFS over the `neighbors` primitive; native-graph /
  recursive-CTE adapters are a future ADR behind the same `GraphStore` port (ADR-003).
- Vector/semantic retrieval over memory + graph is owned by Phase 3 (separate ADR).
  Postgres adapters are a future ADR behind the same ports when multi-node scale is needed.
- Real trust *scoring* is Phase 4; confidence currently defaults to 0.5 placeholder.
