# 03 — Current State

> This file is the canonical **implementation-status** record for SCP.

_Last updated: 2026-06-20_

## Repository
- `project-memory-bank/` (source of truth) + `adr/` (ADR-000..002).
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

## Verification (this session)
- `ruff check` + `ruff format --check`: clean.
- `mypy --strict scp`: clean (11 files).
- `pytest`: **32 passed** (models, lifecycle, both backends, service).
- Performance (SQLite, durable commits): store p95 ≈ 6.3ms, get p95 ≈ 7.0ms,
  query p95 ≈ 2.3ms — well under the 150ms NFR target.

## Stack
- Python 3.12+ (dev/test ran on 3.14), async-first, `pydantic` v2, `aiosqlite`.
  Tooling: `ruff`, `mypy --strict`, `pytest` + `pytest-asyncio`. See ADR-001.

## Phase Status
- **Phase 0 (Memory Bank Bootstrap): Complete.**
- **Phase 1 (Memory Core): Implemented, awaiting Phase Gate approval.**
  Storage + retrieval + lifecycle + consolidation + compression done, trust built in.
- All later phases: Not started.

## Known Constraints / Deferred
- Compression is deterministic truncation; semantic (LLM) summarisation deferred.
- Content is a string payload; structured payloads deferred.
- Vector/graph storage owned by Phases 2–3 (separate ADRs). Postgres adapter is a
  future ADR behind the same `MemoryStore` port when multi-node scale is needed.
- Real trust *scoring* is Phase 4; confidence currently defaults to 0.5 placeholder.
