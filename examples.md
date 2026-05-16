# TurnGuard Examples

These examples show the intended classification and routing behavior.

| Human message | Situation | Class | Route |
| --- | --- | --- | --- |
| "I think it might be a network issue." | Debugging task is running | `interrupt` | Record as a hypothesis, continue the current trace. |
| "Stop, this direction is wrong." | Any task is running | `redirect` | Pause, summarize progress, ask for the new direction. |
| "Stop now. Do not keep going." | Agent is mid-task | `redirect` stop | Stop starting new work, cancel in-flight work if supported, summarize state, and wait. |
| "Cancel that command if you can." | A tool call or command may already be running | `redirect` stop | Use runtime cancellation if available; if not, stop immediately after control returns and do not start follow-up work. |
| "Actually that file is under /etc, not /var." | Agent used the wrong path | `correction` | Stop building on the wrong assumption and retry from the corrected path. |
| "After this, write a launch post." | Current task is still running | `task` | Queue it and continue the current task first. |
| "By the way, my deploy keys are in 1Password." | Current task does not need the keys now | `background` | Acknowledge as context, do not act on it. |
| "Use the other branch instead." | Agent is editing or reviewing a branch | `redirect` | Pause and confirm branch switch impact before continuing. |
| "This can wait." | Current task is running | `redirect` | Pause and ask whether to stop, queue, or resume later. |
| "Same issue happened yesterday." | Debugging task is running | `background` | Keep as a diagnostic note if relevant, continue. |
| "Don't touch production." | Any task may affect deployment | `correction` or `redirect` | Pause and reset the boundary around non-production work only. |
| "Also check the README typo." | Current task is not finished | `task` | Add to queue unless the user says to switch now. |
| "No, I meant the Windows machine." | Agent assumed the wrong environment | `correction` | Stop, restate the corrected environment, redo the next step. |
| "Can you explain what you just did?" | Current task may still be running | `interrupt` | Briefly explain current progress, then resume. |
| "This looks like you cannot simulate it." | TurnGuard test is running | `correction` | Acknowledge the concern, keep the test state, and continue processing later messages in order. |
| "I think A is wrong." then "Also write an X post later." | Messages arrive back-to-back | `correction`, then `task` | Apply the correction to the current judgment, queue the later task, and do not drop either message. |
| Runtime says "Interrupting current task." | System banner appears before the agent reply | not user intent | Preserve the active task, classify the real user message, and route normally. |
| "Continue the audit. Do not run off track." | Side branches appeared during a long task | `redirect` recovery | Restore the original task and continue from the latest verified step. |
| "Pause here and wait for me." | Agent wants to continue its plan | `redirect` stop | Pause the plan, report the last safe state, and wait for user input. |
| Agent wants to say "Classification: task." | Normal user-facing session | avoid label leak | Keep the label internal and explain the action naturally instead. |

## Ambiguous Cases

If a message could be either an interrupt or a redirect, ask once instead of guessing.

Example:

```text
User: What about the API route?
Agent: I can treat that as a note on the current task or switch to the API route now. Which do you want?
```

## Red-Line Cases

Pause regardless of classification when the next step is destructive, irreversible, money-related, account-sensitive, or contradicts the user's stated boundaries.
