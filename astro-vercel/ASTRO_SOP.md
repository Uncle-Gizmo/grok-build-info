# ASTRO_SOP.md — Astro for Public Research & Multi-Project Info Hubs

**Version:** May 2026 (Astro 6.3+)  
**Audience:** Human + Grok Build (GB) agentic workflows  
**Scope:** Public research/blog/info sites that keep engineering repos private

## 1. What is Astro and Why It Fits This Project

Astro is a modern JavaScript web framework optimized for **content-driven websites** (blogs, documentation, marketing sites, research hubs, portfolios).

**Key characteristics (current as of 2026):**
- **Server-first + Islands architecture**: Renders to lightweight HTML by default. Zero JavaScript shipped unless explicitly requested via `client:*` directives. Outstanding Core Web Vitals and performance (real-world data shows Astro sites significantly outperform most alternatives on mobile).
- **Content-first**: Native, excellent support for Markdown and MDX with frontmatter. Designed to pull content from the filesystem, remote sources, or CMS.
- **Content Collections** (core recommendation): Type-safe, schema-validated collections of Markdown/MDX. Provides validation, TypeScript intellisense, optimized querying, and excellent error messages. Far superior to raw `import.meta.glob` for maintainable research/blog content.
- **File-based routing** with flexible patterns (`[slug].astro`, etc.).
- **Zero lock-in**: Works with React, Svelte, Vue, Solid, Preact, etc. (islands). Since the primary engineering work here uses Svelte, Astro + Svelte islands is a natural fit when light interactivity is desired.
- **AI-ready**: Official MCP server + context files for AI coding tools. Explicitly designed for high-quality collaboration with agents.
- **Other production features**: View Transitions (native browser API), optimized image handling, middleware, Actions, environment variables, dev toolbar, prefetching.
- **Fully static by default** → perfect for simple, fast, cheap, reliable research sites. Optional adapter for SSR/on-demand rendering when needed.
- **Free & open source** (MIT license). No subscriptions, usage limits, or paid tiers from Astro itself.

**Setup (standard, repeatable)**

```bash
npm create astro@latest
# Follow prompts (TypeScript recommended, strict mode, install deps, git init)
cd your-project
npm run dev          # Local development (http://localhost:4321)
npm run build        # Produces production-ready static output in ./dist
```

The `dist/` folder contains everything needed for any static host.

## 2. Why Astro Was Chosen Over Alternatives (Hugo, 11ty, Next.js, etc.)

**Decision context (May 2026, Chatter_Svelte public presence discussion):**

- **Hugo**: Extremely fast for very large content volumes. Uses Go templates. These templates are unfamiliar to most LLMs and produce lower-quality, harder-to-edit code during agentic sessions. Less "native" feeling for modern JS/TS developers and agents.
- **Eleventy (11ty)**: Minimalist, Markdown-friendly, data-cascade model. The cascade and Nunjucks/Liquid templating again create friction for reliable LLM generation and long-term maintenance by agents.
- **Next.js**: Overkill for pure content/research sites. Requires more JavaScript, more complex mental model, and usually server/SSR considerations. Excellent when heavy React interactivity or complex data fetching is core, but not the primary need here.
- **Astro**: Wins decisively for **agentic collaboration quality**. Modern `.astro` component syntax is close to HTML + familiar JS/TS. Content Collections give structured, type-safe data that agents handle reliably. File-system routing is obvious. The output is clean, semantic, and easy for both humans and future agents to edit or extend. Strong native Markdown + frontmatter story. Explicit "AI-Ready" positioning.

**Long-term rationale**: The public info layer will be maintained over years alongside private engineering work. Using a tool that produces *high-quality, low-surprise code* when an agent (GB) is asked to "add a new research post", "migrate the Kilo lessons learned", "update the master navigation", or "add a YouTube embed for the tagging demo" is the highest-leverage decision. Astro was selected specifically for this marriage with Grok Build workflows.

## 3. Recommended Repository Structure (Public Info Hub)

Two common patterns:

**A. Single-project public info repo** (start here for Chatter_Svelte or first project)
```
my-project-info/
├── README.md                 # Hub / landing page with project overview + links
├── astro.config.mjs
├── package.json
├── src/
│   ├── content/
│   │   └── research/         # or posts/, guides/, architecture/, etc.
│   ├── pages/
│   │   ├── index.astro
│   │   └── research/
│   │       └── [slug].astro  # or use Content Collections getStaticPaths
│   ├── layouts/
│   ├── components/
│   └── styles/
├── public/                   # images, videos (or reference YouTube), fonts
└── ...
```

**B. Master multi-project hub repo** (long-term goal)
```
grok-build-info/ or context-forge-info/
├── README.md                 # Master index / navigation across all projects
├── src/content/
│   ├── chatter-svelte/       # or by topic: research/, architecture/, videos/
│   ├── another-project/
│   └── shared/               # cross-project patterns (e.g. generic context)
└── ...
```

Subfolders per major topic or per project. GitHub Topics + README keywords for discoverability.

**Content Collections config example** (highly recommended):

```ts
// src/content/config.ts
import { defineCollection, z } from 'astro:content';

const research = defineCollection({
  type: 'content',
  schema: z.object({
    title: z.string(),
    description: z.string().optional(),
    pubDate: z.date(),
    updatedDate: z.date().optional(),
    tags: z.array(z.string()).default([]),
    project: z.string().optional(),           // e.g. "Chatter_Svelte"
    youtubeId: z.string().optional(),         // for featured video
    draft: z.boolean().default(false),
    // Add any other frontmatter fields you standardize on
  }),
});

export const collections = { research };
```

Querying becomes type-safe and delightful for agents and humans.

## 4. Frontmatter Conventions & Content Authoring

Standardize early. Suggested minimal set for research/blog posts:

```yaml
---
title: "Message Tagging Normalization — Full-Stack Implementation"
description: "How we moved from client-only tags to a production-grade PostgreSQL-backed system with bulk operations and filtering."
pubDate: 2026-05-24
updatedDate: 2026-05-25
tags: ["svelte", "tagging", "architecture", "normalization"]
project: "Chatter_Svelte"
youtubeId: "dQw4w9wgxcQ"   # optional featured video
draft: false
---
```

**Additional useful fields** (evolve as needed):
- `series`, `order`, `relatedPosts`, `githubIssue`, `commitHash`, `lessonsLearned`

Use Markdown (or MDX when you need components inside content) for the body. Keep posts self-contained but reference private engineering context with care (never leak secrets or internal-only details).

## 5. YouTube Videos, Embeds & Media

Astro handles this extremely well:

- Simple responsive YouTube embed using a reusable Astro component (recommended over raw iframe in every post).
- Example pattern: create `src/components/YouTubeEmbed.astro` that accepts `id` and `title`.
- MDX posts can import and use the component directly.
- Store video metadata in frontmatter (`youtubeId`).
- Thumbnail strategy: use YouTube's `https://img.youtube.com/vi/ID/hqdefault.jpg` or host optimized images in `public/`.
- For long-term ownership, consider downloading key videos or using a reliable host, but linking public YouTube is usually sufficient and low-maintenance.

## 6. Agentic Grok Build (GB) Collaboration Patterns

This is the killer feature for our workflow.

**High-value agent tasks (documented patterns):**
- "Create a new research post from the following private notes. Generate appropriate frontmatter, add to the correct collection, and update the index page."
- "Migrate the three most recent high-value Markdown documents from the private repo (KILO_FIRST_ATTEMPT_LESSONS_LEARNED.md, MESSAGE_TAGGING_NORMALIZATION_PLAN.md, GENERIC_PROJECT_CONTEXT_PATTERN.md) into the public hub under `chatter-svelte/architecture/`. Produce clean frontmatter and a summary post."
- "Add a 'Videos' section to the master hub. Create placeholder cards that will later link to YouTube demos of the tagging system and model selector improvements."
- "Update the root README.md with the latest project status and add a new navigation item for the public info hub."
- "Run a consistency pass: ensure all posts have `project` and `tags`, fix any broken internal links, and regenerate the RSS feed if present."
- "Propose a new subfolder structure for a second project joining the hub."

**Best practices when working with GB:**
- Keep the public repo's `src/content/` structure clean and predictable.
- Prefer small, focused changes with clear commit messages.
- Use Content Collections — the schema acts as a contract that helps the agent stay consistent.
- For bulk migrations, the agent can generate many `.md` files locally; the human reviews and the publish-public-research skill (or direct push) moves them.
- Always have the agent output a short "changes made + rationale" summary.

## 7. Themes, Starters & When to Use What

- Start with the official blog / docs starter or a lightweight theme (Astro Nano, Microblog, OpenBlog, etc.) and heavily customize — agents are excellent at this.
- Starlight (official docs theme) is outstanding if the primary output is documentation rather than a casual research blog.
- Avoid heavy themes if the goal is long-term agent maintainability — simpler bases win.

## 8. Performance, SEO, Accessibility

- Default Astro output is already excellent.
- Add `astro-seo` or manual meta tags in layouts.
- Sitemap, RSS (via `@astrojs/rss`), and robots.txt are trivial.
- Accessibility: standard semantic HTML + the fact that you ship very little JS helps enormously.

## 9. When to Extend Beyond Pure Static

- Need auth-protected pages or user-specific content? Add the Vercel adapter + on-demand rendering (see VERCEL_DEPLOYMENT_SOP).
- Need a lightweight backend? Astro Actions or integrate with your existing backend.
- For now: stay static. The research/info use case is an almost perfect fit for static + edge hosting.

## 10. Living Document

Update this SOP whenever the workflow, tooling, or decision rationale evolves. Reference specific commit hashes or GitHub Issues where major changes were discussed.

**Key references (official, keep current)**
- https://astro.build/
- https://docs.astro.build/en/getting-started/
- https://docs.astro.build/en/guides/content-collections/
- https://docs.astro.build/en/guides/deploy/vercel/
- Astro's AI/MCP guidance in the docs.

---

*This SOP exists to make high-quality public research publishing fast, consistent, and delightful for both humans and Grok Build agents.*
