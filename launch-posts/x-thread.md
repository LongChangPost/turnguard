# TurnGuard X 中文首发帖（最新版）

## 推荐标题

我不会写代码，但我给 AI Agent 装了刹车和方向盘。

## 标题备选

1. 我不会写代码，但我给 AI Agent 写了一条规矩：该继续别跑偏，该停下别硬跑
2. AI Agent 最大的问题之一：你随口插一句，它就把主线弄丢了
3. 不是模型不够聪明，是它不知道人类这句话到底算什么
4. 我给 AI Agent 做了一个小协议：先听懂插话，再决定要不要继续
5. 会沟通的人，正在获得一种新的工程能力

## 推荐 X Thread

### 1/

我不会写代码。

不是科班，也不是传统工程师。

但我最近给 AI Agent 做了一个小东西：

给它装“刹车”和“方向盘”。

名字叫 TurnGuard。
一个很小的 conversation-routing protocol。

https://github.com/LongChangPost/turnguard

### 2/

它解决的不是玄学问题，而是我每天都遇到的真实崩溃：

Agent 正在跑长任务。
我随口补一句想法。
它立刻以为我下了新命令。

然后开始抢跑、切题、忘掉原任务。

### 3/

更糟的是反过来也会发生。

有时候你已经说：

停一下。
别继续。
先别改。
等我确认。

但 agent 还会沿着旧计划继续往前冲。

它不是坏，是不会判断“这句话的优先级”。

### 4/

真实工作里，人类说话不是 API call。

同一个聊天窗口里，可能混着：

新任务
插话
纠错
改方向
叫停
背景信息

如果 agent 全部当成“下一步指令”，长任务一定会漂。

### 5/

TurnGuard 的做法很简单：

先分类，再路由。

task / interrupt / redirect / correction / background

新任务先排队。
插话只记录。
纠错就回滚错误假设。
叫停就停止新动作、总结状态、等待。

### 6/

我最在意的不是让 agent 把分类说出来。

恰恰相反，好用的 TurnGuard 不该天天输出：

Classification: interrupt

好的路由应该消失在行为里：

收到，先记下。
这个任务我先排队。
我先停在这里，等你确认。

### 7/

我不是用 demo 骗自己。

我把它装进真实 agent，压了长任务：

连续插话
前后纠错
新任务排队
runtime interrupt
provider drop / reconnect
弱 agent 把内部分类说漏嘴

这些问题都真实发生过。

### 8/

所以 TurnGuard 不是 framework。
不是安全沙箱。
也不是 prompt injection 防护。

它只是一个很小的工作协议：

让 agent 知道什么时候继续，
什么时候排队，
什么时候纠错，
什么时候停下来等人。

### 9/

这也是我越来越确定的一件事：

普通人用 AI 做事，优势不一定来自会不会写代码。

而是来自能不能把工作经验、沟通规则、判断边界，变成 agent 能执行的协议。

TurnGuard 是我公开发布的第一个。

https://github.com/LongChangPost/turnguard

## 备用短帖

我不会写代码。

但我给 AI Agent 做了一个小协议：

让它在人类插话时，该继续不丢主线，该停下别硬跑。

TurnGuard 不是什么大 framework。

它只做一件事：

先判断新消息是 task / interrupt / redirect / correction / background，
再决定继续、排队、纠错、恢复主线，还是停下来等人。

https://github.com/LongChangPost/turnguard

## 发布备注

- GitHub repo 公开后确认链接可打开：`https://github.com/LongChangPost/turnguard`。
- 配图：`launch-posts/turnguard-x-cover.png`。
- 不建议在公开帖里提阿珍、CoCo、Hermes 迁移路径等内部上下文。
- “真实 agent 压力测试”可以讲，但不要把内部机器路径、Notion 链接、agent 名字放进 X。
