# Astra 智能编排

[English README](README.md)

这是一个由 **BANG-S** 开发、需要显式调用的 Codex Skill。它让 GPT-6 Astra 负责拆解、困难推理和最终决策，将适合委派的边界清晰工作优先交给 GPT-5.6 Luna，并在必要时受控升级到 Terra。

> 本项目由 BANG-S 独立开发，不是 OpenAI 官方产品。

## 为什么做这个 Skill

Astra 适合处理含糊需求、架构、冲突和综合判断，但不一定需要亲自完成每一次仓库扫描、文档读取、日志检查和其他高信息量工作。

这个 Skill 提供一套具有成本意识的编排策略：

- Astra 先判断委派是否有明确收益；
- 很短或强耦合的工作由 root 直接完成；
- 边界清晰、可独立验证的工作优先交给 Luna；
- Luna 不可用或结果明显不完整时，可受控升级一次 Terra；
- child 只接收足够完成任务的最小上下文，并返回精简证据；
- Astra始终负责结果整合、适度验证和最终回答。

官方模型指南指出，Astra 可能比工作流预期更少主动委派，因此需要明确说明何时以及在多大程度上使用子代理。参见 [GPT-6 Astra 官方指南](https://developers.openai.com/api/docs/guides/latest-model)。

## 安装

### 使用 Skill Installer

在 Codex 中调用 `$skill-installer`，让它从本仓库安装 `astra-orchestrator`。

### 手动安装

将本仓库复制或克隆到用户级 Skill 目录：

- macOS/Linux：`$HOME/.agents/skills/astra-orchestrator`
- Windows：`%USERPROFILE%\.agents\skills\astra-orchestrator`

如果没有立即出现在 Skill 列表中，请重启 Codex。Skill 的发现与安装位置可参考 [OpenAI官方 Skill 文档](https://learn.chatgpt.com/docs/build-skills)。

## 使用方法

1. 选择 GPT-6 Astra，并根据任务难度选择 reasoning 等级。
2. 第一次发送任务时显式调用：

```text
$astra-orchestrator 分析这个项目，修复重要问题并验证结果。
```

3. 正常继续同一项未完成任务。安装可选持续门控后，补充要求、纠正、复查和继续执行会保持同一套编排流程。

Skill 会保留用户选择的 Astra reasoning 等级。`low` 只是经济型起点，不是强制要求。

## 可选的多轮持续门控

[`optional/AGENTS.md`](optional/AGENTS.md) 包含一段很轻量的全局门控，用来让 Skill 在同一项未完成任务的后续对话中继续生效。

如需使用，请把其中三条规则合并到你现有的全局 `AGENTS.md`，不要覆盖已有个人指令。任务完成、用户明确关闭、开始明显不同的新任务，或 root 切换离开 Astra 后，门控会停止应用。

## 模型分工

```text
GPT-6 Astra root
    ├─ 负责拆解、困难推理、架构、综合和最终决策
    ├─ 直接完成很短或强耦合的工作
    └─ 委派适合拆出的工作
          ├─ 优先 GPT-5.6 Luna
          └─ 满足升级条件时使用一次 GPT-5.6 Terra
```

Skill 不强制固定 child 数量，也不强制所有 child 使用同一个 reasoning 等级。普通 child 不得自行继续派生更多代理，除非 root 针对某项明确任务授权。

## 仓库内容

- [`SKILL.md`](SKILL.md)：激活边界、职责、路由、上下文和生命周期规则。
- [`agents/openai.yaml`](agents/openai.yaml)：中文显示信息和仅限显式调用策略。
- [`references/handoff-schema.md`](references/handoff-schema.md)：按需使用的精简 child-to-root 交接格式。
- [`references/routing-verification.md`](references/routing-verification.md)：只有用户要求时才进行的路由验证流程。
- [`examples/real-world-evaluation.md`](examples/real-world-evaluation.md)：经过脱敏的个人真实案例测试。
- [`docs/design-and-iterations.md`](docs/design-and-iterations.md)：经过整理和脱敏的设计与迭代记录。

## 测试名称

为了让第一次接触项目的人能够直接看懂，公开测试统一使用以下名称：

1. **基准组（原生Astra）**
2. **工具组（Astra智能编排开启）**
3. **人工深度组（原生Astra+深度提示词）**

“基准组”和“工具组”主要观察同类简短提示下，Skill 是否改变默认完成范围。“人工深度组”观察不使用 Skill 时，用户通过详细提示能否让原生 Astra 达到相近或更深的工作程度。

## 测试能够说明什么

测试使用了一个经过匿名处理的个人真实案例：梳理某产品近30天内转化表现靠前的30篇小红书笔记，读取内容、检查数据范围并总结共同规律。

观察到的主要价值是降低提示词门槛：用户不必手动写出所有读取、持续执行、范围校验和报告要求，也能通过简短需求获得完整、可用于业务判断的结果。

这是一项个人真实案例测试，主要观察实际使用体验和任务完成范围。三次运行实际完成的工作范围并不相同，因此其中的额度比例不代表所有任务都能获得相同结果。

## 隐私说明

本仓库不包含原始业务数据、产品身份、笔记正文、图片、视频、账号信息、经营金额、私人任务链接、本地配置或访问凭据。公开案例只保留任务结构、执行范围、近似使用量和总体观察。

## 作者

由 **BANG-S** 创建并维护。

## 许可证

MIT
