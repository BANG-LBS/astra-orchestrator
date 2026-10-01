# Astra Orchestrator

[中文说明](README.zh-CN.md)

A Codex skill created by **BANG-S**. Version 2 lets users describe a clear goal while GPT-6 Astra decides which work to handle itself and which bounded tasks to delegate to GPT-6 Luna or GPT-6 Sol.

> This is an independent project by BANG-S and is not an official OpenAI product.

## Why

Powerful root models are useful for ambiguity, architecture, conflict resolution, and synthesis. They do not need to personally perform every repository scan, document pass, log review, or other high-volume task.

This skill adds a cost-aware orchestration policy:

- Astra first decides whether delegation has a clear benefit.
- Short or tightly coupled work stays with the root.
- Clear, repeatable, independently verifiable work goes to GPT-6 Luna.
- Work that clearly needs substantial planning, multi-step execution, or deep validation goes directly to GPT-6 Sol.
- A blocked or materially incomplete Luna result may be escalated once to Sol after the gap is reviewed.
- Child agents receive the smallest sufficient context and return compact evidence.
- The Astra root remains responsible for integration, proportionate verification, and the final answer.

The aim is a useful deliverable with proportionate effort. Savings depend on how much suitable work can be delegated and the cost of coordination, checking, and rework; a larger task does not automatically mean a larger saving.

## Installation

### With Skill Installer

Invoke `$skill-installer` in Codex and ask it to install `https://github.com/BANG-LBS/astra-orchestrator`. Select GPT-6 Astra separately before using this skill.

### Manual installation

Copy or clone this repository to the user skill directory:

- macOS/Linux: `$HOME/.agents/skills/astra-orchestrator`
- Windows: `%USERPROFILE%\.agents\skills\astra-orchestrator`

Restart Codex if the skill does not appear immediately. The [official skill documentation](https://learn.chatgpt.com/docs/build-skills) describes skill discovery and installation locations. If it is already installed in a location recognized by your Codex version, update that copy instead of creating a duplicate.

Normal use does not require Python or PyYAML. Those are only needed if a maintainer chooses to run the separate Skill Creator format validator. Model and subagent availability depend on the host and account.

## Usage

1. Select GPT-6 Astra and the reasoning level appropriate for the task.
2. Explicitly invoke the skill in the first request:

```text
$astra-orchestrator Analyze this repository, fix the important issues, and verify the result.
```

3. Continue the same objective normally. When the optional activation gate is installed, clarifications, corrections, reviews, and revisions after an initial delivery remain under the same orchestration workflow without another invocation.

The skill supports the Astra reasoning level selected by the user. `low` is an economical starting point, not a requirement.
Invoking the skill does not switch the root model. If the runtime cannot show the root model, explicitly confirm that GPT-6 Astra is selected before activation.

## Optional multi-turn activation gate

[`optional/AGENTS.md`](optional/AGENTS.md) contains a small global gate that instructs the model to keep the skill active across follow-up turns for the same objective or deliverable. This is an instruction-based continuation policy, not a separate runtime switch.

If you use it, merge its rules into your existing global `AGENTS.md`. Do not overwrite unrelated personal instructions. A final reply or an initial delivery does not by itself close the task. The workflow exits when the task is clearly closed, the user disables it, a materially different task begins, or the root changes away from Astra; reactivation then requires a new explicit invocation. Activation is quiet, while exit gets one brief explanation. Handoff and compaction summaries preserve the activation state, objective, and skill path. The gate does not require a completion-confirmation question or additional work after the current request is fulfilled.

## Routing policy

```text
GPT-6 Astra root
    ├─ handles decomposition, difficult reasoning, architecture, synthesis, and final decisions
    ├─ completes short or tightly coupled work directly
    └─ delegates suitable bounded work
          ├─ GPT-6 Luna for clear, repeatable work
          └─ GPT-6 Sol for demanding work or a Luna escalation
```

The skill does not force a fixed number of child agents. This project's quality-first policy requests Luna `max`; Sol starts at `medium`, with `xhigh` for deep reviews, complex implementations, or similarly demanding bounded work. Users may explicitly choose another supported effort. These are project choices, not a guarantee of lower quota usage. Ordinary child agents do not recursively spawn more agents unless the root explicitly authorizes that for a named task.

## Files

- [`SKILL.md`](SKILL.md): activation, responsibility, routing, context, and lifecycle rules.
- [`agents/openai.yaml`](agents/openai.yaml): UI metadata and explicit-only invocation policy.
- [`references/handoff-schema.md`](references/handoff-schema.md): optional compact child-to-root result format.
- [`references/routing-verification.md`](references/routing-verification.md): live routing verification used only when requested.
- [`examples/real-world-evaluation.md`](examples/real-world-evaluation.md): anonymized personal real-world test.
- [`docs/design-and-iterations.md`](docs/design-and-iterations.md): a Chinese-language record of the design decisions and iterations that shaped the skill.

## Personal real-world evaluation

The September 2026 V2 comparison used the same original workbook, a concise business prompt, and GPT-6 Astra `xhigh`, with the native group followed by the skill-assisted group. The task was to analyze 30 high-converting Xiaohongshu posts for an unspecified product.

| Observed result | Native Astra | Astra + V2 |
| --- | --- | --- |
| Core deliverable | Complete report with per-post analysis | Complete report with per-post analysis |
| Source handling | Reused archived material | Reused archived material and checked 30 current pages |
| Finding relevant cases | Keyword search | Keyword search plus format and writing-category filters |
| Total root + child tokens | About 6.24 million | About 7.62 million |
| API token-equivalent cost, monitoring excluded | About $11.35 | About $9.33 |

In this case, V2 added useful verification and filtering while its API-equivalent cost was **17.8% lower**. The user found its report easier to read and reuse; the native report felt more technical. Both groups completed the task without additional business instructions.

These costs reprice recorded tokens using Standard API list prices checked on September 27, 2026; they are not actual subscription charges or measured five-hour quota savings. Both groups reused historical material and experienced quota interruptions, their completed scope differed, and review was not blind. Total tokens increased by 22.1%. This is one personal case, not a general savings rate or proof of better onboarding for every beginner.

The [anonymized evaluation](examples/real-world-evaluation.md) gives the method, model-level cost calculation, limitations, and separate V1 background. Further changes will be guided by real business use rather than additional benchmark runs.

## Privacy

The release files omit the original business dataset, product identity, post content, images, videos, business account details, sales figures, private task links, local configuration, and credentials. The public evaluation retains anonymized scope, model usage, API-equivalent costs, and high-level observations. Raw logs and complete business reports remain private.

## Author

Created and maintained by **BANG-S**.

## License

MIT
