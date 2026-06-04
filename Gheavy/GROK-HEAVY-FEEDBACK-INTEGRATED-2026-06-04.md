# Grok Heavy feedback — integrated (2026-06-04)

Observations from Grok Heavy on GROK_Build new-project initialization, and what was integrated into the platform.

**Public review pack:** [Gheavy/GROK-HEAVY-REVIEW-GOVERNANCE-2026-06-04.md](https://github.com/Uncle-Gizmo/grok-build-info/blob/main/Gheavy/GROK-HEAVY-REVIEW-GOVERNANCE-2026-06-04.md)

---

## Feedback summary

| Idea | Integration |
|------|-------------|
| Full stack generality | `AGENT-SETUP-TASKS.md` principles + signal table; generic `Platform tooling` in `templates/AGENTS.md`; `detect_stack_signals()` in `governance_sync.py` |
| Skills scan on setup | New Step 2: scan `~/.grok/skills` + `.grok/skills`; write `.grok/skills-discovered.md` |
| Suggest ≤3 skills only | Optional subsection; user approval required; no auto-create |
| One-line waypoint context | `SESSION_CONTEXT_LINE` in waypoint template + `session_context_one_line()` |
| Roadmap validation on start | `validate_roadmap_sync()` + `validate_session_start.py` (`--fix-roadmap`) |
| AI-fill over codegen | Reinforced in setup tasks and `NEW-PROJECT-SETUP.md` (unchanged policy) |

---

## Files changed

- `templates/AGENT-SETUP-TASKS.md` — stack-agnostic steps + skills
- `templates/skills-discovered.stub.md` — new
- `templates/AGENTS.md` — generic platform tooling + skills link
- `templates/waypoints/WAYPOINT_TEMPLATE.md` — context one-liner
- `scripts/governance_sync.py` — validate, stack signals, skills listing, context line
- `scripts/validate_session_start.py` — new CLI for agents
- `scripts/setup_grok_build_project.py` — `.grok/skills/`, skills-discovered stub
- `ON-SESSION-START.md` — roadmap check + setup pending
- `grok-skills-system/scripts/create_waypoint.py` — context line in waypoints

---

## Agent commands

```powershell
# Session start (per project)
python "C:\AI\GROK_Build\GROK_Build-Housekeeping\scripts\validate_session_start.py" --root "C:\AI\GROK_Build\<Project>"

# Fix roadmap drift from memory
python "C:\AI\GROK_Build\GROK_Build-Housekeeping\scripts\validate_session_start.py" --root "C:\AI\GROK_Build\<Project>" --fix-roadmap
```

---

## Not automated (by design)

- **Skill creation/install** — suggestions only; user must approve.
- **Stack-specific codegen in setup script** — remains AI fill via checklist so any repo shape works.
- **TUI wiring** — skills bootstrap in grok-skills-system remains a separate step per `NEW-PROJECT-SETUP.md`.