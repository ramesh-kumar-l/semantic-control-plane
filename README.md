# Semantic Control Plane (SCP)

A **Cognitive Control Layer for AI Systems** — the infrastructure beneath agents and
applications that owns memory, knowledge, trust, and governance ("Kubernetes for AI
cognition").

The authoritative design and rules live in [`project-memory-bank/`](project-memory-bank/README.md).
Read `99-development-rules.md` and `03-current-state.md` first.

## Status
Phase 1 **Memory Core** and Phase 2 **Knowledge Graph** complete (Phase Gates approved).
Phase 3 **Semantic Query Engine** implemented (hybrid vector + graph retrieval,
trust-aware ranking, query planning) — awaiting its Phase Gate; hybrid retrieval beats
the vector-only baseline on a labeled fixture. Trust metadata is first-class on every
memory, entity, and relationship, and informs ranking. See
`project-memory-bank/04-roadmap.md`.

## Quickstart
```bash
python -m venv .venv
.venv/Scripts/python -m pip install -e ".[dev]"   # Windows
# source .venv/bin/activate && pip install -e ".[dev]"  # POSIX

ruff check scp tests
mypy scp
pytest
```

## Usage
```python
import asyncio
from scp.memory import MemoryCore, SqliteStore, MemoryType, SourceType

async def main():
    core = MemoryCore(await SqliteStore.connect("memories.db"))
    record = await core.store(
        "The Eiffel Tower is in Paris.",
        memory_type=MemoryType.SEMANTIC,
        source_type=SourceType.USER,
    )
    print(await core.get(record.id))
    await core.close()

asyncio.run(main())
```

## Layout
- `scp/memory/` — Memory Core (models, port, backends, lifecycle, service).
- `scp/graph/` — Knowledge Graph (models, port, backends, traversal, service).
- `scp/query/` — Semantic Query Engine (embeddings, vector store, ranking, planner, engine).
- `tests/` — unit + integration tests (mirrors source paths).
- `project-memory-bank/` — source of truth: vision, architecture, roadmap, ADRs, rules.
