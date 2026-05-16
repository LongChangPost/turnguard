# Publishing Checklist

Use this file before publishing TurnGuard.

## GitHub Repo

Recommended repo name:

```text
turnguard
```

Recommended description:

```text
A tiny conversation-routing skill for interrupted AI agent sessions.
```

Recommended topics:

```text
ai-agents, agent-skills, claude-code, codex, hermes-agent, openclaw, prompt-engineering, ai-workflows
```

Recommended visibility:

```text
public
```

## Files To Publish

Publish the contents of this folder:

```text
README.md
README.zh-CN.md
examples.md
LICENSE
.gitignore
skills/turnguard/SKILL.md
launch-posts/x-thread.md
launch-posts/turnguard-x-cover.png
PUBLISHING_CHECKLIST.md
```

Do not publish local downloads, migration packages, staging folders, `.env`, `auth.json`, logs, caches, or any old Hermes files.

Keep these local unless they are rewritten into public-safe summaries:

```text
TURNGUARD_TEST_PROCESS.md
```

## Validation

Before publishing:

- `skills/turnguard/SKILL.md` validates as a skill.
- `name` is `turnguard`, lowercase and hyphen-safe.
- The repo does not contain secrets or old agent state.
- Confirm `https://github.com/MrHistoryLeo/turnguard` is live before publishing the X thread.

## Launch Order

1. Create public GitHub repo `turnguard`.
2. Upload this release folder's contents.
3. Confirm README renders correctly.
4. Confirm Hermes install command works or is documented correctly.
5. Confirm OpenClaw install path is documented correctly.
6. Copy the repo URL into the X launch post.
7. Publish the X thread.
8. Publish or submit to skill registries:
   - OpenClaw / ClawHub: `clawhub skill publish skills/turnguard`
   - Hermes: `hermes skills publish skills/turnguard --to github --repo MrHistoryLeo/turnguard`
9. Pin or bookmark the repo for future skill updates.
