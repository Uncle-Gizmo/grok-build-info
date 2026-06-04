# GROK_Build project governance — review pack for Grok Heavy

**Date:** 2026-06-04  
**Prepared by:** Grok CLI session (Cursor)  
**Example project:** `C:\AI\GROK_Build\AndroidApp` (Grok Calculator)  
**Platform housekeeping:** `C:\AI\GROK_Build\GROK_Build-Housekeeping`

**Purpose of this document:** Single markdown summarizing an audit of GROK_Build “governance” markdown (files that instruct AI agents how to work in a project), the problems found, and the platform + project fixes applied. Intended for **Grok Heavy** to read, critique, and suggest improvements.

**Related audit (platform):** `GOVERNANCE-AUDIT-ANDROIDAPP-2026-06-04.md` in the same folder.

---

## Instructions for Grok Heavy

Please review this pack and comment on:

1. Whether the **canonical resume order** is clear and sufficient for long sessions.
2. Whether **`.grok/AGENT-SETUP-TASKS.md`** is the right mechanism for AI-filled `AGENTS.md` (vs. codegen from repo scan in `setup_grok_build_project.py`).
3. Whether **`<!-- SYNC:ROADMAP:START/END -->`** between `PROJECT_MEMORY.md` (source of truth) and `AGENTS.md` (synced on shutdown) will stay reliable or drift.
4. Whether **generic Platform tooling** in `templates/AGENTS.md` is enough for Android/Windows projects without a per-stack file like `ANDROID_CLI.md`.
5. Any security or privacy risks in publishing this document to the public hub (see bottom).

---

## 1. Original user request (context)

The user asked an agent to:

- Load `C:\AI\GROK_Build\AndroidApp`
- **Not** execute the operational instructions inside governance markdown (no setup/shutdown/feature work)
- **Evaluate** auto-generated / maintained governance markdown
- Decide if instructions could be improved for AI agent interaction
- Identify useful additional instruction markdown

A follow-up session asked to:

- Save findings in `GROK_Build-Housekeeping`
- Add **AI fill of `AGENTS.md`** to the new-project setup flow (not manual-only)
- Apply in-project fixes to AndroidApp
- Apply generator/housekeeping fixes for **all** GROK_Build projects
- Prefer **generic** new-project files over per-project files like `ANDROID_CLI.md`
- **Not** add one-off migration archive logic to generic project creation

---

## 2. What counts as “governance” markdown

| File | Role | Created / updated by |
|------|------|----------------------|
| `AGENTS.md` | Primary agent guide (commands, architecture, resume order) | Template + **AI fill** after setup |
| `PROJECT_MEMORY.md` | Living truth: focus, roadmap, decisions, blockers | Template + sessions; **roadmap source of truth** |
| `.grok/AGENT-SETUP-TASKS.md` | Mandatory AI checklist after `run setup routine` | `setup_grok_build_project.py` |
| `.grok/PATH-CHECK.md` | Path / UNC / Android safety | Setup script (`path_checks.py`) |
| `docs/LATEST_WAYPOINT.txt` | Pointer to latest session handoff | `create_waypoint.py` on shutdown |
| `docs/WAYPOINT_*.md` | Session handoff (delta + short memory excerpt) | Shutdown |
| `docs/TLDR_*`, `RESTART_CHECKLIST_*` | Optional quick resume | Shutdown |
| `docs/build-notes/README.md` | Explains `*.txt` logs are **not** instructions | Generic setup |

**Not governance:** `docs/build-notes/*.txt` (Gradle/adb logs), archived one-off notes under `docs/archive/` (project-specific).

**Platform docs (outside project):** `GROK_Build-Housekeeping/ON-SESSION-START.md`, `NEW-PROJECT-SETUP.md`, `HELP.md`, `REGISTRY.md`, Constitution under `AI Operating System/`.

---

## 3. Audit findings (AndroidApp, before fixes)

### What worked

- Architecture tree in `AGENTS.md` matched Kotlin source layout.
- `CalculatorEngine` as pure Kotlin + `gradlew test assembleDebug` workflow was correct.
- `.grok/PATH-CHECK.md` was concise.

### Problems

| # | Problem | Impact on agents |
|---|---------|------------------|
| 1 | **Stale roadmap** — `AGENTS.md` had Stage 2 “Current”; memory said Stage 2 Done | Wrong priority |
| 2 | **Three resume orders** — ON-SESSION-START, TLDR, RESTART_CHECKLIST disagreed | Wasted turns |
| 3 | **Waypoint pasted ~2500 chars of PROJECT_MEMORY** | Token waste, drift |
| 4 | **Git contradictions** — “git unavailable” vs “Git: committed” in same waypoint | Confusion |
| 5 | **Template drift** — missing texttasks, “ask don’t guess”, unified resume | Weaker behavior |
| 6 | **Android notes only in `docs/*.txt`** | Agents miss SDK/AGP/adb context |

---

## 4. Design decisions (user-aligned)

### 4.1 AI fills `AGENTS.md` (not human by default)

**Problem:** `NEW-PROJECT-SETUP.md` said “human edits AGENTS.md” — did not scale.

**Solution:** Setup writes `.grok/AGENT-SETUP-TASKS.md` from `templates/AGENT-SETUP-TASKS.md`. Next agent session must scan repo + PATH-CHECK, fill `AGENTS.md` and `PROJECT_MEMORY.md`, mark **COMPLETE**.

Template `AGENTS.md` header points to this file.

### 4.2 Roadmap sync on shutdown

**Source of truth:** `PROJECT_MEMORY.md` between:

```html
<!-- SYNC:ROADMAP:START -->
| Stage | Scope | Status |
...
<!-- SYNC:ROADMAP:END -->
```

**Copy target:** Same markers in `AGENTS.md`.

**Mechanism:** `GROK_Build-Housekeeping/scripts/governance_sync.py` → called from `grok-skills-system/scripts/create_waypoint.py` after each shutdown.

### 4.3 Canonical resume order (all projects)

1. `docs/LATEST_WAYPOINT.txt` → named `docs/WAYPOINT_*.md`
2. `PROJECT_MEMORY.md`
3. `GROK_Build-Housekeeping/REGISTRY.md` — **only** for platform / cross-project work

Skip TLDR / RESTART_CHECKLIST unless user asks for “quick resume”.

Aligned in: `templates/AGENTS.md`, `ON-SESSION-START.md`, waypoint templates.

### 4.4 No per-project `ANDROID_CLI.md` on creation

Android-specific content is filled into **Platform tooling** in `AGENTS.md` when PATH-CHECK shows an Android Gradle project. Generic setup adds `docs/build-notes/README.md` so agents do not treat log `.txt` files as tasks.

### 4.5 Migration archive **not** in generic setup

`FOLDER-MIGRATION.md` was archived only on AndroidApp (`docs/archive/`) because that project had already migrated. New projects do not get `docs/archive/` automatically.

---

## 5. Platform changes applied (2026-06-04)

### New / updated files (housekeeping)

| Path | Change |
|------|--------|
| `GOVERNANCE-AUDIT-ANDROIDAPP-2026-06-04.md` | Audit record |
| `GROK-HEAVY-REVIEW-GOVERNANCE-2026-06-04.md` | This review pack |
| `scripts/governance_sync.py` | Git context, memory excerpt, roadmap sync |
| `templates/AGENTS.md` | Resume, SYNC roadmap, platform tooling, docs table |
| `templates/PROJECT_MEMORY.md` | SYNC roadmap, blockers section |
| `templates/AGENT-SETUP-TASKS.md` | AI fill checklist → `.grok/` |
| `templates/docs-build-notes-README.md` | → `docs/build-notes/README.md` on setup |
| `templates/waypoints/*.md` | Shorter excerpts, unified git line, resume order |
| `scripts/setup_grok_build_project.py` | Writes setup tasks + build-notes README |
| `NEW-PROJECT-SETUP.md` | Agent fills AGENTS; not human-default |
| `ON-SESSION-START.md` | Canonical resume + pending setup tasks |
| `SNIPPET-for-project-AGENTS.md` | Resume + setup tasks pointer |

### Generator changes (`grok-skills-system`)

| Path | Change |
|------|--------|
| `scripts/create_waypoint.py` | Uses `governance_sync`; no 2500-char memory dump |
| `scripts/project_close.py` | Git summary “N/A (not a git repository)” when no `.git` |

---

## 6. AndroidApp in-project changes (example)

| Item | Status |
|------|--------|
| Stage 2 → **Done**, Stage 3 → **Current** | `AGENTS.md` + `PROJECT_MEMORY.md` |
| Resume, docs table, platform tooling sections | `AGENTS.md` |
| `docs/build-notes/` for `*.txt` logs | Moved from `docs/` root |
| `docs/archive/FOLDER-MIGRATION.md` | One-off, marked completed |
| `.grok/AGENT-SETUP-TASKS.md` | **COMPLETE** |

**Note:** Waypoints dated `2026-06-03` still use old format until the next **shutdown routine**.

---

## 7. Publish skill (public Git) — for the human

**Skill path:** `C:\Users\HP\.grok\skills\publish-public-research\SKILL.md`

**Purpose:** Publish research/docs to a **separate public** repo while keeping `GROK_Build` / app repos **private**.

**Mandatory public hub (per skill):** `https://github.com/Uncle-Gizmo/grok-build-info`

**Critical rule:** Files under a **private** repo path (even `public/` or `docs/`) are **not** readable by Grok Heavy without auth. Only content in the public `-info` hub is suitable for “explore this URL.”

**Authorization:** Agent must **not** create repos or push without explicit user **yes**.

**Suggested publish path for this document:**

```text
grok-build-info/
  grok-build-governance/
    GROK-HEAVY-REVIEW-GOVERNANCE-2026-06-04.md
    README.md   (short hub blurb + link to housekeeping audit)
```

**Suggested GitHub topics (manual in UI):** `grok`, `ai-agents`, `documentation`, `developer-tools`, `android`

---

## 8. How to point Grok Heavy at this content

### Option A — Local path (if Grok Heavy has workspace access)

```text
C:\AI\GROK_Build\GROK_Build-Housekeeping\GROK-HEAVY-REVIEW-GOVERNANCE-2026-06-04.md
```

Also read: `GOVERNANCE-AUDIT-ANDROIDAPP-2026-06-04.md`

### Option B — Public URL (after publish skill + your authorization)

Published URL:

```text
https://github.com/Uncle-Gizmo/grok-build-info/blob/main/Gheavy/GROK-HEAVY-REVIEW-GOVERNANCE-2026-06-04.md
```

### Option C — Prompt snippet for Grok Heavy

```text
Read the governance review pack at [path or URL above].
Comment on: resume order, AGENT-SETUP-TASKS workflow, SYNC:ROADMAP markers,
generic platform tooling vs stack-specific files, and risks of publishing this publicly.
Do not execute setup/shutdown routines or modify AndroidApp code unless asked.
```

---

## 9. Open questions for Grok Heavy

1. Should waypoints be **collapsed further** (e.g. only `WAYPOINT_*.md` + pointer, drop TLDR/CHECKLIST entirely)?
2. Should `setup_grok_build_project.py` **auto-detect** stack (Android, Node, Python) and pre-fill Platform tooling without a second agent pass?
3. Is `<!-- SYNC:ROADMAP -->` too fragile — would YAML front matter or a single `STATUS.json` be better?
4. Should `REGISTRY.md` ever be read on **every** app session, or only when the user says “platform work”?
5. What should the **minimum** `AGENTS.md` length be for small projects to avoid template bloat?

---

## 10. Privacy checklist before public publish

Before pushing to `grok-build-info`, confirm this doc contains **no**:

- API keys, tokens, or vault paths with secrets
- Private machine paths that should not be public (user may redact `C:\Users\HP` → `C:\...` if desired)
- Unreleased product names the user wants private

This pack references `C:\AI\GROK_Build\...` paths as documentation of the system layout — user should approve publishing those paths.

---

*End of review pack.*