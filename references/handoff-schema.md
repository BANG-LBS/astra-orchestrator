# Compact child-to-root handoff

When requested, return one compact JSON object without a Markdown fence. `status` and `summary` are always required:

```json
{
  "status": "done|blocked|failed",
  "summary": "No more than three concise sentences."
}
```

Add fields only when they contain useful information:

- `evidence`: verified facts with a precise `location` and concise `fact`.
- `changes`: actual changes made, preferably with artifact or file locations.
- `verification`: checks performed and their results.
- `risks`: unresolved but non-blocking concerns.
- `next_action`: a concrete next step for the root.

For `blocked`, add every currently known independent blocker:

```json
{
  "status": "blocked",
  "summary": "What cannot currently be completed.",
  "blockers": [
    {
      "primary": true,
      "reason": "concrete blocker",
      "impact": "what cannot proceed",
      "evidence": "observed evidence",
      "attempted": ["what was already tried"],
      "needed": "specific input, authority, or state change required"
    }
  ],
  "next_action": "what the root should do next"
}
```

## Rules

- Order blockers by dependency and impact. Mark exactly one as `primary: true` and place it first; do not hide additional independent blockers.
- Put speculative or merely possible obstacles under `risks`; do not present them as blockers.
- Omit optional fields that would otherwise be empty.
- Do not return raw logs, full files, repeated context, or chain-of-thought unless the root explicitly requests the underlying material.
