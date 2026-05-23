# GROK BUILD INFO

**Public research, practical guides, and implementation notes for Grok Build (Grok CLI) in agentic, headless, and programmatic scenarios.**

This repository collects real-world research on using **Grok Build** with the **Agent Client Protocol (ACP)**, `grok agent stdio`, and related agentic integration patterns.

Keywords: Grok Build, Grok CLI, Grok agent, Agent Client Protocol, ACP, grok agent stdio, xAI agent, headless Grok, programmatic Grok, agentic coding.

---

## Contents

### Current Research

- **[grok-build-acp-research](./grok-build-acp-research)**  
  In-depth exploration of `grok agent stdio` (the official Agent Client Protocol). 
  Covers the full implementation journey, critical gotchas (especially the `permission_mode` difference), Spawn vs ACP decision framework, and a working reference Python client.

### Planned / Future Topics

- Advanced Grok Build agent orchestration patterns
- Safety, sandboxing, and observability techniques
- Comparisons between spawn-based and protocol-based approaches
- Tooling and prompting strategies for long-running agents

---

## Purpose

Most people interact with Grok Build using the simple spawn approach (`grok -p ... --always-approve`).

This repo focuses on the **official programmatic path** — `grok agent stdio` — which speaks the Agent Client Protocol. It documents hard-won lessons from building real bidirectional clients.

All material here comes from actual working code rather than theory.

---

## Searchability & Topics

This repository is tagged with the following topics for better discoverability:

`grok` `grok-build` `grok-cli` `agent-client-protocol` `acp` `grok-agent-stdio` `xai` `agentic` `headless` `research`

---

## Related Projects

- [Chatter Svelte](https://github.com/Uncle-Gizmo/chatter-svelte) — The main project where this research originated.

---

**Feel free to open issues with questions, corrections, or suggestions for new research topics.**