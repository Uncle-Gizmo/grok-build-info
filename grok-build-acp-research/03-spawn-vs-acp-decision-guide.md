# Spawn vs ACP: When to Choose Which Path

**A practical, opinionated guide for driving Grok Build agentically**

---

## The Two Main Approaches

### 1. Spawn Path (Current Default in Most Projects)

**Command style:**
```bash
grok -p "Your prompt here" --cwd /path/to/project --always-approve
```

Python spawns the CLI (usually via Git Bash on Windows), streams the output, and provides an outer safety/approval layer.

**This is what powers the main Coalface execution system in Chatter.**

### 2. ACP Path (`grok agent stdio`)

**Command style:**
```bash
grok agent stdio
```

Your program speaks the official **Agent Client Protocol** (JSON-RPC 2.0) over stdin/stdout. The agent can send tool requests back to you, and you decide what to do.

---

## Decision Guide

| Situation | Recommended Path | Why |
|-----------|------------------|-----|
| Daily agentic coding and file work in your own projects | **Spawn** (strongly preferred) | Simpler, more mature, matches how you normally use `grok`, lower maintenance |
| You want the client to see and control every individual tool call | **ACP** | Much stronger audit, logging, and safety boundary |
| You are building a rich UI that should show plans, diffs, or live tool activity | **ACP** | You get structured events instead of parsing stdout |
| You need to run in a high-security / regulated environment | **ACP** | You can implement your own sandbox and approval logic per operation |
| You want a clean, reusable reference implementation for other projects | **ACP** | Protocol is standard (in theory portable to other ACP agents) |
| Spawn path is throwing repeated errors on normal tasks | **Fix the spawn path first** | Do not switch to ACP just because the simpler path is currently broken |
| You want maximum power and controllability regardless of complexity | **ACP** | It is genuinely the more powerful architecture |

---

## Current Honest Recommendation (May 2026)

- **For normal, day-to-day agentic work** → Use the **spawn path**.
- **When you specifically need the extra controllability and observability** → Use (or add) the **ACP path**.

Do **not** choose ACP just because the spawn path is currently unreliable on a task. Fix the spawn approach first. Only move to ACP when the job itself benefits from the protocol-level advantages.

---

## Summary

| Path   | Power | Complexity | Maturity (in this project) | Best For |
|--------|-------|------------|----------------------------|----------|
| Spawn  | High  | Low        | High                         | Most real work today |
| ACP    | Very High | Medium-High | Medium (just validated)     | When you need control, visibility, or a platform |

Both approaches are valid. They solve slightly different problems.

The spawn path is currently the better default for most people.

The ACP path is the better choice when you want to be a first-class peer in the agent’s execution environment rather than just an observer.