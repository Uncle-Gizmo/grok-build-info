# Astro + Vercel — Public Research / Info Publishing Hub

**Purpose of this folder**

This folder contains high-signal reference documentation (SOPs) for building and maintaining a public-facing research, documentation, and "blog-style" information presence using **Astro** (static site generator) + **Vercel** (hosting + deployment).

The system is designed specifically for the user's multi-project workflow:

- Main engineering repositories (e.g. `Chatter_Svelte`) remain **private**.
- A separate lightweight **public "info/research" GitHub repository** acts as the source of truth for public content ("Content as Code").
- Astro builds beautiful, fast, Markdown-first static sites from that content.
- Vercel provides zero-config GitHub-connected deployments with excellent preview workflows.
- Grok Build (GB) / agentic patterns are first-class citizens for ongoing generation, migration, editing, and maintenance of public content.
- A master navigation / hub experience can span multiple projects over time.
- YouTube videos, research notes, architectural decisions, lessons learned, and guides can be published while preserving the private engineering context.

**SOPs vs Skills decision (May 2026)**

At this stage, the material here is intentionally maintained as **Standard Operating Procedures (SOPs)** rather than new executable skills.

- **Skills** (see the existing `publish-public-research` skill) are for repeatable, state-changing, tool-orchestrated actions that typically require explicit user authorization (e.g., actually creating a new public GitHub repository or pushing content).
- **SOPs** are durable, high-signal reference documentation — the "why", the "how we do it here", recommended structures, frontmatter conventions, agent collaboration patterns, and decision rationale. They are the equivalent of `AGENTS.md`, `GENERIC_PROJECT_CONTEXT_PATTERN.md`, `MESSAGE_TAGGING_NORMALIZATION_PLAN.md`, etc. in active projects.

These SOPs can be read by both humans and agents (Grok Build) to produce consistent, high-quality public output. If stable patterns emerge, selected automation may later be extracted into a dedicated skill.

**Core principles (aligned with publish-public-research skill)**

- Explicit authorization required before creating any public repository or publishing sensitive material.
- GitHub remains the single source of truth for public content (Markdown + frontmatter + assets).
- Separate public info repo(s) per major initiative or one master hub repo with topic/project subfolders.
- Astro's modern component model + Content Collections chosen specifically because it produces higher-quality, more reliably editable code when collaborating with LLM agents than alternatives (Hugo Go templates, Eleventy data cascades, etc.).
- Vercel chosen for seamless GitHub → preview → production flow with excellent DX.

**Documents in this folder**

- [ASTRO_SOP.md](./ASTRO_SOP.md) — Deep reference on Astro for this use case (why chosen, setup, Content Collections, frontmatter conventions, YouTube/media, agentic GB patterns, recommended structures).
- [VERCEL_DEPLOYMENT_SOP.md](./VERCEL_DEPLOYMENT_SOP.md) — Vercel Hobby (free) tier, Astro-specific deployment, GitHub integration, previews, rollback, limits, and when to consider upgrades.
- This README — orientation and decision context.

**Status (as of May 2026)**

- Research + SOP creation phase complete.
- Actual public repository creation and first content publishing will use the `publish-public-research` skill (or direct GitHub MCP tools) and will require explicit user confirmation at each major step.
- Existing high-value Markdown assets from private projects (plans, lessons learned, architecture notes, etc.) are prime candidates for controlled migration once the public hub structure is approved.

**Related patterns in the broader ecosystem**

- GitHub Issues used as durable project context store (labels such as `project-context`).
- Aggressive preference for minimal local "always-read" files + rich GitHub-backed history.
- Content-as-Code philosophy across the board.

**Next actions (when ready)**

1. Review the two SOPs.
2. Decide on the name and scope of the first public info repo (single-project vs master hub).
3. Use the publish-public-research skill (or equivalent) with explicit authorization to initialize the repo + initial structure.
4. Begin controlled migration of selected public-facing documents (with proper frontmatter).

These SOPs are living documents. Update them as the workflow matures.

---

*Maintained for Grok Build / agentic + human collaboration on public research publishing.*
