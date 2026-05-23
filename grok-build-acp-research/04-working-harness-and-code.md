# The Working ACP Harness

**A practical, reusable Python client for `grok agent stdio`**

---

## Overview

The harness lives in the [Chatter Svelte](https://github.com/Uncle-Gizmo/chatter-svelte) repository at:

```
experiments/coalface/005_acp_client_harness/test_acp_client.py
```

It is a self-contained, well-logged Python client that can drive Grok Build through the official Agent Client Protocol and achieve real autonomous work (file creation, etc.).

---

## Core Features

- Uses the project's Secure Vault for the `xai_api_key`
- Proper JSON-RPC request/response correlation using `asyncio.Future`
- Handles the full bidirectional flow (client → agent **and** agent → client tool calls)
- Implements basic filesystem tools (`read_text_file`, `write_text_file`, `list_directory`)
- Very rich structured logging on every protocol message and tool interaction
- Clean startup, early death detection, and shutdown

---

## How to Run It

```powershell
cd experiments\coalface\005_acp_client_harness
python test_acp_client.py
```

**Requirements:**
- `grok` CLI on your PATH
- `xai_api_key` available via Secure Vault (or modify the code)
- Global `~/.grok/config.toml` with `permission_mode = "always-approve"` (recommended for agentic behavior)

---

## Key Design Points

### Bidirectional Handling
The client must be able to receive and answer tool requests from the agent, not just send prompts and read streaming text.

### Logging
Every tool request, success, failure, and response is explicitly logged. This was invaluable during development.

### Tool Implementation
The client actually executes the filesystem operations locally when the agent requests them. This is what allows true autonomous file creation through the protocol.

---

## Current Limitations (as of May 2026)

- Only implements a minimal set of filesystem tools
- Long-running sessions still use a simple sleep + verify approach (could be improved with better stabilization detection from notifications)
- No terminal/shell tool support yet

---

## Future Potential

This client (or a cleaned-up version of it) can serve as:

- A reference implementation for other projects
- The foundation for an `ACPExecutionBackend` in the main Chatter codebase
- A starting point for building more sophisticated agent platforms with strong observability and control

---

The existence of a working harness proves that `grok agent stdio` can be used for real agentic work — it just requires treating it as a proper protocol rather than a fancy CLI.