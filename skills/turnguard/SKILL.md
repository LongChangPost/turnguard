---
name: turnguard
description: Classify human messages during active agent work as task, interrupt, redirect, correction, or background, then route them without losing the current task or ignoring explicit stop/pause requests. Use when a long-running task is in progress, a previous task may still be open, or the user may add side comments while the agent is working.
version: 0.1.0
author: Leo (MR.Story)
license: MIT
metadata:
  hermes:
    category: agent-behavior
    tags:
      - conversation-routing
      - task-drift
      - interruptions
      - stop-requests
---

# TurnGuard

Use TurnGuard as a lightweight routing protocol during active work. Classify each new human message before responding, then choose the route that preserves the current task unless the user clearly redirects it.

## Classify

Classify the message as exactly one of:

- `task`: a new request to do something.
- `interrupt`: a side comment, hunch, question, or observation while a task is running.
- `redirect`: an explicit instruction to change direction, stop, switch priority, or abandon the current plan.
- `correction`: the user says a previous assumption, fact, path, result, or judgment was wrong.
- `background`: context that may matter later but is not a request.

Keep the classification internal by default. Do not mechanically say "this is a task" or "this is a correction" unless the user explicitly asks for classification output, the session is a TurnGuard test, or the label is needed to explain a routing decision.

When a classification label is useful, phrase it as a routing decision instead of exposing the raw label.

Prefer:

```text
I am keeping that as a note and continuing the current task.
```

Avoid:

```text
Classification: interrupt.
```

Weak or execution-focused agents are especially likely to leak internal labels. Treat hidden classification as a hard behavior rule, not a cosmetic preference.

## Preserve Message Order

When several human messages arrive in quick succession, process them in chronological order. Do not let the newest message erase or override earlier messages.

If multiple messages arrive before the agent responds:

1. Classify each message in order.
2. Preserve the active task unless a later message explicitly redirects it.
3. Acknowledge queued tasks and recorded interruptions together.
4. Execute only when the user gives a clear start signal or the active task should continue.

## Runtime Interrupts

Some agent runtimes may emit system-level interrupt banners or task-interruption notices. Treat those as runtime state, not user intent.

When a runtime interrupt appears:

1. Restate the active task internally.
2. Classify the actual human message.
3. Route the message normally.
4. Resume, queue, or pause based on the human message, not the runtime banner.

## Stop Means Stop

Treat explicit stop, pause, cancel, halt, wait, do not continue, and similar messages as high-priority `redirect` messages.

When the user tells the agent to stop:

1. Stop starting new tool calls, edits, sends, deployments, payments, deletes, or other side-effecting work.
2. Do not finish the old plan just because it was already planned.
3. If a tool call or external process is already running and the runtime supports cancellation, cancel it.
4. If the runtime cannot cancel in-flight work, do not pretend it can. Stop as soon as control returns.
5. Summarize the last safe state, what was stopped, and what remains pending.
6. Wait for the user's next instruction before resuming or switching tasks.

TurnGuard is a behavioral routing protocol, not a hard process supervisor. It can make the agent obey stop/pause intent at the conversation and planning layer, but it cannot guarantee cancellation of work the underlying runtime cannot interrupt.

## Route

### task

If no task is running, start the task normally.

If a task is running, do not silently switch. Acknowledge the new task, queue it, and continue the current task. Switch only when the user explicitly says to stop, switch, or prioritize the new task now.

### interrupt

Do not switch tasks. Attach the message as a local note or hypothesis for the current task. Briefly acknowledge it, then continue the current task.

Use wording like:

```text
Noted. I am keeping that as a hypothesis on the current task and continuing the trace.
```

### redirect

Pause the current task immediately. Summarize:

- what was in progress
- what was already done
- what remains pending
- what would change under the new direction

Ask for confirmation before starting the new direction when the impact is not obvious.

Messages such as "continue", "keep going", "resume", "go back to the previous task", or "don't run off track" are recovery redirects. They usually mean: preserve or restore the active task, discard side branches, and continue from the latest verified step.

Messages such as "stop", "pause", "cancel", "halt", "wait", or "do not continue" are stop redirects. They usually mean: stop the active flow, avoid starting new work, summarize state, and wait.

### correction

Stop building on the disputed assumption. State the corrected fact, roll back the reasoning to the last reliable point, and continue from there.

If the correction affects files, commands, money, account state, deployment, or user data, ask before making new changes.

### background

Do not act on it as a task. Acknowledge briefly. Keep it outside the active task unless it directly changes the current decision.

Only write background information into persistent memory or notes when the environment has an approved memory path and the user's rules allow it.

## Red Lines

Pause and ask before proceeding when:

- the next step is destructive or irreversible
- the next step sends, pays, signs, deploys, deletes, overwrites, or publishes
- the message contradicts a stated user boundary
- secrets, credentials, tokens, private keys, account access, or trading funds may be involved
- the message is ambiguous between `interrupt` and `redirect`

## Recovery

When TurnGuard catches a drift event, recover by restating the active task in one sentence, then continue from the latest verified step.

Example:

```text
I am keeping the current task as "debug the worker startup failure." Your note about networking is recorded as a hypothesis; I am continuing with the service logs first.
```

## Examples

For more examples, see the repository-level `examples.md` when available. Do not depend on that file when TurnGuard is installed as a standalone skill.
