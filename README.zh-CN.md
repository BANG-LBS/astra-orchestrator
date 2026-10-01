# Astra 智能编排

[English README](README.md)

这是一个由 **BANG-S** 开发、需要显式调用的 Codex Skill。用户说清目标后，由 GPT-6 Astra 判断哪些工作直接完成、哪些适合交给 GPT-6 Luna 或 GPT-6 Sol，并负责最终交付。

> 本项目由 BANG-S 独立开发，不是 OpenAI 官方产品。

## 为什么做这个 Skill

Astra 适合处理含糊需求、架构、冲突和综合判断，但不一定需要亲自完成每一次仓库扫描、文档读取、日志检查和其他高信息量工作。

这个 Skill 提供一套具有成本意识的编排策略：

- Astra 先判断委派是否有明确收益；
- 很短或强耦合的工作由 root 直接完成；
- 清晰、可重复、可独立验证的工作交给 GPT-6 Luna；
- 明显需要复杂规划、多步骤执行或深入验证的工作直接交给 GPT-6 Sol；
- Luna 受阻或结果明显不完整时，检查缺口后最多升级一次 Sol；
- child 只接收足够完成任务的最小上下文，并返回精简证据；
- Astra始终负责结果整合、适度验证和最终回答。

目标是用合适的投入交付有用的结果。能省多少，取决于可委派的工作量，以及拆分、沟通、验收和返工的成本；任务变大不自动意味着节省比例更高。

## 安装

### 使用 Skill Installer

在 Codex 中调用 `$skill-installer`，让它安装 `https://github.com/BANG-LBS/astra-orchestrator`。使用本 Skill 前，仍需另行选择 GPT-6 Astra。

### 手动安装

将本仓库复制或克隆到用户级 Skill 目录：

- macOS/Linux：`$HOME/.agents/skills/astra-orchestrator`
- Windows：`%USERPROFILE%\.agents\skills\astra-orchestrator`

如果没有立即出现在 Skill 列表中，请重启 Codex。Skill 的发现与安装位置可参考 [OpenAI 官方 Skill 文档](https://learn.chatgpt.com/docs/build-skills)。若已安装在当前 Codex 能识别的位置，直接更新原副本，避免重复安装。

正常使用无需安装 Python 或 PyYAML；只有维护者运行独立的 Skill Creator 格式校验器时才需要这些工具。可用模型和子代理能力以实际运行环境、账号权限为准。

## 使用方法

1. 选择 GPT-6 Astra，并根据任务难度选择 reasoning 等级。
2. 第一次发送任务时显式调用：

```text
$astra-orchestrator 分析这个项目，修复重要问题并验证结果。
```

3. 正常继续同一目标。安装可选持续门控后，补充要求、纠正、复查，以及初次交付后的修改会沿用同一套编排流程，无需再次调用。

Skill 会保留用户选择的 Astra reasoning 等级。`low` 只是经济型起点，不是强制要求。
调用 Skill 不会自动切换主模型；如运行环境不能显示主模型，请先明确确认已选择 GPT-6 Astra。

## 可选的多轮持续门控

[`optional/AGENTS.md`](optional/AGENTS.md) 包含一段很轻量的全局门控，要求模型在围绕同一目标或交付物的后续对话中继续应用 Skill。这是基于指令的延续约定，不是独立的运行时开关。

如需使用，请把其中规则合并到你现有的全局 `AGENTS.md`，不要覆盖已有个人指令。结束一轮回复或交付一版不单独代表任务关闭。任务明确收尾、用户关闭编排、开始明显不同的新任务，或 root 切换离开 Astra 后退出，再次启用需要显式调用。手动启用时不额外播报状态，退出时简短说明一次原因。交接和上下文压缩摘要保留启用状态、当前目标与 Skill 路径。无需为门控额外追问是否完成，也不在当前请求已经完成后继续自行工作。

## 模型分工

```text
GPT-6 Astra root
    ├─ 负责拆解、困难推理、架构、综合和最终决策
    ├─ 直接完成很短或强耦合的工作
    └─ 委派适合拆出的工作
          ├─ 清晰、可重复：GPT-6 Luna
          └─ 明显复杂或 Luna 升级：GPT-6 Sol
```

Skill 不强制固定 child 数量。按本项目当前偏重质量的选择，Luna 子任务默认请求 `max`；Sol 以 `medium` 为起点，深度审查、复杂实现等高难度子任务请求 `xhigh`。用户可以明确指定其他受支持的强度。这些是项目策略，不保证降低套餐额度消耗。普通 child 不得自行继续派生更多代理，除非 root 针对某项明确任务授权。

## 仓库内容

- [`SKILL.md`](SKILL.md)：激活边界、职责、路由、上下文和生命周期规则。
- [`agents/openai.yaml`](agents/openai.yaml)：中文显示信息和仅限显式调用策略。
- [`references/handoff-schema.md`](references/handoff-schema.md)：按需使用的精简 child-to-root 交接格式。
- [`references/routing-verification.md`](references/routing-verification.md)：只有用户要求时才进行的路由验证流程。
- [`examples/real-world-evaluation.md`](examples/real-world-evaluation.md)：经过脱敏的个人真实案例测试。
- [`docs/design-and-iterations.md`](docs/design-and-iterations.md)：经过整理和脱敏的设计与迭代记录。

## 个人真实案例测试

2026年9月的 V2 对照使用同一原始表格、简短业务提示和 GPT-6 Astra `xhigh`，先运行基准组，再运行工具组。业务场景为分析某产品近30天转化表现靠前的30篇小红书笔记。

| 观察项 | 基准组：原生 Astra | 工具组：Astra + V2 |
| --- | --- | --- |
| 核心交付 | 完整报告与逐篇分析 | 完整报告与逐篇分析 |
| 来源处理 | 复用历史素材 | 复用历史素材，另核验30篇当前页面 |
| 案例查找 | 关键词搜索 | 关键词搜索、内容形式和写法类别组合筛选 |
| 根及子任务总 token | 约624万 | 约762万 |
| API token 等价成本，不含监测 | 约11.35美元 | 约9.33美元 |

本次工具组多提供了来源复核和筛选功能，**API 等价成本仍低约17.8%**。用户实际阅读后认为工具组更易理解、方便复用，原生组的表达更偏专业分析。两组都无需追加业务要求即可完成交付。

上述成本用2026年9月27日核实的 Standard API 单价重算已有 token，不是订阅实际扣款或已测得的五小时额度节省。两组都复用了历史素材、经历额度中断，实际交付范围不同，评阅也不是盲评；工具组总 token 反而多22.1%。这是一个个人案例，不是通用节省率，也不能代表所有新手的使用体验。

详细方法、分模型计价、限制和单列的 V1 历史观察见[脱敏案例](examples/real-world-evaluation.md)。后续根据真实业务反馈改进，不再为了寻找通用结论追加跑分。

## 隐私说明

发布文件不包含原始业务数据、产品身份、笔记正文、图片、视频、业务账号、经营金额、私人任务链接、本地配置或访问凭据。公开案例只保留脱敏任务范围、模型用量、API 等价成本和总体观察；原始日志与完整业务报告不公开。

## 作者

由 **BANG-S** 创建并维护。

## 许可证

MIT
