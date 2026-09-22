---
name: astra-orchestrator
description: Explicit Astra-root orchestration for an unfinished task, delegating bounded work to Luna with controlled Terra escalation. Use only after the user invokes $astra-orchestrator with GPT-6 Astra.
---

# Astra 智能编排

## Activation boundary

- Start only after the user explicitly invokes `$astra-orchestrator` for a `gpt-6-astra` root. Preserve the root reasoning effort selected by the user; recommend `low` as the economical default, but do not override `medium`, `high`, `xhigh`, or `max`. Treat the invocation as confirmation of the selected root unless the runtime reports otherwise.
- Keep the workflow active across clarifications, corrections, reviews, and continuation requests for the same unfinished task; the user need not invoke it again.
- Changing the root reasoning effort does not deactivate the workflow as long as the root remains `gpt-6-astra`.
- Stop when the task is complete, the user disables it, a materially different task begins, or the root changes away from Astra. If the runtime reports a different root, do not apply the workflow and ask the user to switch models before invoking it again.

## Root responsibilities

- Keep one stable root for decomposition, difficult reasoning, architecture, cross-cutting synthesis, conflict resolution, and final decisions.
- Decide whether delegation has a clear benefit before selecting a child model. Delegate work that is bounded and independently verifiable, useful in parallel, or likely to add noisy exploration, logs, tests, or document processing to the root context.
- Complete short or tightly coupled actions directly when delegation overhead would exceed the benefit.
- The root owns result integration, proportionate verification, and the final decision.

## Child routing

- For a task already judged worth delegating, explicitly request `gpt-5.6-luna` first.
- The root may escalate the same task once to `gpt-5.6-terra` when Luna is unavailable, the spawn fails, the result is blocked or materially incomplete, key evidence is missing, or deeper repository-wide reasoning is clearly required. Review the Luna result before escalating; do not create an automatic retry loop.
- Do not use Astra as a child agent for ordinary work.
- Do not force one global reasoning effort for children. Choose effort per task; child agents may use up to `max` when difficult reasoning warrants it.
- Assign one clear owner to each subtask. Do not ask multiple agents to repeat the same work unless independent verification is intentional.

## Child context and lifecycle

- Give each child the smallest sufficient context. Prefer `fork_turns="none"` with a self-contained brief containing only the objective, scope, relevant files or symbols, constraints, acceptance criteria, and return format. If history is necessary, pass the smallest useful number of recent turns.
- Child agents must not spawn additional children unless the root explicitly authorizes nested delegation for a named task.
- Reuse the existing child for corrections, follow-up checks, or unfinished work in the same scope. Spawn a new child only when the boundary materially changes, the prior child is unavailable, or independent verification is required.
- Use child evidence and perform only targeted verification of disputed, high-risk, or incomplete findings. Avoid repeating a child's full scan without a concrete reason.

## Handoff

- When a structured return will help, read [references/handoff-schema.md](references/handoff-schema.md) before writing the child brief and require its compact JSON shape.
- For non-text artifacts, require the artifact plus only the handoff fields needed to locate and verify it.

## Routing verification

- Only when the user asks to test model routing, read [references/routing-verification.md](references/routing-verification.md) and perform the live verification described there.
