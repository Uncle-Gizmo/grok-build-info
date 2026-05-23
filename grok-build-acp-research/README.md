# grok-build-acp-research

**Public research and practical experience using the official Agent Client Protocol (`grok agent stdio`) with Grok Build.**

This is a focused sub-section inside the [GROK BUILD INFO](../) collection.

---

## Documents

| Document | Description |
|----------|-------------|
| [01 - Journey and Problems](./01-journey-and-problems.md) | The full story of building the ACP client, the bugs we hit, and how they were solved |
| [02 - The Permission Gotcha](./02-the-permission-gotcha.md) | Critical and non-obvious difference in how `permission_mode` works between the spawn path and `grok agent stdio` |
| [03 - Spawn vs ACP Decision Guide](./03-spawn-vs-acp-decision-guide.md) | Practical guide on when to use the simple spawn approach vs the full ACP protocol |
| [04 - Working Harness and Code](./04-working-harness-and-code.md) | The final reference implementation and how to run it |

---

## Background

Most people using Grok Build for agentic work rely on the "spawn" method (`grok -p "..." --always-approve`).

This repository documents the journey of exploring the **official** programmatic path: `grok agent stdio`, which speaks the Agent Client Protocol (ACP).

The research includes several important gotchas that are not well documented elsewhere (especially around permission handling and the bidirectional nature of the protocol).

---

## Status

- A working bidirectional ACP client has been validated
- Real autonomous file creation via `grok agent stdio` has been achieved
- All documents are based on actual implementation work (May 2026)

---

This work originated from the [Chatter Svelte](https://github.com/Uncle-Gizmo/chatter-svelte) project.