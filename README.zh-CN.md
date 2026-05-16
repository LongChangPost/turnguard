# TurnGuard

> 一个给 AI Agent 用的轻量会话路由 skill：先分类，再回应，避免人类插话污染上下文。

人是会插话的。大多数 Agent 系统却默认把每条新消息都当成新任务。这个默认假设会把一句随口补充变成任务跑偏、上下文污染、retry 风暴和 token 失控。

TurnGuard 的思路很小：回应前先判断这句话是什么，再决定怎么处理。

## 它解决什么

当 Agent 正在执行长任务时，把用户新消息分成五类：

- `task`：一个新任务
- `interrupt`：任务进行中的插话、想法、旁支问题
- `redirect`：明确改变方向
- `correction`：用户指出 Agent 前面的判断错了
- `background`：有用背景信息，但不是请求

然后按类型路由，避免 Agent 丢掉当前主线。

## 为什么值得用

长任务中的 AI 协作很容易被人类自己打断。你可能只是补一句“我觉得像网络问题”，Agent 却可能立刻切方向、忘掉原任务，甚至把半成品判断写进记忆。

TurnGuard 把三件事分开：

- 当前任务
- 插话缓冲区
- 新任务队列

它还处理五个真实使用中很常见的问题：

- 用户连续发多条消息时，要按时间顺序逐条处理，不能只看最后一句。
- 系统层的 interrupt 提示不等于用户意图，Agent 仍要按用户真实消息来路由。
- “继续”“回到刚才任务”“不要跑题”属于恢复型 redirect，要回到原主线。
- “停止”“暂停”“取消”“别继续”属于叫停型 redirect，Agent 应停止发起新动作、总结当前状态并等待。
- 分类标签默认不要外显；TurnGuard 是行为路由，不是让 Agent 每次播报分类。

## 安装

TurnGuard 是纯 `SKILL.md` 包，不带脚本、不需要依赖、不需要密钥。任何支持 `SKILL.md` 风格 skill 的 agent runtime 都可以用。

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

GitHub repo 公开后，Hermes 用户也可以直接从 GitHub 安装：

```bash
hermes skills install MrHistoryLeo/turnguard/skills/turnguard
```

### OpenClaw

```bash
cp -r skills/turnguard ~/.openclaw/skills/
```

OpenClaw 项目内安装：

```bash
cp -r skills/turnguard /your/workspace/skills/
```

### 项目内安装

```bash
cp -r skills/turnguard /your/project/.codex/skills/
```

## 兼容性

TurnGuard 面向 Claude Code、Codex、Hermes Agent、OpenClaw，以及其他兼容 `SKILL.md` 的 agent runtime。它刻意保持为纯行为协议：不执行 shell 命令、不联网、不读取密钥，也不依赖某个 runtime 的专有工具。

## 使用场景

当 Agent 正在改文件、调试、研究、跑工具、执行多步骤任务时，如果用户可能继续补充消息，就启用 TurnGuard。

例子：

```text
User: 我觉得可能是网络问题。
内部判断: interrupt
Agent 行为: 把它记为当前诊断的一个假设，但不立刻切换任务。
```

更多例子见 [examples.md](examples.md)。

## 它不是什么

TurnGuard 不是安全沙箱，不负责权限控制、提示注入防护或密钥保护。它只是一个会话行为协议，用来减少任务漂移和上下文污染。

TurnGuard 也不保证强制中断已经在运行中的工具调用或外部进程。如果 runtime 支持取消，Agent 应该使用取消能力；如果 runtime 不支持，Agent 应在控制权返回后立刻停止，不再启动新的动作。

## 状态

`v0`。刻意保持很小。欢迎反馈、fork 和真实失败案例。

## License

MIT
