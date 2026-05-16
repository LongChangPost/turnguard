# TurnGuard

> A tiny conversation-routing skill for interrupted AI agent sessions.

People interrupt. Most agent setups treat every new human message as a new task. That default turns a casual side comment into task drift, context pollution, retry storms, and runaway token bills.

TurnGuard is the smallest useful fix: classify before responding, then route.

## What It Does

TurnGuard asks an AI agent to classify each incoming human message during active work as one of:

- `task`: a new thing to do
- `interrupt`: a side comment while work is already running
- `redirect`: an explicit change of direction
- `correction`: the user says the agent got something wrong
- `background`: useful context, not a request

Then it routes the message without losing the current task.

## Why It Matters

Long-running AI work is fragile. A user may add a thought, correction, or side question while the agent is editing files, debugging, researching, or coordinating tools. Without turn routing, the agent may silently switch tasks, forget the original goal, or pollute memory with half-formed context.

TurnGuard keeps three lanes separate:

- Current task
- Interruption buffer
- Task queue

It also handles five common real-world failure modes:

- Back-to-back user messages must be processed in order, not collapsed into the newest one.
- Runtime interrupt banners are not user intent; the agent should route the actual human message.
- "Continue", "resume", and "do not run off track" are recovery redirects that restore the active task.
- "Stop", "pause", "cancel", and "do not continue" are stop redirects; the agent should stop starting new work, summarize state, and wait.
- Classification labels should usually stay hidden from the user; TurnGuard is a routing behavior, not a UI mode.

## Install

TurnGuard is a plain `SKILL.md` package with no scripts, no dependencies, and no required secrets. It should work in any agent runtime that supports `SKILL.md` style skills.

### Claude Code

```bash
cp -r skills/turnguard ~/.claude/skills/
```

### Codex

```bash
cp -r skills/turnguard ~/.codex/skills/
```

### Hermes Agent

```bash
cp -r skills/turnguard ~/.hermes/skills/
```

After this repo is public, Hermes users can also install directly from GitHub:

```bash
hermes skills install MrHistoryLeo/turnguard/skills/turnguard
```

### OpenClaw

```bash
cp -r skills/turnguard ~/.openclaw/skills/
```

OpenClaw workspace install:

```bash
cp -r skills/turnguard /your/workspace/skills/
```

### Project-local install

```bash
cp -r skills/turnguard /your/project/.codex/skills/
```

## Compatibility

TurnGuard is designed for Claude Code, Codex, Hermes Agent, OpenClaw, and other `SKILL.md`-compatible runtimes. It is intentionally instruction-only: no shell commands, no network calls, no credentials, and no runtime-specific tool assumptions.

## Usage

Use TurnGuard whenever an agent is in the middle of work and the user may add more messages before the work is complete.

Example:

```text
User: I think it might be a network issue.
Internal classification: interrupt
Agent behavior: keep the note attached to the current diagnostic, but continue the original task.
```

More examples are in [examples.md](examples.md).

## Not A Security Sandbox

TurnGuard does not enforce permissions, prevent prompt injection, or protect secrets. It is a behavioral routing protocol for reducing task drift and context pollution.

TurnGuard also does not guarantee hard cancellation of an already-running tool call or external process. When the runtime supports cancellation, the agent should use it. When it does not, the agent should stop as soon as control returns and avoid starting any new work.

## Status

`v0`. Small on purpose. Feedback, forks, and real failure cases are welcome.

## License

MIT
