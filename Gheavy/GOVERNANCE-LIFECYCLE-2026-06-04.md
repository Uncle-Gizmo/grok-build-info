# Day-to-day lifecycle governance (2026-06-04)

Grok Heavy suggestions for runtime governance beyond initial setup — integrated into GROK_Build templates and validators.

---

## Added: `PROJECT_STAGES.md`

| Aspect | Detail |
|--------|--------|
| **Created by** | `setup_grok_build_project.py` from `templates/PROJECT_STAGES.md` |
| **Refined by** | Agent during `.grok/AGENT-SETUP-TASKS.md` Step 5 from roadmap + repo evidence |
| **Contains** | Current stage, session checkpoint, phase table, per-phase success criteria, behaviour guidelines |
| **Resume order** | After `PROJECT_MEMORY.md`, before `REGISTRY.md` |

---

## Optional safety: lifecycle vs maturity

`validate_lifecycle_checkpoint()` in `governance_sync.py`:

- If **Stage** is early (Planning, Initiation, …) but multiple maturity signals exist (old memory, waypoints, roadmap Done rows, setup COMPLETE), emits **WARN**.
- Primary control remains the **Session checkpoint** checklist in `PROJECT_STAGES.md`.
- Use `--strict-lifecycle` on `validate_session_start.py` to fail exit code when mismatched.

```powershell
python "C:\AI\GROK_Build\GROK_Build-Housekeeping\scripts\validate_session_start.py" --root "<PROJECT>"
python "...\validate_session_start.py" --root "<PROJECT>" --strict-lifecycle
```

---

## DECISION_LOG — evaluation

**Recommendation: no default `DECISION_LOG.md` on new projects.**

| Approach | Verdict |
|----------|---------|
| `PROJECT_MEMORY.md` → ## Key decisions | **Default** — living memory, low duplication |
| Separate `DECISION_LOG.md` / ADR folder | **Opt-in** only when user wants formal ADR history |

Rationale: two logs drift; agents already maintain memory on shutdown. For heavy ADR needs, user can add `docs/decisions/` manually.

---

## Other lightweight bootstrap ideas (optional, not auto-added)

| Mechanism | When useful |
|-----------|-------------|
| `texttasks/<name>/task.md` | Already in template — non-trivial multi-step work |
| `.grok/skills-discovered.md` | Setup — skill relevance |
| `docs/build-notes/` | Log snippets, not instructions |
| `CHANGELOG.md` | User-facing releases (agent updates at Release prep) |
| `docs/decisions/` | User-requested ADRs only |

**Not recommended by default:** duplicate roadmap files, per-stack `*_CLI.md`, auto `DECISION_LOG.md`.

---

## Canonical resume order (updated)

1. Waypoint (`docs/LATEST_WAYPOINT.txt`)
2. `PROJECT_MEMORY.md`
3. `PROJECT_STAGES.md`
4. `REGISTRY.md` (platform only)

---

## Related

- [GOVERNANCE-AUDIT-ANDROIDAPP-2026-06-04.md](https://github.com/Uncle-Gizmo/grok-build-info/blob/main/Gheavy/GOVERNANCE-AUDIT-ANDROIDAPP-2026-06-04.md)
- [GROK-HEAVY-FEEDBACK-INTEGRATED-2026-06-04.md](https://github.com/Uncle-Gizmo/grok-build-info/blob/main/Gheavy/GROK-HEAVY-FEEDBACK-INTEGRATED-2026-06-04.md)
- NEW-PROJECT-SETUP.md (private GROK_Build-Housekeeping — use review pack for public context)