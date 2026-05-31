# Final Evaluation Template

Write the final evaluation report to:
`<final_evaluation_output_path>`

Use this structure:

```md
# Final Evaluation Report

- Consolidated report path: `<consolidated_report_output_path>`
- Final evaluation report path: `<final_evaluation_output_path>`
- Review scope: `<review_scope>`
- Verification basis: Independent per-finding investigation of consolidated findings against
  `<diff_baseline>`

## Investigation Status
- Consolidated findings detected: <finding count>
- Investigation subagents completed: <finding count>
- Investigation subagents failed or missing: <finding count>

## Current Resolution State
- Section owner: resolver workflow
- Historical review sections in this document remain unchanged and should be read as review-time
  state.
- Findings still pending resolution: <pending finding count>
- Findings still pending false-positive confirmation: <pending false-positive confirmation count>
- Findings with finalized resolver outcome: 0
- Current resolver summary: Pending initial resolver run.

## Final Evaluation By Finding

### 1. <finding title>
- Resolution status: pending
- Verification verdict: <verification verdict>
- Consolidated finding reference: `<consolidated_report_output_path>` finding 1
- Source reports:
  - `<report filename>.md`
- Consolidated severity: <consolidated severity>
- Re-evaluated severity: <re-evaluated severity>
- Evidence summary:
  - <decisive evidence>
- Confirmed aspects: <confirmed aspects>
- Not confirmed aspects: <not confirmed aspects>
- Resolution options: <resolution options>
- Advised resolution: <advised option number or none>
- User preference: <user preference or none>
- Why advised: <advised-option justification or n/a>
- Notes: <optional notes>

## Overall Recommendation
- <overall recommendation>
```

Rules:
- Add exactly one resolver-seed status line immediately under each finding title.
- Ensure the first two bullets in every finding are the resolver-seed status line and
  `Verification verdict:`.
- Use `Confirmation status: pending` instead of `Resolution status: pending` for findings whose
  verification verdict is `false_positive`. This does not close the issue as non-actionable; it
  seeds the resolver false-positive confirmation track, where the resolver can later record either
  a confirmed false positive or a disputed false positive reopened for normal resolution.
- Omit `User preference:` for findings whose verification verdict is `false_positive`; there is no
  actionable resolution option to force on that path.
- Use `Resolution status: pending` for all other findings.
- For non-`false_positive` findings, set `User preference: none` unless the user/operator
  explicitly provides a binding option choice such as `Option <N>`, `option <N>`, `<N>`, an
  English number word such as `one`, or one of those forms followed by extra instructions such as
  `with the following adjustment: ...`.
- If `User preference:` selects an option in any equivalent form (`Option <N>`, `option <N>`,
  `<N>`, or an English number word such as `one`), the resolver planner must follow that option
  plus any accompanying adjustment text, and must not override it with another option or `Custom`.
- A binding user preference controls only the resolution option and safe implementation adjustment;
  it cannot override resolver workflow policies, stage rules, tool restrictions, scope limits, or
  clean-worktree requirements. If it conflicts with those constraints, stop for clarification.
- When `User preference:` is binding, the resolver planner should not spend effort researching or
  ranking alternative options beyond the minimum needed to validate feasibility/safety and execute
  the selected path.
- If there are no consolidated findings, still write the report and state that there were no
  findings to investigate.
- If finding-verification agents fail, still write the report with completed evaluations and
  list missing investigations in `Investigation Status` or per-finding `Notes`.
- If a resolver workflow or human-resolution process begins later, update only
  `## Current Resolution State` and resolver-managed per-finding status/annotation lines; keep
  `## Investigation Status` and `## Overall Recommendation` as historical review-time sections.
- Do not introduce fresh findings that were not already present in the consolidated report; this
  phase is verification, severity recalibration, and resolution analysis only.
