# The Journey: Building a Working Grok Build ACP Client

**From broken handshake to real autonomous file creation**

---

## Background

We wanted to explore the *official* way of driving Grok Build from another program: `grok agent stdio`, which uses the Agent Client Protocol (ACP).

Most of our existing agentic work used the "spawn" approach (running `grok -p ...` via Git Bash). This works well, but we wanted to understand the protocol path properly.

---

## What Went Wrong (The Main Issues)

### 1. Hard-coded Session ID

Early versions of the client would successfully call `session/new`, but then hard-code the `sessionId` from a previous run when calling `session/prompt`.

Result: "unknown session id" errors.

**Lesson:** You must dynamically parse and store the `sessionId` returned by `session/new`.

### 2. `--always-approve` Is Not Supported on `stdio`

We tried passing `--always-approve` when spawning `grok agent stdio`.

The process died immediately with:

```
error: unexpected argument '--always-approve' found
```

Unlike the regular `grok -p` path, the stdio subcommand does not accept this flag.

### 3. The Bidirectional Nature of ACP (The Real Blocker)

Even after fixing the above, `session/prompt` would time out.

The agent was alive and reasoning (we saw `tool_call_delta_chunk` notifications), but it was sending tool requests *back to our client* (`fs/read_text_file`, `fs/write_text_file`, etc.) and waiting for responses.

Our initial client only knew how to send requests and receive responses/notifications. It had no concept of the agent sending *us* requests.

Until we implemented proper tool request handling + responses, the agent was blocked and the prompt call would eventually time out.

---

## The Breakthrough

Once we implemented:

- A proper pending request map using `asyncio.Future`
- A background reader that could distinguish responses, notifications, *and* incoming requests
- Handlers for `fs/read_text_file`, `fs/write_text_file`, and directory listing
- Rich logging on every tool interaction

...the agent was finally able to complete real work and create files autonomously.

**Proof:** A timestamped file `test_acp_005_success_*.txt` was successfully written by the agent through the official protocol.

---

## Key Takeaways

- ACP is a true bidirectional protocol. Treating it as "fire a prompt and read stdout" will fail once the agent starts using tools.
- The permission model works differently than the spawn path (see the dedicated permission gotcha document).
- Rich logging made debugging dramatically easier.
- The final client is now a solid reference implementation.

---

This research was done as part of the [Chatter Svelte](https://github.com/Uncle-Gizmo/chatter-svelte) project in May 2026.