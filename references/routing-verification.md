# Routing verification

Use this procedure only when the user explicitly asks to verify child-model routing.

1. Spawn a small, read-only child task whose result is easy for the root to verify, such as a bounded repository scan.
2. Request the model required by the active routing policy: Luna first, or Terra only under the documented escalation conditions.
3. Record the model requested and any model identity confirmed by the runtime. Do not ask the child to infer or claim its own model.
4. Confirm only what the runtime evidence supports. If the runtime does not expose the accepted model, report that limitation instead of treating the requested model as proven.
5. Verify that no Astra child was requested for ordinary work.
