# Explicit Astra orchestration gate

- Keep `astra-orchestrator` inactive unless the user explicitly invokes `$astra-orchestrator` with a GPT-6 Astra root.
- Once activated, continue applying it to clarifications, corrections, reviews, and continuation requests for the same objective or deliverable, including revisions after an initial delivery. An assistant final reply, delivering a version, or not delegating does not by itself close the task.
- Stop when the task is clearly closed (for example, the user accepts it as finished), the user explicitly disables the workflow, a materially different task begins, or the root changes away from GPT-6 Astra. Do not ask for a completion confirmation solely to manage this gate or keep working after fulfilling the current request. After exit, require a new explicit invocation to reactivate.
- Preserve activation state, current objective, and skill path in any handoff or compaction summary you write; if active rules are missing, re-read the skill from that path. Do not infer activation merely from a similar topic.
- Do not announce activation status. Briefly state exit and its reason once when the workflow becomes inactive; do not repeat status on ordinary continuation turns.
