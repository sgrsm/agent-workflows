# Finding [NN] Implementation Review

Verdict: [pass | needs_fix | blocked]
Implemented resolution approach: [concise summary]
Selected resolution approach label: [Option N (recommended) | Option N with slight refinement | Option N | Custom]
Effective follow-up severity for commit labeling: [blocker | major | minor | severity-unknown | n/a]
Remediation retry eligible: [yes | no | n/a]
Finding resolved: [yes | partially | no | unknown]
Context-rule compliance: [pass | fail | not assessed]
Verification status: [passed | failed | partial | not run]
Primary decision reason: [one sentence explaining the verdict]
Custom approach summary: [required only when selected approach is Custom; otherwise n/a]

## Required follow-up

- [None for pass; otherwise list the exact required action or clarification.]

## Decision summary

- Resolution: [whether the implementation resolves the reviewed finding]
- Compliance: [whether the implementation follows project context files and resolver policy]
- Behavior risk: [none | low | medium | high] — [short reason]
- Verification confidence: [short summary of commands, tests, or inspection basis]

## Blocking findings (must fix before pass)

If there are no blocking findings, write `None`. Otherwise repeat this shape for each blocking issue:

### B1. [short issue title]

- Location: `[path]:[line or range]`
- Issue: [what is wrong]
- Why it blocks this verdict: [specific correctness, behavior, project-rule, policy, or maintainability reason]
- Required fix: [smallest acceptable fix]

## Non-blocking warnings

If there are no non-blocking warnings, write `None`. Otherwise list pass-compatible follow-up, residual polish, or future cleanup:

- [warning or suggestion]

## Evidence

### Resolution evidence

- [Evidence that the finding was resolved, partially resolved, or not resolved.]

### Compliance evidence

- [Evidence for project-rule compliance, violation, or accepted deviation.]

### Behavior and test evidence

- [Evidence from source, tests, specs, command results, or explanation of why verification was not run.]

## Residual risks

- [Remaining uncertainty, especially if tests were not run.]

If none, write `None identified`.

<details>
<summary>Reviewed files</summary>

- `[path]`
- `[path]`

</details>

<details>
<summary>Reviewer commands</summary>

Run:

```bash
[command]
```

Not run:

```text
[command or test suite] — [reason]
```

</details>

<details>
<summary>Context references</summary>

- `[project context file, policy, spec, or source path]` — [why it mattered]
- Finding start SHA: `[sha]`

</details>
