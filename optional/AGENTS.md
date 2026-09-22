# Explicit Astra orchestration gate

- Keep `astra-orchestrator` inactive unless the user explicitly invokes `$astra-orchestrator` with a GPT-6 Astra root.
- Once activated, continue applying it to clarifications, corrections, reviews, and continuation requests for the same unfinished task; the user need not invoke it again.
- Stop on task completion, explicit disablement, a materially different task, or a root change away from GPT-6 Astra.
