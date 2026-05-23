# The Permission Mode Gotcha

**A critical and non-obvious difference between `grok -p` and `grok agent stdio`**

---

## The Problem

When your global `~/.grok/config.toml` has:

```toml
[ui]
permission_mode = "ask"
```

(or the older `yolo = false` equivalent), behavior differs significantly depending on how you invoke the agent.

### Regular Spawn Path (`grok -p`)

This works:

```bash
grok -p "Create a file..." --cwd /path/to/project --always-approve
```

The `--always-approve` flag **overrides** the global setting for that specific invocation.

### ACP Path (`grok agent stdio`)

This **does not work** the same way:

```bash
grok agent stdio --always-approve     # ERROR
```

`grok agent stdio` does **not accept** `--always-approve` (or similar permission flags) as a command-line argument.

The agent will respect whatever is set in your global config.

---

## What Actually Happened

We built a Python client that successfully completed the full ACP handshake:

- `initialize`
- `authenticate`
- `session/new`

We then sent a prompt asking the agent to create a file using its tools.

Because the global setting was `"ask"`, the agent started making tool calls (e.g. `fs/read_text_file`, `fs/write_text_file`) but could not execute them autonomously. It either waited for approval or was restricted.

Our client was not answering the tool requests (we hadn't implemented that part yet), so the `session/prompt` call eventually timed out — even though the handshake had succeeded.

Only after changing the global config to `always-approve` did the agent freely use its filesystem tools through the ACP protocol.

---

## Practical Implication

If you want autonomous/agentic behavior when using `grok agent stdio`, you currently have two options:

1. Set `permission_mode = "always-approve"` (or equivalent) in your global `~/.grok/config.toml`.
2. Implement the full tool-calling loop in your client and handle approvals yourself.

There is currently no per-invocation `--always-approve` flag for the stdio path.

---

## Recommendation

For development and research with `grok agent stdio`, it is usually easiest to set the global permission mode to always-approve, then rely on your own client code for any safety/approval logic you want to add on top.

This is different from the spawn path, where you can keep the global setting strict and selectively loosen it per run with `--always-approve`.

---

*Discovered during real implementation in May 2026 while building a working ACP client for the Chatter project.*