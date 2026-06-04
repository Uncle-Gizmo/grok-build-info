# GROK_Build governance audit — AndroidApp (2026-06-04)

**Scope:** Evaluation of automatically created / maintained markdown that governs AI agent behavior in GROK_Build projects.  
**Example project:** `C:\AI\GROK_Build\AndroidApp`  
**Status:** Findings addressed in platform templates and generators (see changelog at end).

---

## Governance file map

| File | Role | Origin |
|------|------|--------|
| `AGENTS.md` | Primary agent guide | `templates/AGENTS.md` + **AI fill** via `.grok/AGENT-SETUP-TASKS.md` |
| `PROJECT_MEMORY.md` | Living truth (stage, decisions) | Template + sessions; **roadmap source of truth** |
| `.grok/PATH-CHECK.md` | Path/UNC/Android safety | `setup_grok_build_project.py` |
| `.grok/AGENT-SETUP-TASKS.md` | Post-setup AI work list | Written on new project setup |
| `docs/LATEST_WAYPOINT.txt` | Pointer to latest handoff | `create_waypoint.py` (shutdown) |
| `docs/WAYPOINT_*.md` | Session handoff (delta, not full memory paste) | Shutdown |
| `docs/TLDR_*`, `RESTART_CHECKLIST_*` | Optional quick resume | Shutdown |
| `docs/build-notes/README.md` | Explains log `.txt` files are not instructions | New project setup |

**Not governance:** `docs/*.txt` build logs, one-off migration notes (project-specific; do not add on generic setup).

---

## Original findings (AndroidApp)

### Worked well

- Architecture tree in `AGENTS.md` matched the repo.
- Pure-Kotlin engine rule and Gradle verify commands were correct.
- `.grok/PATH-CHECK.md` was concise and high-signal.
- `PROJECT_MEMORY.md` structure was appropriate.

### Problems found

1. **Stale roadmap** — `AGENTS.md` marked Stage 2 *Current* while memory said *Done*.
2. **Conflicting resume orders** — ON-SESSION-START, TLDR, and RESTART_CHECKLIST disagreed.
3. **Token waste** — Waypoints embedded ~2500 chars of `PROJECT_MEMORY.md`.
4. **Contradictory git lines** — `(git unavailable)` in table vs `Git: committed` in close summary.
5. **Template drift** — Missing texttasks, additional guidance, unified resume section.
6. **Android tribal knowledge** only in `docs/*.txt`, not in agent contract.

---

## Platform changes applied (2026-06-04)

| Area | Change |
|------|--------|
| **Setup** | Writes `.grok/AGENT-SETUP-TASKS.md`; creates `docs/build-notes/`; generic `docs/` + platform tooling sections in `AGENTS.md` template |
| **AGENTS fill** | Agent must complete setup tasks after *run setup routine* (documented in `NEW-PROJECT-SETUP.md`) |
| **Roadmap sync** | `<!-- SYNC:ROADMAP:START/END -->` in `AGENTS.md` and `PROJECT_MEMORY.md`; `governance_sync.py` updates AGENTS from memory on shutdown |
| **Waypoints** | Excerpt = Current focus + Stage/roadmap + Blockers only; link to full memory |
| **Git** | Distinguishes *not a repository* vs *git not on PATH*; checklist omits git when N/A |
| **Resume** | Single canonical order in `AGENTS.md` + `ON-SESSION-START.md` |

---

## Design decisions

### Why AI fills `AGENTS.md` (not only human)

Manual fill did not scale; agents already scan the tree on first session. Setup now drops a **mandatory task file** (`.grok/AGENT-SETUP-TASKS.md`) so the next agent run fills overview, commands, and memory—then marks tasks complete.

### Why not per-project `ANDROID_CLI.md` on every new project

Android-only content belongs in **filled** `AGENTS.md` sections when `PATH-CHECK` reports an Android Gradle project. Generic template includes a **Platform tooling** section with pointers to `.grok/PATH-CHECK.md` and `docs/build-notes/` for all projects.

### Why migration archive is not in setup

`FOLDER-MIGRATION.md`-style docs are one-time, project-specific events. Generic setup does not create archive folders; projects may add `docs/archive/` manually when needed.

---

## Canonical resume order (all projects)

1. `docs/LATEST_WAYPOINT.txt` → full `docs/WAYPOINT_*.md`
2. `PROJECT_MEMORY.md`
3. `GROK_Build-Housekeeping/REGISTRY.md` — only for platform/cross-project work

Skip TLDR / RESTART_CHECKLIST unless the user asks for a quick resume.

---

## Related files

- [NEW-PROJECT-SETUP.md](NEW-PROJECT-SETUP.md)
- [templates/AGENTS.md](templates/AGENTS.md)
- [scripts/governance_sync.py](scripts/governance_sync.py)
- [scripts/setup_grok_build_project.py](scripts/setup_grok_build_project.py)