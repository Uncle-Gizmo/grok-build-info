# GROK_Build system skills catalog

**Date:** 2026-06-04  
**Source:** `C:\AI\GROK_Build\grok-skills-system\system\` (canonical catalog)  
**Count:** 13 skills  
**Audience:** Humans and Grok Heavy — public overview; implementation stays in the private GROK_Build tree.

---

## Summary table

| # | Skill | Category | Status | Purpose (one line) |
|---|--------|----------|--------|-------------------|
| 1 | bootstrap | meta | draft | First-run project setup — copy governance skills and folder structure from catalog |
| 2 | skill-validator | meta | draft | Quality gate for SKILL.md structure, scripts, and references |
| 3 | prompt-library | meta | draft | Versioned reusable prompts referenced by ID |
| 4 | system-diagnostics | meta | draft | Health checks for TUI, catalog integrity, environment |
| 5 | project-close | lifecycle | draft | Clean shutdown — validator, git, backup, waypoints |
| 6 | list-projects | lifecycle | active | List top-level folders under `C:\AI\GROK_Build` |
| 7 | grok-build-help | lifecycle | active | Platform routines — HELP.md, setup, shutdown, mirror |
| 8 | error-handler | lifecycle | draft | Graceful failure handling and recovery guidance |
| 9 | file-classification | governance | draft | Private vs public vs promotional file rules |
| 10 | backup-strategy | governance | active | Local archive + Google Drive mirror via Grok-Mirror |
| 11 | git-governance | governance | draft | Git init, branches, commits, GitHub hygiene |
| 12 | markdown-governance | documentation | draft | Where docs/logs live; git vs Drive sync rules |
| 13 | secrets-hygiene | security | draft | Credentials, .env discipline, secret scanning |

---

## By category

### Meta / orchestration

#### bootstrap
- **Description:** Project initialization orchestrator — ensures canonical governance skills, folder structure, and databases exist when entering a project.
- **Purpose:** Self-healing project entry; copies from trusted catalog instead of generating skills from scratch.
- **Path:** `grok-skills-system/system/bootstrap/SKILL.md`

#### skill-validator
- **Description:** Validates SKILL.md front matter, required sections, associated scripts, and structural health.
- **Purpose:** Quality gate for the skill ecosystem; run on bootstrap and project-close.
- **Path:** `grok-skills-system/system/skill-validator/SKILL.md`

#### prompt-library
- **Description:** Central, versioned collection of high-quality reusable prompts and fragments.
- **Purpose:** Consistency — other skills reference prompt IDs instead of reinventing text.
- **Path:** `grok-skills-system/system/prompt-library/SKILL.md`

#### system-diagnostics
- **Description:** Health checks for the GROK Build TUI, skills catalog, and environment sanity.
- **Purpose:** Detect configuration drift and corruption early.
- **Path:** `grok-skills-system/system/system-diagnostics/SKILL.md`

### Lifecycle

#### project-close
- **Description:** Clean, repeatable project shutdown — validator, git hygiene, backups, session waypoints, close log.
- **Purpose:** Turn “quit” into a deliberate handoff; resume via `docs/LATEST_WAYPOINT.txt`.
- **Script:** `grok-skills-system/scripts/project_close.py`
- **Path:** `grok-skills-system/system/project-close/SKILL.md`

#### list-projects
- **Description:** Lists top-level GROK_Build project folders (read-only inventory).
- **Purpose:** Navigation before opening a project or running bootstrap.
- **Script:** `grok-skills-system/scripts/list_projects.py`
- **Path:** `grok-skills-system/system/list-projects/SKILL.md`

#### grok-build-help
- **Description:** GROK_Build platform help when the user says *help*, forgot a routine, or needs setup/shutdown/mirror commands.
- **Purpose:** Index to `GROK_Build-Housekeeping/HELP.md` and related docs (not Grok CLI config).
- **Script:** `grok-skills-system/scripts/show_help.py`
- **Path:** `grok-skills-system/system/grok-build-help/SKILL.md`

#### error-handler
- **Description:** Centralized handling for unrecoverable or confusing errors with logging and recovery options.
- **Purpose:** Avoid leaving the user in a broken state; delegated to by other skills on failure.
- **Path:** `grok-skills-system/system/error-handler/SKILL.md`

### Governance

#### file-classification
- **Description:** Canonical locations and rules for private, public, promotional, and LLM-discussion files.
- **Purpose:** Foundation for backup-strategy and safe sharing boundaries.
- **Path:** `grok-skills-system/system/file-classification/SKILL.md`

#### backup-strategy
- **Description:** Configurable backup and mirroring — local archives and Google Drive via Grok-Mirror.
- **Purpose:** Data durability; invoked from project-close by default.
- **Script:** `grok-skills-system/scripts/backup_strategy.py`
- **Path:** `grok-skills-system/system/backup-strategy/SKILL.md`

#### git-governance
- **Description:** Git initialization, branching model, commit conventions, and GitHub integration hygiene.
- **Purpose:** Professional repo discipline across GROK_Build projects.
- **Path:** `grok-skills-system/system/git-governance/SKILL.md`

### Documentation

#### markdown-governance
- **Description:** Standardizes where Markdown content lives and what syncs to GitHub vs Google Drive.
- **Purpose:** Searchable, archivable knowledge layout across projects.
- **Path:** `grok-skills-system/system/markdown-governance/SKILL.md`

### Security

#### secrets-hygiene
- **Description:** Best practices for credentials, Windows Credential Manager, .env handling, and secret scanning.
- **Purpose:** Security by default; integrates with classification and backup rules.
- **Path:** `grok-skills-system/system/secrets-hygiene/SKILL.md`

---

## How skills relate (quick map)

```text
bootstrap → provisions catalog skills + structure
     ↓
project work → grok-build-help (routines) | list-projects (pick folder)
     ↓
project-close → skill-validator + backup-strategy + waypoints
     ↓
errors → error-handler
```

**Platform layer (separate repo folder):** `GROK_Build-Housekeeping` — `AGENTS.md`, setup routine, `REGISTRY.md`, not duplicated here.

---

## Machine-readable index

`grok-skills-system/manifest.json` mirrors this catalog for tooling. Per-project `.grok/skills-discovered.md` includes a **Platform catalog** table refreshed by:

```powershell
python "C:\AI\GROK_Build\GROK_Build-Housekeeping\scripts\refresh_skills_discovered.py" --root "<PROJECT>"
```

---

## For Grok Heavy

Please review for: missing skills, unclear purposes, category/status accuracy, and suggestions for consolidation or promotion from *draft* to *active*.

*Published from private `grok-skills-system` for public review only.*