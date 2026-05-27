# Report for Grok Heavy: Improving blueprint_gen_5.py (Chatter Blueprint Generator)

**Project:** Chatter_Svelte (Chatter_Fix_Me)  
**Location:** C:\AI\CONTEXT_FORGE_PROJECTS\Chatter_Svelte\Chatter_Fix_Me  
**Date:** 2026-05-27  
**Analysis performed by:** Grok 4.3 (Grok Build TUI)  
**Source material:** `blueprint_gen_5.py` (v4.0.0), the May 6 2026 generated output folder, `blueprint_manifest.json`, existing self-analysis docs inside the blueprints, and the user instruction note `upgrade blueprint_gen_5.py.txt`

---

## Executive Summary

The `blueprint_gen_5.py` script is one of the most valuable and distinctive pieces of infrastructure in this project. It converts the live codebase into structured, LLM-optimized Markdown collections ("blueprints") that power two important capabilities:

- Chatter's "attach collection to chat" feature (any Grok instance reading the collection gets rich project context).
- Grok Build (this TUI) gaining high-quality, multi-view context that raw filesystem tools and simple searches do not easily provide.

**The core problem is real and measurable:** despite excellent filtering and the improvements in v4.0.0, a single "complete" run still produces excessive low-signal volume and noise.

Real data from the 2026-05-06 run on this project:
- 245 files generated
- ~705,000 estimated tokens
- `misc_configs` group: 113 files / 382k tokens (undifferentiated catch-all)
- `PHASE_4_FINAL_VERIFICATION`: 72 files / 125k tokens
- Dozens of 1-file groups, each receiving full `structure_*.md` + `details_*.md` + verbose headers

This dilutes signal, increases cost, and reduces effectiveness for any LLM (Grok Heavy or otherwise) consuming the collection.

The script is already professional-grade and has evolved well (v4.0.0 added argparse CLI, anchored grouping, and stronger noise reduction). The remaining gap is **precision and restraint**, not raw coverage.

---

## Current Strengths (v4.0.0)

- Aggressive, effective noise reduction (`EXCLUDE_DIRS`, `EXCLUDE_FILES`, patterns, `ALLOWED_EXTS`).
- Shift from global heuristic scanning to **directory-anchored grouping** (a major architectural improvement — see dedicated groups for `rune_stores`, `panels_system`, `control_panel`, `api_backend`, `frontend_svelte_*`, etc.).
- Python `ast`-based import tracing for dependency-aware groups.
- Automatic generation of high-level narrative overview documents (`00_ARCHITECTURE_OVERVIEW.md`, `01_KEY_COMPONENTS.md`, `GROK_INTEGRATION_STATUS.md`, `master_catalog.md`).
- Smart "update-only" mode with header-aware normalization (strips metadata so content changes are correctly detected).
- Per-group token estimation + machine-readable `blueprint_manifest.json`.
- Clean, timestamped output folders that are immediately usable by any LLM.
- Self-documenting: the generator produces analysis documents about itself.

These are genuine strengths. The script is already better than most automated project documentation tools.

---

## Evidence of the Problem

From the live `blueprint_manifest.json` (2026-05-06_10-20-21 run):

| Group                          | Files | Est. Tokens | Observation |
|--------------------------------|-------|-------------|-------------|
| `misc_configs`                 | 113   | 382,059     | Massive undifferentiated bucket |
| `PHASE_4_FINAL_VERIFICATION`   | 72    | 124,575     | Legacy/experimental catch-all |
| `api_backend`                  | 71    | 122,378     | Necessary but extremely large |
| `frontend_svelte_components`   | 13    | 31,903      | Reasonable density |
| `rune_stores`                  | 8     | 10,159      | Good |
| Many 1-file groups             | 1     | < 1k        | Each still gets full structure + details + long headers |

Every file carries a repetitive 10-line metadata header. Details files are near-literal full source dumps. Tiny standalone scripts (`check_db.py`, `find_kilo_bridge.py`, `project_root_detector_v1.py`, etc.) each spawn their own group with full treatment.

The older self-generated `14_BLUEPRINT_GENERATOR_IMPROVEMENTS.md` (from a prior version of the script) already identified many of the same volume and signal issues.

---

## Root Causes

1. **Over-granular grouping** — Any non-trivial standalone file or small module is promoted to its own group.
2. **No token or file-count budgets** — Nothing enforces "this group is too noisy."
3. **No importance / semantic tiering** — The logic is purely structural + dependency-based. It cannot distinguish "architecturally critical" from "one-off verification script."
4. **Details files are too literal** — Full raw content + verbose headers for almost everything.
5. **Catch-all groups become black holes** (`misc_configs`, verification phases, etc.).
6. **Narrative documents exist but are not strong enough** to compensate for the volume of raw details.

---

## Prioritized Recommendations for Grok Heavy

### High Priority (Highest Leverage)

1. **Add global + per-group token/file budgets** (`--max-tokens`, `--max-group-tokens`). Force summarization, truncation, or exclusion when limits are exceeded.
2. **Collapse tiny / low-value groups** — Any group with < 3 files or < ~2–3k tokens should be merged into "support_scripts", "misc_tools", or omitted from full details (structure + summary only).
3. **Two-tier output model**:
   - Always produce: clean structure trees + strong narrative documents + manifest.
   - Conditionally produce: full `details_*` only for explicitly high-importance groups (core architecture, stores, routers, entry points, agentic subsystems).
4. **Importance heuristics / allow-list** — Tag groups as `core`, `high`, `medium`, `low`. Low-value groups (verification scripts, detectors, one-off bridges, legacy experiments) receive lighter treatment by default.
5. **Narrative-first design** — Make the 5–8 high-level narrative documents the *primary* content any LLM is expected to read. Raw details become secondary / on-demand.

### Medium Priority

6. Add `--focus`, `--include-groups`, `--exclude-groups`, or a small `blueprint_focus.yaml` for project-specific tuning.
7. "Executive / light" mode (`--light` or `--exec`) that targets a hard cap (e.g. < 60 files / < 150k tokens) while preserving essential signal.
8. Automatic secret redaction, binary skipping, and license file handling.
9. Git-aware incremental mode (only changed files since last run + impact summary).
10. Optional self-critique pass: after generation, have the local model score the output for signal density and suggest pruning.

### Lower Priority / Future

- External templates (Jinja-style) for all narrative documents.
- Direct xAI Collections upload with diff-only logic (building on the v4.0.0 CLI foundation).
- Versioned blueprints + auto-generated changelog between runs.

---

## Alternative / Better Approaches

These go beyond incremental fixes on the current script. They are offered for Grok Heavy to evaluate:

**A. Manifest-Driven / Curated Core (Strongly Recommended)**
Maintain a small, human- or LLM-curated `blueprint_core.yaml` (or JSON) that explicitly lists the 15–25 most important artifacts/groups. The generator produces full narrative + details for the core set and very lightweight structure/summary only for everything else. This flips the model from "maximum coverage" to "guaranteed high signal."

**B. Layered / Hierarchical Collections**
Produce multiple collections from one run:
- `Chatter_Fix_Me_Architect.md` (narratives + core files only)
- `Chatter_Fix_Me_Codebase.md` (full details for agents)
- `Chatter_Fix_Me_Changes.md` (incremental since last run)
Users attach the appropriate layer.

**C. LLM-Guided Pruning (Agentic Evolution)**
After a full scan, feed the manifest + group sizes to a model (Grok Heavy or local) with a prompt such as: "Prune to maximum 120k tokens while preserving all architectural decision points and agentic execution paths." Output a pruned subset + rationale. This creates a self-improving loop.

**D. Hybrid: Structure + RAG-Ready Chunks**
Move away from huge `details_*.md` files. Produce clean structure trees, narrative documents, and smaller, overlapping, semantically chunked files optimized for embedding/RAG instead of one giant linear Markdown per group.

**E. "Living Blueprint" Mode**
Maintain a continuously updated lightweight blueprint (via file watchers or git hooks) that only tracks high-importance changes and updates narratives + manifest. Full detail dumps become rare, on-demand operations.

---

## Validation & Measurement

Any improvement should be measured with concrete before/after metrics on this project:
- Total files and total estimated tokens
- Token distribution (max group size, % in top 5 groups)
- Signal density (subjective scoring by Grok Heavy or human review when the collection is attached)
- Real usage quality in Chatter chats and Grok Build sessions that consume the collection

---

## Risks & Trade-offs

- Over-pruning could hide important context for future agentic work (mitigate with an explicit "low but included" tier + easy override).
- Project-specific tuning (`blueprint_focus.yaml`) adds maintenance burden.
- Changing output format risks breaking existing collections (version the generator and support legacy modes).

---

## Recommended Immediate Next Steps

1. Review this report together with the actual May 6 output folder and current `blueprint_gen_5.py`.
2. Decide on the primary philosophy: "maximum safe coverage" vs "guaranteed high-signal core."
3. Prototype one high-impact change (e.g. group collapsing + token budgets) and re-run on this project.
4. Attach the new output to a test collection and evaluate real usage in both Chatter and Grok Build.
5. Iterate and update the generator’s own self-documentation as part of the change.

---

## Final Note to Grok Heavy

This generator is already a sophisticated and genuinely useful piece of infrastructure. The opportunity is not to rewrite it from scratch, but to evolve it from a "very good structural dumper" into a **precision context engine** that reliably gives you (and other models) the exact views needed for high-quality architectural reasoning and agentic collaboration — without the current noise tax.

The data from the May 6 run and the script’s own earlier self-analysis both point in the same direction: we need stronger, more opinionated filtering, tiering, and budgets on top of the excellent foundation that already exists.

This analysis was performed directly inside the project using Grok Build tools and the live generated artifacts.

---

**End of Report**

*If you are Grok Heavy reading this via a collection, you now have the full context needed to propose concrete code changes, a new generation mode, or a different architectural approach.*

---

## Appendix: Full Source Code of blueprint_gen_5.py (v4.0.0)

```python
"""
blueprint_gen_5.py (v4.0.0 - Modernized CLI + Current Architecture 2026)
Generates LLM-optimized .md blueprints with strong noise reduction
and high-level narrative documents.

v4.0.0 Changes (2026-04-10):
- Removed dead frontend/ directory block and AgentBuilderPanel.svelte references
- Replaced global heuristic scan with anchored frontend_svelte/ grouping
- Added dedicated groups: rune_stores, panels_system, control_panel,
  dual_server_entry, api_backend, frontend_svelte_config/routes/components/lib_other
- Converted interactive input() to argparse CLI (--mode, --fast, --dry-run,
  --upload-to-collection, --output, --no-row-counts, --project-name)
- Updated narrative documents to reflect: modular rune stores (7 stores),
  API-only backend, dual-server architecture, agentic panel system
- run_server.py now in its own group (1 file) instead of dragging 34 backend files
- backend/ now directory-anchored as api_backend instead of dependency-traced

Author: Oppie (based on original by ATH/GROK)
"""
import os
import sys
import ast
import argparse
import datetime
import sqlite3
import json
from pathlib import Path
from collections import defaultdict

# -------------------------------
# Configuration - Improved Filtering
# -------------------------------
EXCLUDE_DIRS = {
    '.git', '.venv', '__pycache__', 'site-packages', 'dist-info', 'node_modules',
    'logs', 'build', 'dist', '.pytest_cache', '.idea', '.vscode', 'blueprints',
    'docs', '.svelte-kit', 'generated', 'static', 'public', 'temp',
    '.kilo', '.kilocode', '.opencode', 'agents', 'plans', 'resources',
    'migrations', 'data', 'backup'
}
EXCLUDE_FILES = {
    'package-lock.json', 'yarn.lock', 'pnpm-lock.yaml', '.DS-Store',
    'Thumbs.db', 'tsconfig.tsbuildinfo'
}
EXCLUDE_PATTERNS = ['*.log', '*.min.js', '*.map', '*-lock.json', '*.lock', '*.bak']
ALLOWED_EXTS = {'.py', '.svelte', '.js', '.ts', '.jsx', '.tsx', '.md', '.yaml',
                '.yml', '.toml', '.sql', '.html', '.css', '.json'}
DETAILS_MAX_BYTES = 2_000_000

FRONTEND_DIR_NAME = "frontend_svelte"

# GROUP_TAGS: maps group names to descriptive tags for LLM understanding
GROUP_TAGS = {
    'core_instructions': ['rules', 'guidance', 'specification'],
    'frontend_svelte_config': ['svelte5', 'sveltekit', 'vite', 'tailwind4', 'config'],
    'frontend_svelte_routes': ['sveltekit-routes', 'file-based-routing'],
    'frontend_svelte_components': ['svelte5', 'ui-components'],
    'control_panel': ['control-panel', 'process-management', 'spec-editor'],
    'rune_stores': ['rune-stores', 'svelte-5-reactivity', 'state'],
    'panels_system': ['panel-manager', 'panel-host', 'registry', 'agentic-ui'],
    'frontend_svelte_lib_other': ['sveltekit-lib', 'utils', 'api'],
    'dual_server_entry': ['dual-server', 'fastapi', 'vite-dev', 'orchestration'],
    'api_backend': ['fastapi', 'api-only', 'routes', 'crud', 'models'],
    'grok_collections': ['xai_sdk', 'sqlite-sync', 'collections-service'],
    'misc_configs': ['config', 'env', 'settings'],
}

def should_include_file(f: Path) -> bool:
    """Strict filter to eliminate noisy and unnecessary files."""
    if any(part in EXCLUDE_DIRS for part in f.parts):
        return False
    if f.name in EXCLUDE_FILES:
        return False
    if any(f.match(pat) for pat in EXCLUDE_PATTERNS):
        return False
    if f.suffix not in ALLOWED_EXTS:
        return False
    return True

def get_file_header(file_path: Path, description: str, project_root: Path, layer: str = "General") -> str:
    now = datetime.datetime.now().strftime("%Y-%m-%d")
    rel = str(file_path.relative_to(project_root)).replace('\\', '/')
    return f"""# =============================================
# File: {file_path.name}
# Project-Relative Path: ./{rel}
# Description: {description}
# Layer: {layer}
# Updated: {now}
# Version: 4.0.0 (Modernized CLI + Current Architecture 2026)
# Author: Oppie
# =============================================
"""

def timestamp() -> str:
    return datetime.datetime.now().strftime("%Y-%m-%d_%H-%M-%S")

def normalize_for_comparison(content: str) -> str:
    lines = content.splitlines()
    normalized = []
    in_header = True
    for line in lines:
        stripped = line.strip()
        if in_header and (not stripped or stripped.startswith("#") or "===" in stripped or "Updated:" in stripped):
            continue
        if not stripped:
            continue
        normalized.append(stripped)
        in_header = False
    return "\n".join(normalized).strip()

def get_imports(path: Path) -> set:
    imports = set()
    try:
        with open(path, 'r', encoding='utf-8', errors='ignore') as f:
            tree = ast.parse(f.read())
        for node in ast.walk(tree):
            if isinstance(node, ast.Import):
                for alias in node.names:
                    imports.add(alias.name.split('.')[0])
            elif isinstance(node, ast.ImportFrom):
                if node.module:
                    imports.add(node.module.split('.')[0])
    except Exception:
        pass
    return imports

def estimate_tokens(text: str) -> int:
    return len(text) // 4

def extract_sqlite_schema(db_path: Path, include_row_counts: bool = True) -> str:
    out_lines = []
    summary = "-- Schema summary: "
    try:
        conn = sqlite3.connect(str(db_path))
        cur = conn.cursor()
        cur.execute("SELECT name FROM sqlite_master WHERE type='table' AND name NOT LIKE 'sqlite_%';")
        table_names = [row[0] for row in cur.fetchall()]
        for table in table_names:
            cur.execute(f"SELECT sql FROM sqlite_master WHERE type='table' AND name='{table}';")
            sql_row = cur.fetchone()
            if sql_row and sql_row[0]:
                out_lines.append(sql_row[0].strip() + ";")
            if include_row_counts:
                try:
                    cur.execute(f"SELECT COUNT(*) FROM {table};")
                    count = cur.fetchone()[0]
                    summary += f"{table}: {count} rows | "
                except:
                    summary += f"{table}: count failed | "
        conn.close()
    except Exception as e:
        return f"-- Schema read failed: {e}"
    summary = summary.rstrip(" | ") + "\n" if include_row_counts and table_names else ""
    return summary + "\n".join(out_lines)

def generate_group_ascii_tree(group_files: set, project_root: Path) -> str:
    tree = {}
    for fpath in group_files:
        rel = fpath.relative_to(project_root)
        current = tree
        for part in rel.parts[:-1]:
            current = current.setdefault(part, {})
        current[rel.name] = None
    def render(node, prefix=""):
        lines = []
        items = sorted(node.items())
        for i, (name, child) in enumerate(items):
            is_last = i == len(items) - 1
            conn = "└── " if is_last else "├── "
            lines.append(f"{prefix}{conn}{name}")
            if isinstance(child, dict):
                new_prefix = prefix + (" " if is_last else "│ ")
                lines.extend(render(child, new_prefix))
        return lines
    return "\n".join(render(tree)) or "(no files in group)"

def generate_narrative_documents(out_dir: Path, project_root: Path, groups: dict):
    """Generate high-level narrative documents for better LLM understanding."""

    # 00_ARCHITECTURE_OVERVIEW.md
    arch_path = out_dir / "00_ARCHITECTURE_OVERVIEW.md"
    arch = get_file_header(arch_path, "High-level system architecture and blueprint usage", project_root, "Overview")
    arch += "\n## How to Use This Blueprint\n"
    arch += "1. Read `blueprint_manifest.json` first\n"
    arch += "2. Read `00_ARCHITECTURE_OVERVIEW.md`\n"
    arch += "3. Read `master_catalog.md`\n"
    arch += "4. Review `GROK_INTEGRATION_STATUS.md`\n\n"
    arch += "## Project Description\n"
    arch += "Custom chat interface (Chatter) supporting multiple LLMs via OpenRouter and native Grok models via xAI.\n"
    arch += "Architecture: API-only backend (FastAPI) + SvelteKit 5 frontend (frontend_svelte/),\n"
    arch += "launched via dual-server orchestration (run_server.py -> uvicorn + vite dev).\n\n"
    arch += "## Dual-Server Architecture\n"
    arch += "- **Backend**: FastAPI on port 8000 (configurable via app_settings DB table)\n"
    arch += "- **Frontend**: SvelteKit 5 dev server on port 5173 (Vite)\n"
    arch += "- **Entry point**: run_server.py orchestrates both, opens browser automatically\n"
    arch += "- **No SSR**: Frontend communicates with backend via REST API + WebSockets\n\n"
    arch += "## Frontend Architecture (frontend_svelte/)\n"
    arch += "The frontend uses SvelteKit 5 with Svelte 5 runes ($state, $derived, $effect):\n"
    arch += "- frontend_svelte_config -> svelte.config, vite.config, package.json, tailwind\n"
    arch += "- frontend_svelte_routes -> file-based routing (+page.svelte, +layout.svelte, +server.ts)\n"
    arch += "- frontend_svelte_components -> UI components (ChatMessage, ChatInput, Sidebar, etc.)\n"
    arch += "- control_panel -> Process management subsystem (SpecForm, ProcessList, ConstraintEditor)\n"
    arch += "- rune_stores -> 7 modular Svelte 5 reactive stores (message, thread, ui, model, collection, tag, theme)\n"
    arch += "- panels_system -> Agentic panel registry (PanelManager, PanelHost, panelStore, registry)\n"
    arch += "- frontend_svelte_lib_other -> utils/api.ts and other lib files\n"
    arch_path.write_text(arch, encoding='utf-8')

    # 01_KEY_COMPONENTS.md
    key_path = out_dir / "01_KEY_COMPONENTS.md"
    key = get_file_header(key_path, "Most important files and components", project_root, "Overview")
    key += "\n## Key Components\n"
    for group_name in sorted(groups.keys()):
        key += f"- **{group_name}**: {len(groups[group_name])} files\n"
    key += "\n## Architecture Highlights\n"
    key += "- Dual-server: FastAPI backend + SvelteKit 5 frontend (run_server.py orchestrates)\n"
    key += "- 7 modular rune stores replacing monolithic chatStore\n"
    key += "- Agentic panel system (PanelManager + registry pattern) ready for extension\n"
    key += "- API-only backend: no server-side rendering\n"
    key_path.write_text(key, encoding='utf-8')

    # GROK_INTEGRATION_STATUS.md
    grok_path = out_dir / "GROK_INTEGRATION_STATUS.md"
    grok = get_file_header(grok_path, "Current state of Grok + Collections integration", project_root, "Feature")
    grok += "\n## Grok Integration Status\n"
    grok += "- Dedicated GROK tab in UI\n"
    grok += "- `file_search` configuration for collection search (native xAI API)\n"
    grok += "- Per-chat `grok_collection_id` support via collectionStore.svelte.ts\n"
    grok += "- xAI SDK integration for collection management (list_grok_collections.py)\n"
    grok += "- Using xAI platform + OpenRouter fallback\n"
    grok_path.write_text(grok, encoding='utf-8')

def map_project_structure(root: Path):
    all_files = {}
    module_to_path = defaultdict(list)
    for f in root.rglob("*"):
        if f.is_file() and should_include_file(f):
            all_files[f] = True
            if f.suffix == '.py':
                rel = f.parent.relative_to(root)
                module_name = '.'.join(rel.parts) if rel.parts else ''
                module_to_path[module_name].append(f)
    return all_files, module_to_path

def trace_dependencies(entry_point: Path, root: Path, module_map: dict, all_files: dict):
    involved_files = set()
    stack = [entry_point]
    visited = set()
    while stack:
        current = stack.pop()
        if current in involved_files: continue
        involved_files.add(current)
        if current.suffix == '.py':
            for imp in get_imports(current):
                if imp in visited: continue
                visited.add(imp)
                for mod_name, files in module_map.items():
                    if mod_name == imp or mod_name.startswith(imp + '.'):
                        for f in files:
                            if f not in involved_files:
                                stack.append(f)
    return involved_files

def remove_unchanged_files(new_dir: Path):
    blueprint_base = new_dir.parent
    timestamp_dirs = sorted([d for d in blueprint_base.iterdir() if d.is_dir() and '_' in d.name],
                            key=lambda d: d.name, reverse=True)
    if len(timestamp_dirs) < 2:
        print(" - First blueprint → keeping all files")
        return
    previous_dir = timestamp_dirs[1]
    print(f" - Comparing to previous blueprint: {previous_dir.name}")
    removed = 0
    for file_path in list(new_dir.glob("*.md")):
        prev_path = previous_dir / file_path.name
        if not prev_path.exists(): continue
        try:
            new_c = normalize_for_comparison(file_path.read_text(encoding='utf-8', errors='ignore'))
            old_c = normalize_for_comparison(prev_path.read_text(encoding='utf-8', errors='ignore'))
            if new_c == old_c:
                file_path.unlink()
                removed += 1
        except Exception:
            continue
    print(f" - Removed {removed} unchanged files")

def generate_blueprints(project_root: Path, out_dir: Path, mode: str, include_row_counts: bool, fast_mode: bool = False, project_name: str | None = None):
    print("[STEP 2] Mapping project structure...")
    all_files, module_map = map_project_structure(project_root)
    
    groups = defaultdict(set)
    
    # Core instruction files
    core_patterns = ['LLM Instruction 1a.txt', 'ai-guidance.txt', 'Project Specification Document.md',
                     'LLM Chat Interface – Functional Specification.md']
    for pattern in core_patterns:
        for f in project_root.rglob(pattern):
            if should_include_file(f):
                groups['core_instructions'].add(f)

    # Entry point detection and dependency tracing (Python scripts only)
    # Note: run_server.py is handled separately as dual_server_entry
    entry_points = [f for f in project_root.glob("*.py")
                    if should_include_file(f) and f.name != "run_server.py"]
    for subdir in project_root.iterdir():
        if subdir.is_dir() and subdir.name not in EXCLUDE_DIRS and subdir.name != FRONTEND_DIR_NAME:
            for pat in ['main.py', 'app.py', 'run.py']:
                candidate = subdir / pat
                if candidate.exists() and should_include_file(candidate):
                    entry_points.append(candidate)
    for ep in entry_points:
        group_name = ep.stem
        if ep.parent != project_root:
            group_name += f"_{ep.parent.name}"
        group_name = group_name.replace(" ", "_").replace("-", "_")
        deps = trace_dependencies(ep, project_root, module_map, all_files)
        groups[group_name].update(deps)

    # === DUAL-SERVER ENTRY POINT (run_server.py only, not dependency-traced) ===
    run_server_file = project_root / "run_server.py"
    if run_server_file.exists() and should_include_file(run_server_file):
        groups['dual_server_entry'].add(run_server_file)

    # === BACKEND: DIRECTORY-ANCHORED (not dependency-traced) ===
    backend_dir = project_root / "backend"
    if backend_dir.exists():
        for f in backend_dir.rglob("*"):
            if f.is_file() and should_include_file(f):
                groups['api_backend'].add(f)

    # === FRONTEND_SVELTE: ANCHORED GROUPING ===
    frontend_dir = project_root / FRONTEND_DIR_NAME
    frontend_src = frontend_dir / "src" if frontend_dir.exists() else None

    if frontend_dir.exists():
        # 1. Config files (frontend_svelte/ root level)
        config_patterns = [
            "svelte.config.js", "svelte.config.ts",
            "vite.config.js", "vite.config.ts",
            "tailwind.config.js", "tailwind.config.ts",
            "postcss.config.js", "postcss.config.ts",
            "package.json", "tsconfig.json",
            "app.html", "app.css"
        ]
        for f in frontend_dir.iterdir():
            if f.is_file() and should_include_file(f):
                if any(f.name == pat for pat in config_patterns) or f.name.startswith("tsconfig."):
                    groups['frontend_svelte_config'].add(f)

        if frontend_src and frontend_src.exists():
            # 2. Routes (src/routes/)
            routes_dir = frontend_src / "routes"
            if routes_dir.exists():
                for f in routes_dir.rglob("*"):
                    if f.is_file() and should_include_file(f):
                        groups['frontend_svelte_routes'].add(f)

            # 3. Rune stores (src/lib/state/)
            state_dir = frontend_src / "lib" / "state"
            if state_dir.exists():
                for f in state_dir.rglob("*"):
                    if f.is_file() and should_include_file(f):
                        groups['rune_stores'].add(f)

            # 4. Panel system (src/lib/panels/)
            panels_dir = frontend_src / "lib" / "panels"
            if panels_dir.exists():
                for f in panels_dir.rglob("*"):
                    if f.is_file() and should_include_file(f):
                        groups['panels_system'].add(f)

            # 5. ControlPanel (src/lib/components/ControlPanel/)
            cp_dir = frontend_src / "lib" / "components" / "ControlPanel"
            if cp_dir.exists():
                for f in cp_dir.rglob("*"):
                    if f.is_file() and should_include_file(f):
                        groups['control_panel'].add(f)

            # 6. Other components (src/lib/components/ but NOT ControlPanel/)
            components_dir = frontend_src / "lib" / "components"
            if components_dir.exists():
                for f in components_dir.iterdir():
                    if f.is_file() and should_include_file(f):
                        groups['frontend_svelte_components'].add(f)
                    elif f.is_dir() and f.name != "ControlPanel":
                        for sf in f.rglob("*"):
                            if sf.is_file() and should_include_file(sf):
                                groups['frontend_svelte_components'].add(sf)

            # 7. Remaining lib files (src/lib/ minus state, panels, components)
            claimed_lib_dirs = {"state", "panels", "components"}
            lib_dir = frontend_src / "lib"
            if lib_dir.exists():
                for f in lib_dir.rglob("*"):
                    if f.is_file() and should_include_file(f):
                        rel_parts = f.relative_to(lib_dir).parts
                        if rel_parts and rel_parts[0] in claimed_lib_dirs:
                            continue
                        groups['frontend_svelte_lib_other'].add(f)

    # Group remaining files
    grouped = set().union(*groups.values()) if groups else set()
    ungrouped = {f for f in all_files if f not in grouped}
    if ungrouped:
        groups['misc_configs'].update(ungrouped)

    # Remove groups whose files are entirely within backend/ (already covered by api_backend)
    # Also merge groups that are dependency-traced from backend entry points
    backend_dir = project_root / "backend"
    groups_to_remove = []
    for gname, gfiles in list(groups.items()):
        if gname in ('api_backend', 'dual_server_entry', 'core_instructions'):
            continue
        py_files = {f for f in gfiles if f.suffix == '.py'}
        if not py_files:
            continue
        # If all Python files are inside backend/ or services/, merge into api_backend
        backend_or_service = all(
            str(f).startswith(str(backend_dir)) or
            str(f.relative_to(project_root)).startswith('services')
            for f in py_files
        )
        if backend_or_service:
            groups_to_remove.append(gname)
    for gname in groups_to_remove:
        groups['api_backend'].update(groups.pop(gname))

    # Generate narrative documents
    generate_narrative_documents(out_dir, project_root, groups)

    master_catalog = []
    total_tokens = 0
    groups_data = {}
    for group_name, files in groups.items():
        if not files: continue
        print(f" - Writing group '{group_name}' ({len(files)} files)...")
        struct_file = out_dir / f"structure_{group_name}.md"
        details_file = out_dir / f"details_{group_name}.md"
        s_content = get_file_header(struct_file, f"ASCII file tree for group: {group_name}", project_root, "Structure")
        s_content += "\n## Directory Tree\n\n```text\n" + generate_group_ascii_tree(files, project_root) + "\n```\n"
        struct_file.write_text(s_content, encoding='utf-8')
        if fast_mode:
            d_content = get_file_header(details_file, f"Full raw content for group: {group_name}", project_root, "Details")
            d_content += f"\n(Fast mode: {len(files)} files skipped)\n"
            details_file.write_text(d_content, encoding='utf-8')
            group_tokens = 0
        else:
            d_content = get_file_header(details_file, f"Full raw content for group: {group_name}", project_root, "Details")
            group_tokens = 0
            for fpath in sorted(files):
                rel = str(fpath.relative_to(project_root)).replace('\\', '/')
                lang = fpath.suffix[1:] if fpath.suffix else 'text'
                d_content += f"\n## File: ./{rel}\n\n```{lang}\n"
                try:
                    if fpath.suffix == '.db':
                        d_content += extract_sqlite_schema(fpath, include_row_counts)
                    elif fpath.stat().st_size < DETAILS_MAX_BYTES:
                        text = fpath.read_text(encoding='utf-8', errors='ignore')
                        d_content += text
                        group_tokens += estimate_tokens(text)
                    else:
                        d_content += "(file too large)"
                except Exception as e:
                    d_content += f"(read error: {e})"
                d_content += "\n```\n"
            details_file.write_text(d_content, encoding='utf-8')
        tags = GROUP_TAGS.get(group_name, ["general"])
        master_catalog.append({"group": group_name, "files_count": len(files), "tokens": group_tokens, "tags": tags})
        total_tokens += group_tokens
        groups_data[group_name] = {"files": len(files), "tokens": group_tokens, "tags": tags,
                                  "purpose": f"Directory-anchored grouping (v4.0.0)"}

    # Manifest and master catalog
    p_name = project_name or project_root.name
    manifest = {
        "blueprint_version": "4.0.0",
        "blueprint_date": datetime.datetime.now().isoformat(),
        "project_name": p_name,
        "total_files": len(all_files),
        "total_estimated_tokens": total_tokens,
        "mode": mode,
        "fast_mode": fast_mode,
        "groups": groups_data
    }
    (out_dir / "blueprint_manifest.json").write_text(json.dumps(manifest, indent=2), encoding='utf-8')

    master_path = out_dir / "master_catalog.md"
    content = get_file_header(master_path, "Master index and usage guide", project_root, "Overview")
    content += "\n## Blueprint Usage\n"
    content += "1. Start with `00_ARCHITECTURE_OVERVIEW.md`\n"
    content += "2. Read `blueprint_manifest.json`\n"
    content += "3. Read this master_catalog.md`\n"
    content += "4. Review `GROK_INTEGRATION_STATUS.md`\n\n"
    content += "## Groups\n"
    for item in sorted(master_catalog, key=lambda x: x['group']):
        content += f"**{item['group']}** — {', '.join(item['tags'])}\n"
        content += f"- Files: {item['files_count']} | Tokens: ~{item['tokens']}\n\n"
    master_path.write_text(content, encoding='utf-8')

    print(f" Generated {len(groups)} groups + 3 narrative documents (v4.0.0 Dual-Server + Rune Stores)")
    return len(all_files), total_tokens

def main():
    parser = argparse.ArgumentParser(
        description="Blueprint Generator v4.0.0 — Modernized CLI + Current Architecture"
    )
    parser.add_argument("--project", "-p", default=None,
                        help="Project root path (default: current directory)")
    parser.add_argument("--mode", "-m", choices=["complete", "update"], default="complete",
                        help="Blueprint mode (default: complete)")
    parser.add_argument("--fast", "-f", action="store_true",
                        help="Fast mode: skip detail files, generate structure + narratives only")
    parser.add_argument("--dry-run", "-d", action="store_true",
                        help="Preview what would be generated without writing files")
    parser.add_argument("--upload-to-collection", "-u", type=str, default=None, metavar="UUID",
                        help="Upload to xAI Grok Collection UUID after generation (stub)")
    parser.add_argument("--output", "-o", type=str, default=None,
                        help="Override output directory path")
    parser.add_argument("--no-row-counts", action="store_true",
                        help="Skip SQLite row counting for faster generation")
    parser.add_argument("--project-name", "-n", type=str, default=None,
                        help="Override project name (default: folder name)")

    # Interactive fallback if no arguments provided
    if len(sys.argv) == 1:
        print("No arguments provided. Use --help for options, or running interactive prompts (legacy).")
        # Legacy interactive prompts would go here if needed for backward compatibility
        # For now, show help and exit
        parser.print_help()
        return

    args = parser.parse_args()

    project_root = Path(args.project).resolve() if args.project else Path.cwd()
    if not project_root.exists():
        print(f"Error: Project path does not exist: {project_root}")
        return

    mode = "update_only" if args.mode == "update" else "complete"
    fast_mode = args.fast
    dry_run = args.dry_run
    upload_uuid = args.upload_to_collection

    p_name = args.project_name

    if dry_run:
        print(f"\n[DRY RUN] Would generate {mode} blueprint in: {project_root / 'blueprints' / p_name / timestamp()}")
        all_files, _ = map_project_structure(project_root)
        groups = defaultdict(set)

        frontend_dir = project_root / FRONTEND_DIR_NAME
        if frontend_dir.exists():
            for f in frontend_dir.rglob("*"):
                if f.is_file() and should_include_file(f):
                    groups["frontend_svelte (total)"].add(f)

        backend_dir = project_root / "backend"
        if backend_dir.exists():
            for f in backend_dir.rglob("*"):
                if f.is_file() and should_include_file(f):
                    groups["api_backend"].add(f)

        print(f"[DRY RUN] Total files scanned: {len(all_files)}")
        print(f"[DRY RUN] Frontend files (frontend_svelte/): {len(groups.get('frontend_svelte (total)', set()))}")
        print(f"[DRY RUN] Backend files (backend/): {len(groups.get('api_backend', set()))}")

        state_dir = frontend_dir / "src" / "lib" / "state" if frontend_dir.exists() else None
        if state_dir and state_dir.exists():
            state_files = [f for f in state_dir.iterdir() if f.is_file() and should_include_file(f)]
            print(f"[DRY RUN] Rune store files (src/lib/state/): {len(state_files)}")
            for sf in sorted(state_files):
                print(f"  - {sf.name}")

        panels_dir = frontend_dir / "src" / "lib" / "panels" if frontend_dir.exists() else None
        if panels_dir and panels_dir.exists():
            panel_files = [f for f in panels_dir.rglob("*") if f.is_file() and should_include_file(f)]
            print(f"[DRY RUN] Panel system files (src/lib/panels/): {len(panel_files)}")
            for pf in sorted(panel_files):
                print(f"  - {pf.name}")

        cp_dir = frontend_dir / "src" / "lib" / "components" / "ControlPanel" if frontend_dir.exists() else None
        if cp_dir and cp_dir.exists():
            cp_files = [f for f in cp_dir.rglob("*") if f.is_file() and should_include_file(f)]
            print(f"[DRY RUN] ControlPanel files (src/lib/components/ControlPanel/): {len(cp_files)}")
            for cf in sorted(cf_files):
                print(f"  - {cf.name}")

        run_server_file = project_root / "run_server.py"
        if run_server_file.exists():
            print(f"[DRY RUN] Dual-server entry: {run_server_file.name} (1 file)")

        print(f"[DRY RUN] Mode: {mode} | Fast: {fast_mode} | Output: {project_root / 'blueprints' / p_name / timestamp()}")
        return

    print(f"\nGenerating {mode} blueprint in: {project_root / 'blueprints' / p_name / timestamp()}")
    total_files, total_tokens = generate_blueprints(
        project_root, project_root / "blueprints" / p_name / timestamp(), mode,
        not args.no_row_counts,
        fast_mode=fast_mode,
        project_name=p_name
    )

    if mode == "update_only":
        remove_unchanged_files(project_root / "blueprints" / p_name / timestamp())

    if upload_uuid:
        print(f"\n[UPLOAD] Upload to xAI Collections not yet implemented — placeholder for future agentic pipeline. Collection UUID: {upload_uuid}")

    print(f"\nDone! Blueprint created at:\n{project_root / 'blueprints' / p_name / timestamp()}")
    print(f" Total files scanned: {total_files}")

if __name__ == "__main__":
    main()
```

---

**End of Report** (with full script source appended for Grok Heavy reference).
