# Semantic Control Plane (SCP)

A **Cognitive Control Layer for AI Systems** — the infrastructure beneath agents and
applications that owns memory, knowledge, trust, and governance ("Kubernetes for AI
cognition").

The authoritative design and rules live in [`project-memory-bank/`](project-memory-bank/README.md).
Read `99-development-rules.md` and `03-current-state.md` first.

## Status
Phases 1–3 complete (Phase Gates approved): **Memory Core**, **Knowledge Graph**, and
**Semantic Query Engine** (hybrid vector + graph retrieval that beats the vector-only
baseline on a labeled fixture). Phase 4 **Trust Engine** implemented (explainable trust
scoring, confidence model, source registry, verification policy, contradiction detection)
— awaiting its Phase Gate; it replaces the 0.5 confidence placeholder via additive
injection. Trust metadata is first-class on every memory, entity, and relationship. See
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
- `scp/trust/` — Trust Engine (source registry, confidence model, scoring, verification, contradiction, engine).
- `tests/` — unit + integration tests (mirrors source paths).
- `project-memory-bank/` — source of truth: vision, architecture, roadmap, ADRs, rules.
