# PUBLISHING_WORKFLOW_AND_DECISION_NOTES.md

**Date:** 2026-05-24 (initial creation)  
**Context:** Chatter_Svelte project + emerging multi-project public research presence strategy

## 1. Executive Summary of the Decision

After evaluating GitHub's native web interface (Issues, Discussions, Wiki, Pages) for "imitating a blog" with YouTube videos and cross-project navigation, the team decided:

- GitHub is the **source of truth** and excellent for durable context (Issues with labels such as `project-context`).
- GitHub's **reading experience** for a polished, fast, discoverable public research/blog presence is mediocre.
- A dedicated **static site** (Astro) + excellent hosting (Vercel) gives a dramatically better reader experience while still treating GitHub as the canonical content repository ("Content as Code").
- **Astro** was selected over Hugo and Eleventy specifically because its modern component model and Content Collections produce **higher-quality, more reliably maintainable code** when the primary creator/maintainer is a Grok Build-style agent (LLM).
- The public layer will live in **separate repository/repositories** from the private engineering work. The `publish-public-research` skill formalizes the safe creation and seeding of these public repos.

## 2. Recommended End-to-End Publishing Workflow (Current Best Practice)

1. **Identify publishable material** inside a private project.
   - High-value architecture decisions, lessons learned, normalization plans, model UX improvements, etc.
   - YouTube demo videos (already public or newly recorded).
   - Never include secrets, internal credentials, or unreleased proprietary details.

2. **Use the publish-public-research skill** (or equivalent explicit process).
   - Clarify goal with user.
   - Propose public repo name (e.g., `chatter-svelte-info` or a master `context-forge-public`).
   - Propose initial topic subfolder(s).
   - **Mandatory explicit authorization gate** before creating any public GitHub repository.

3. **Initialize the public repo** (via skill or manual + GitHub MCP tools).
   - Strong root `README.md` acting as the hub.
   - Initial folder structure + placeholder content.
   - Recommended GitHub Topics and description for discoverability.

4. **Stand up the Astro site locally** (follow ASTRO_SOP.md).
   - Use Content Collections from day one.
   - Adopt the frontmatter conventions.
   - Add a reusable YouTube embed component.
   - Create clean layouts and a master navigation component that can later span multiple projects.

5. **Migrate / create initial content**.
   - Agent (GB) does the heavy lifting of converting existing `.md` files into properly frontmatter-ed posts.
   - Human reviews for tone, accuracy, and what must remain private.
   - Keep links back to the private repo where appropriate (e.g., "Full engineering context in the private `Chatter_Svelte` repository").

6. **Connect to Vercel** (follow VERCEL_DEPLOYMENT_SOP.md).
   - Import the GitHub repo.
   - First production deploy.
   - Verify preview deployments work on branches.

7. **Polish & launch**.
   - Custom domain (optional, cheap on Hobby).
   - Add RSS, sitemap, basic SEO.
   - Update the private project's own README / AGENTS.md with a prominent link to the new public hub.
   - Announce (if desired) via existing channels + the YouTube videos hosted in the content.

8. **Ongoing maintenance (agentic + human)**.
   - New research notes are drafted in the private project, then proposed for publication via the skill.
   - GB is routinely asked to "add this lesson to the public research hub", "create a video placeholder card", "refresh the master navigation across all projects", etc.
   - Every content change goes through normal git + preview deploy review.

## 3. Concrete First Content Candidates (from current Chatter_Svelte work)

High-signal, already-written material that maps beautifully to public research posts:

- `KILO_FIRST_ATTEMPT_LESSONS_LEARNED.md` → "Why we removed the first Kilo implementation and what we learned about tool orchestration"
- `GENERIC_PROJECT_CONTEXT_PATTERN.md` → "Using GitHub Issues as the durable source of truth for project context"
- `MESSAGE_TAGGING_NORMALIZATION_PLAN.md` (and the final implementation notes) → "Full-stack message tagging with bulk operations in a Svelte + PostgreSQL app"
- Model selector / friendly name improvements
- The aggressive historical + Kilo cleanup story (as a cautionary and methodological tale)
- Any future YouTube demos of the tagging UI or model UX

These can be turned into a small "Architecture & Lessons" series inside the public hub.

## 4. Master Navigation / Cross-Project Vision

Long-term the public presence should feel like a coherent body of work rather than N disconnected blogs.

- One master hub repo (or a small number of them) with clear top-level navigation: "Projects", "Patterns", "Videos", "Research Notes".
- Each project gets its own subfolder or dedicated section.
- Shared components (navigation, footer, search, tag cloud) live in the hub and are easy for GB to update across the site.
- Consistent visual identity and URL structure.

Start simple (one project) and evolve the structure as the second and third projects join.

## 5. Risk Mitigation & Guardrails

- **Authorization**: The publish-public-research skill and these SOPs both insist on explicit "yes" before any public repository is created. This is non-negotiable.
- **Content hygiene**: Maintain a clear mental (and sometimes documented) boundary between private engineering detail and public research narrative.
- **Attribution & links**: Always give readers a way back to the primary private repositories when relevant.
- **Cost control**: Stay on Hobby until a clear, data-backed reason to upgrade a specific project. Monitor the Vercel dashboard usage graphs monthly at first.
- **Discoverability**: Manually add good GitHub Topics and optimize READMEs (MCP tools currently do not expose topic setting).

## 6. Evolution Path

These SOPs and the folder itself are expected to evolve.

Possible future enhancements:
- A small companion skill that automates more of the "draft public version of this private document" flow (once patterns are proven).
- Standardized Astro starter template pre-configured for research hubs (with the exact frontmatter schema, YouTube component, master nav, etc.).
- Automated cross-linking or search across multiple public info repos.
- Integration with the existing GitHub-Issues-as-context pattern (e.g., a public "related GitHub Issues" section generated from labels).

## 7. References & Related Artifacts

- `publish-public-research` skill (primary action layer): `C:\Users\HP\.grok\skills\publish-public-research\SKILL.md`
- ASTRO_SOP.md and VERCEL_DEPLOYMENT_SOP.md (this folder)
- Recent project documents that will become early public content:
  - KILO_FIRST_ATTEMPT_LESSONS_LEARNED.md
  - GENERIC_PROJECT_CONTEXT_PATTERN.md
  - MESSAGE_TAGGING_NORMALIZATION_PLAN.md
  - AGENTS.md, FIXES.md, CLEANUP_LOG.md (selected excerpts)
- GitHub Issues (primary durable context store going forward)

---

**This document exists so that future humans and agents have the full "why" and the current best "how" when they want to give any of our projects a beautiful, fast, public research presence without ever compromising the privacy of the engineering work.**

Update this file whenever the workflow, tooling, or rationale changes significantly.
