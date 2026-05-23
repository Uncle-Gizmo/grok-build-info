# GROK BUILD INFO

**Public research, guides, and notes about Grok Build (Grok CLI) agentic usage, the Agent Client Protocol (ACP), and related topics.**

This repository serves as a public collection of practical research and documentation from real-world usage of Grok Build in agentic / headless / programmatic scenarios.

---

## Contents

### Current Topics

- **[grok-build-acp-research](./grok-build-acp-research)**  
  Deep dive into using `grok agent stdio` (the official Agent Client Protocol). Includes the full journey, key gotchas (especially the permission mode issue), decision guide (Spawn vs ACP), and a working reference implementation.

### Future Topics (Planned / Welcome)

- More Grok Build integration patterns
- Advanced prompting and agent orchestration techniques
- Tooling, safety, and observability approaches
- Comparisons with other agentic systems

---

## Purpose

This repo exists to share hard-won lessons from building real agentic systems on top of Grok Build, especially around the differences between the common "spawn" approach and the official ACP (`grok agent stdio`) path.

Everything here is based on actual implementation work rather than theory.

---

## Contributing / Suggestions

If you have questions, corrections, or additional research you'd like to see here, feel free to open an issue.

---

**Maintained as part of ongoing work on the [Chatter Svelte](https://github.com/Uncle-Gizmo/chatter-svelte) project.**