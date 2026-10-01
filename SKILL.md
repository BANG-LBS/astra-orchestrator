---
name: astra-orchestrator
description: Explicit Astra-root orchestration for a task and its follow-ups, routing bounded work to GPT-6 Luna or Sol by task difficulty. Use only after the user invokes $astra-orchestrator with GPT-6 Astra.
---

# Astra 智能编排

## Activation boundary

- Start only after the user explicitly invokes `$astra-orchestrator` with a `gpt-6-astra` root. Invoking the skill does not select or prove the root model. Check runtime model metadata when available; if it is unavailable, ask the user to confirm that GPT-6 Astra is selected before activating. Preserve the root reasoning effort selected by the user; recommend `low` as the economical default, but do not override `medium`, `high`, `xhigh`, or `max`.
- Keep the workflow active across clarifications, corrections, reviews, and continuation requests for the same objective or deliverable, including revisions after an initial delivery; the user need not invoke it again. An assistant final reply, delivering a version, or choosing not to delegate does not by itself close the task.
- Changing the root reasoning effort does not deactivate the workflow as long as the root remains `gpt-6-astra`.
- Stop when the task is clearly closed (for example, the user accepts it as finished), the user disables the workflow, a materially different task begins, or the root changes away from Astra. Do not ask for a completion confirmation solely to manage this gate, or keep working after fulfilling the current request. After exit, require a new explicit invocation to reactivate. If the runtime reports a different root, do not apply the workflow and ask the user to switch models before invoking it again.
- Preserve the activation state, current objective, and skill path in any handoff or compaction summary you write. If the workflow remains active but its full instructions are no longer available, re-read the skill from that path before continuing; do not infer activation merely from a similar topic.
- Do not add an activation-status announcement when the user invokes the skill. On an active-to-inactive transition, briefly state the exit and its reason once in the current reply; do not repeat status on ordinary continuation turns.

## Root responsibilities

- Keep one stable root for decomposition, difficult reasoning, architecture, cross-cutting synthesis, conflict resolution, and final decisions.
- Decide whether delegation has a clear benefit before selecting a child model. Delegate work that is bounded and independently verifiable, useful in parallel, or likely to add noisy exploration, logs, tests, or document processing to the root context.
- Complete short or tightly coupled actions directly when delegation overhead would exceed the benefit.
- The root owns result integration, proportionate verification, and the final decision.

## Child routing

- For work already judged worth delegating, explicitly request `gpt-6-luna` for clear, repeatable, narrowly scoped tasks with verifiable results.
- Explicitly request `gpt-6-sol` directly for bounded tasks that clearly need substantial planning, multi-step tool use, complex implementation, or deep validation. Do not run such tasks through Luna solely to follow a fixed sequence.
- If Luna is unavailable or its spawn fails, the root may send the task directly to Sol. If a Luna result is blocked or materially incomplete, review the missing evidence and escalate only the unfinished or disputed scope to Sol at most once. Do not create an automatic retry loop. If Sol is unavailable or its result remains incomplete, the Astra root handles what it can and reports any concrete blocker.
- Do not use Astra as a child agent for ordinary work.
- Explicitly request `max` reasoning effort for GPT-6 Luna children by default. For GPT-6 Sol, request `medium` by default and `xhigh` upfront for deep reviews, complex implementations, or comparably demanding bounded tasks. Honor a different user-specified supported effort. These are this skill's quality-first choices, not claims of quota savings.
- Assign one clear owner to each subtask. Do not ask multiple agents to repeat the same work unless independent verification is intentional.

## Child context and lifecycle

- Give each child the smallest sufficient context. Prefer `fork_turns="none"` with a self-contained brief containing only the objective, scope, relevant files or symbols, constraints, acceptance criteria, and return format. If history is necessary, pass the smallest useful number of recent turns.
- Child agents must not spawn additional children unless the root explicitly authorizes nested delegation for a named task.
- Reuse the existing child for corrections, follow-up checks, or unfinished work in the same scope. Spawn a new child when the boundary materially changes, the prior child is unavailable, independent verification is required, or the documented Luna-to-Sol escalation changes the model.
- Use child evidence and perform only targeted verification of disputed, high-risk, or incomplete findings. Avoid repeating a child's full scan without a concrete reason.

## Handoff

- When a structured return will help, read [references/handoff-schema.md](references/handoff-schema.md) before writing the child brief and require its compact JSON shape.
- For non-text artifacts, require the artifact plus only the handoff fields needed to locate and verify it.

## Routing verification

- Only when the user asks to test model routing, read [references/routing-verification.md](references/routing-verification.md) and perform the live verification described there.
