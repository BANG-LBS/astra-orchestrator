# Astra Orchestrator

[中文说明](README.zh-CN.md)

A Codex skill created by **BANG-S**. It is explicitly invoked and keeps GPT-6 Astra in charge of decomposition and final decisions while delegating suitable bounded work to GPT-5.6 Luna, with controlled escalation to Terra.

> This is an independent project by BANG-S and is not an official OpenAI product.

## Why

Powerful root models are useful for ambiguity, architecture, conflict resolution, and synthesis. They do not need to personally perform every repository scan, document pass, log review, or other high-volume task.

This skill adds a cost-aware orchestration policy:

- Astra first decides whether delegation has a clear benefit.
- Short or tightly coupled work stays with the root.
- Bounded and independently verifiable work goes to Luna first.
- Terra may be used once when Luna is unavailable or its result is materially incomplete.
- Child agents receive the smallest sufficient context and return compact evidence.
- The Astra root remains responsible for integration, proportionate verification, and the final answer.

The policy exists because Astra may delegate less often than a workflow needs unless delegation expectations are made explicit. See the [official GPT-6 Astra model guidance](https://developers.openai.com/api/docs/guides/latest-model).

## Installation

### With Skill Installer

Invoke `$skill-installer` in Codex and ask it to install `astra-orchestrator` from this repository.

### Manual installation

Copy or clone this repository to the user skill directory:

- macOS/Linux: `$HOME/.agents/skills/astra-orchestrator`
- Windows: `%USERPROFILE%\.agents\skills\astra-orchestrator`

Restart Codex if the skill does not appear immediately. The [official skill documentation](https://learn.chatgpt.com/docs/build-skills) describes skill discovery and installation locations.

## Usage

1. Select GPT-6 Astra and the reasoning level appropriate for the task.
2. Explicitly invoke the skill in the first request:

```text
$astra-orchestrator Analyze this repository, fix the important issues, and verify the result.
```

3. Continue the same unfinished task normally. When the optional activation gate is installed, clarifications, corrections, reviews, and continuation requests remain under the same orchestration workflow.

The skill supports the Astra reasoning level selected by the user. `low` is an economical starting point, not a requirement.

## Optional multi-turn activation gate

[`optional/AGENTS.md`](optional/AGENTS.md) contains a small global gate that keeps the skill active across follow-up turns for the same unfinished task.

If you use it, merge its three rules into your existing global `AGENTS.md`. Do not overwrite unrelated personal instructions. The gate stops when the task is complete, the user disables it, a materially different task begins, or the root changes away from Astra.

## Routing policy

```text
GPT-6 Astra root
    ├─ handles decomposition, difficult reasoning, architecture, synthesis, and final decisions
    ├─ completes short or tightly coupled work directly
    └─ delegates suitable bounded work
          ├─ GPT-5.6 Luna first
          └─ GPT-5.6 Terra once when escalation criteria are met
```

The skill does not force a fixed number of child agents or one global child reasoning level. It also prevents ordinary child agents from recursively spawning more agents unless the root explicitly authorizes that for a named task.

## Files

- [`SKILL.md`](SKILL.md): activation, responsibility, routing, context, and lifecycle rules.
- [`agents/openai.yaml`](agents/openai.yaml): UI metadata and explicit-only invocation policy.
- [`references/handoff-schema.md`](references/handoff-schema.md): optional compact child-to-root result format.
- [`references/routing-verification.md`](references/routing-verification.md): live routing verification used only when requested.
- [`examples/real-world-evaluation.md`](examples/real-world-evaluation.md): anonymized personal real-world test.
- [`docs/design-and-iterations.md`](docs/design-and-iterations.md): a Chinese-language record of the design decisions and iterations that shaped the skill.

## What the evaluation does and does not show

The anonymized evaluation used a personal real-world task involving the analysis of the top 30 converting Xiaohongshu posts for an unspecified product. It compares:

1. **Baseline (Native Astra)**
2. **Skill-assisted (Astra Orchestrator enabled)**
3. **Manual-depth (Native Astra + detailed prompt)**

The strongest observed benefit was a lower prompting barrier: a concise request produced a complete, business-usable deliverable without requiring the user to manually specify every persistence, reading, validation, and reporting step.

This personal real-world test focuses on the user experience and actual completion scope. The three runs completed different amounts of work, so the observed percentages should not be treated as a universal quota-saving or quality-improvement rate.

## Privacy

No original business dataset, product identity, post content, images, videos, account details, financial values, private task links, local configuration, or credentials are included in this repository. The public evaluation retains only task shape, execution scope, approximate usage, and high-level observations.

## Author

Created and maintained by **BANG-S**.

## License

MIT
