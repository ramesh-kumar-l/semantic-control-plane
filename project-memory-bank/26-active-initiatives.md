# 26 — Active Initiatives

_Last updated: 2026-06-20_

## Active
### INIT-001 — Bootstrap Memory Bank
- **Status:** Complete.
- **Outcome:** Created `project-memory-bank/` (strategic, execution, domain, governance files + `adr/` + README). Established Python as the implementation stack. Corrected Phase 1 status to "Not started."

## Next Candidate (awaiting approval)
### INIT-002 — Phase 1 Memory Core: Plan & Scaffold
- **Status:** Proposed, not started.
- **Scope:** Design Phase 1 Memory Core; scaffold the Python project (`pyproject.toml`, package layout, tooling: ruff/mypy/pytest); implement Memory Core storage + retrieval as the first vertical slice with trust metadata built in.
- **Blocked by:** explicit user approval to begin Phase 1.
- **Open decisions:** storage backend selection (vector/graph/KV) — to be resolved via ADR during design.

## Backlog (not scheduled)
- Phases 2–7 per `04-roadmap.md`. Do not start before Phase 1 passes its phase gate.
