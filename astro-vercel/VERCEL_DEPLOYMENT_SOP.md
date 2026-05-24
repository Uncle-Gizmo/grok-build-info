# VERCEL_DEPLOYMENT_SOP.md — Deploying Astro Research Sites on Vercel (Hobby Tier)

**Version:** May 2026  
**Focus:** Free / low-cost, GitHub-centric, preview-friendly static site hosting for public research/info hubs

## 1. Why Vercel for This Workflow

- **Best-in-class GitHub integration** for the "Content as Code" model: push to `main` → production deploy. Push to any branch or open PR → instant Preview Deployment with its own URL. This is gold for agentic workflows (review changes safely before merging).
- **Zero-config Astro detection**: Vercel automatically recognizes an Astro project, sets the correct build command (`npm run build`), output directory (`dist`), and framework preset.
- **Generous free Hobby tier** that is more than sufficient for research/blog sites (low-to-medium traffic, mostly static content).
- **Edge network** performance worldwide.
- **Excellent rollback story**: Every deployment is immutable. You can instantly promote any previous deployment to production or simply revert via git + push.
- **Free custom domains + automatic HTTPS** (with some practical limits on Hobby).
- **Preview deploys make collaboration and agent review trivial**.

Strong alternatives exist (Cloudflare Pages, Netlify, GitHub Pages, Render), but Vercel currently offers the smoothest experience when the primary content repo lives on GitHub and the primary maintainer/creator is a human + Grok Build pair.

## 2. Vercel Hobby (Free) Tier — What You Actually Get (2026)

**Key limits (most relevant to research sites):**
- **Bandwidth / Fast Data Transfer**: 100 GB per month. For a documentation/research blog with occasional video embeds and images, this is extremely generous. A popular personal site can easily stay well under this.
- **Edge Requests**: 1 million per month.
- **Function / Serverless** (if you later add any): 1 million invocations, short execution windows.
- **Concurrent builds**: 1 (Hobby). Fine for small projects.
- **Custom domains per project**: 50.
- **Static file uploads** via CLI: 100 MB per deployment (plenty for Markdown + images).
- No overage billing — if you hit a hard cap the affected feature pauses until the rolling window resets (usually 30 days for many metrics). For static sites this is rarely an issue.
- **Personal / non-commercial use** only. Commercial or revenue-generating use requires Pro.

**Real-world suitability**: Perfect for the intended use case (public research notes, architecture decisions, demo videos, cross-project hubs). Traffic will be modest. If a single project ever becomes very popular, upgrading one project to Pro ($20/mo with credits) is straightforward and can be done per-project.

**What is NOT free / limited on Hobby**:
- Team collaboration features.
- Higher build concurrency and longer function timeouts.
- Very high bandwidth or edge function heavy workloads.

## 3. Step-by-Step: First Astro Deployment to Vercel

### Prerequisites
1. Astro project initialized and building locally (`npm run build` succeeds, `dist/` folder appears).
2. Code committed and pushed to a **public** GitHub repository (or the repo you intend to make public). The publish-public-research skill is the recommended way to create and seed this repo with proper authorization.

### Deployment (Website UI — recommended first time)

1. Go to https://vercel.com/new
2. Import your GitHub repository (Vercel will request GitHub permissions — grant the minimum needed).
3. Vercel auto-detects:
   - Framework Preset: **Astro**
   - Build Command: `npm run build` (or `astro build`)
   - Output Directory: `dist`
   - Install Command: `npm install`
4. (Optional) Give the project a nice name.
5. Click **Deploy**.

First production deployment usually completes in under a minute for small Astro sites. You get a `*.vercel.app` URL immediately.

All future pushes to the Production Branch (usually `main` or `master`) trigger new Production Deployments.

### Using Vercel CLI (great for local/agent scripting later)

```bash
npm install -g vercel
vercel
# Follow prompts; choose "No" when asked to override settings (let auto-detection do its job)
```

## 4. Preview Deployments — The Killer Feature for Agentic Work

- Every push to a non-production branch creates a unique preview URL.
- Every Pull Request gets its own isolated preview.
- This means: an agent (GB) can propose content changes, open a PR (or the human does), and both parties review the exact rendered site before merging. Zero risk to production.

This workflow pairs beautifully with the publish-public-research skill and GitHub Issues as context store.

## 5. Project Configuration Details

**No `vercel.json` needed for basic static Astro sites.** The defaults are excellent.

You only create `vercel.json` when you need custom headers, redirects, or more advanced rewrites.

**For on-demand/SSR rendering (future, not recommended yet):**
- Run `npx astro add vercel` (installs the adapter and updates `astro.config.mjs`).
- This enables serverless functions on Vercel. Hobby limits apply to functions.

For pure research/info sites: **stay static**.

## 6. Custom Domains & HTTPS

- Hobby supports up to 50 domains per project.
- Adding a custom domain is done in the Vercel dashboard (Project → Settings → Domains).
- Vercel automatically provisions and renews Let's Encrypt certificates.
- For apex domains you will usually need to configure an A record or use a CNAME/apex handling provider (Vercel gives clear instructions).

**Note**: Some users report occasional friction or surprise billing on custom domains with very high traffic or certain configurations. For low-traffic research sites this is almost never an issue. Monitor usage in the dashboard.

## 7. Rollback, History & Recovery

**Best rollback methods (in order of preference):**

1. **Instant promote** (Vercel dashboard): Go to Deployments → select any previous successful deployment → "Promote to Production". Takes seconds. No code change required.
2. **Git revert + push**: Revert the offending commit(s) locally, push. Triggers a clean new production deployment from the good state.
3. **Redeploy specific commit** from the Vercel dashboard.

Every deployment is immutable and retains its own assets and logs. This is one of Vercel's strongest operational advantages.

## 8. Environment Variables

Rarely needed for pure static research sites.

When you do need them (e.g., a public analytics token or future lightweight integration):
- Project → Settings → Environment Variables
- Available in `import.meta.env` at build time (and runtime if using the adapter).
- Keep secrets out of the public repo.

## 9. Monitoring, Analytics & Logs

- Basic deployment logs and build logs are free and excellent.
- Real-time traffic / usage graphs in the dashboard (bandwidth, requests, etc.).
- For deeper analytics on a research site, consider privacy-friendly options (Plausible, Umami self-hosted, or simple serverless beacon) rather than heavy commercial scripts.

## 10. When (and Why) to Upgrade to Pro

Upgrade a specific project (not your whole account) when:
- You consistently approach or hit 100 GB bandwidth.
- You need team seats / collaboration features.
- You want higher build concurrency or longer function execution windows.
- You need more than 50 custom domains on one project.

Pro starts at $20 per seat per month and includes a $20 usage credit. For most research/info use cases you will never need it, or you will need it on only one high-traffic project.

## 11. Operational Checklist for New Public Info Projects

- [ ] Public GitHub repo created (via explicit authorization + publish-public-research skill or manual).
- [ ] Astro project initialized inside the repo with Content Collections.
- [ ] `README.md` at root acting as the human- and agent-readable hub.
- [ ] First content added with standardized frontmatter.
- [ ] Local `npm run build` succeeds cleanly.
- [ ] Repo pushed.
- [ ] Imported into Vercel → first production deploy.
- [ ] Preview deployment tested on a branch/PR.
- [ ] (Optional) Custom domain added.
- [ ] Link back from private project (e.g., in its own README or AGENTS.md) to the new public hub.

## 12. Troubleshooting Tips

- Build fails on Vercel but succeeds locally → check Node version (Vercel defaults are modern; pin in `package.json` engines if needed), missing dependencies, or case-sensitivity issues on Windows vs Linux.
- Images or assets not appearing → ensure they are in `public/` (copied as-is) or correctly imported/optimized.
- Preview looks different from production → almost always a branch vs main content difference. Check the exact commit the preview is built from.
- Hitting a Hobby limit → the Vercel dashboard shows exactly which metric and when it resets.

## 13. References

- Official Astro + Vercel guide: https://docs.astro.build/en/guides/deploy/vercel/
- Vercel Hobby plan: https://vercel.com/docs/plans/hobby
- Vercel limits (detailed): https://vercel.com/docs/limits
- Vercel pricing overview: https://vercel.com/pricing

---

*Treat this SOP as the operational companion to ASTRO_SOP.md. Together they give both humans and agents everything needed to stand up and maintain excellent public research presences with minimal friction and maximum long-term maintainability.*
