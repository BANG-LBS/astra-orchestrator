# Routing verification

Use this procedure only when the user explicitly asks to verify child-model routing.

1. Confirm the root is GPT-6 Astra before spawning.
2. Spawn a small, read-only child task whose result is easy for the root to verify, such as a bounded repository scan. Request the model and effort required by the active policy: GPT-6 Luna `max` for a clear, narrow task; GPT-6 Sol `medium` by default or `xhigh` for a deep review or complex implementation. Test Luna-to-Sol escalation only when the documented conditions occur.
3. Record the root-model evidence, requested child model and effort, and any child-model identity confirmed by the runtime. Do not ask the child to infer or claim its own model.
4. Confirm only what the runtime evidence supports. If the runtime does not expose the accepted child model, report that limitation instead of treating the requested model as proven. Do not call a run Astra-root verified without root-model evidence.
5. Verify that no Astra child was requested for ordinary work.
