# Finding Verification Policy

Use this policy for post-consolidation per-finding verification.

## Task payload requirements

Each task must include:

1. review scope `<review_scope>`
2. diff baseline `<diff_baseline>`
3. exact consolidated finding entry
4. finding number and title
5. source report filenames plus spec, additional-context, project-guidance, and file:line references
   copied from the finding
6. read-only/non-delegating instructions: do not write files or spawn subagents
7. this policy

## Investigation rules

- Verify the assigned finding against the current branch and `<diff_baseline>`; do not assume it
  is correct.
- Return exactly one verdict: `confirmed | partially_confirmed | false_positive | inconclusive`.
- For `confirmed` or `partially_confirmed` findings:
  - re-evaluate severity as `blocker | major | minor`
  - for non-documentation findings, provide **2 or 3** plausible resolution options
  - for documentation-only findings, provide exactly **1** option naming the files/sections to
    change and what to say; treat a finding as documentation-only only when its category/scope is
    documentation and it is not also a production-code, test, config, data, or runtime issue
  - use `<documentation_reviewer_report_filename>` as an optional source-report hint for the
    documentation-only special case, not as the sole trigger when a finding is mixed-scope
  - nominate exactly one advised option
  - set `User preference: none`; this field is reserved for later user/operator override in the
    final evaluation report and must not be invented by verification
  - justify the advised option across minimal change surface, reviewability, maintainability,
    robustness, expressiveness, testability, and business impact
- If `<documentation_reviewer_report_filename>` is `none`, ignore the source-report hint and use
  the documentation-only special case only when the finding category/scope itself clearly qualifies.
- For `partially_confirmed`, separate confirmed aspects from disproven, unsupported, or
  overstated aspects; severity applies only to the confirmed part.
- For `false_positive`, explain decisive evidence/reasoning; set re-evaluated severity, options,
  advised resolution, and `User preference` to `none`.
- For `inconclusive`, explain the blocker, state the safest interim assessment, and describe what
  evidence would resolve uncertainty; use `Resolution options: none` unless a concrete
  evidence-gathering or containment next step is justified, and set `User preference: none` unless
  the task payload explicitly says otherwise.

## Output shape

```md
### Finding <finding number>. <finding title>
- Verdict: confirmed | partially_confirmed | false_positive | inconclusive
- Consolidated severity: blocker | major | minor | not_stated
- Re-evaluated severity: blocker | major | minor | none
- Evidence summary:
  - <decisive evidence>
- Verification reasoning: <verification reasoning>
- Confirmed aspects: <confirmed aspects>
- Not confirmed aspects: <not confirmed aspects>
- Resolution options: <resolution options>
- Advised resolution: <advised option number or none>
- User preference: none
- Why advised: <advised-option justification or n/a>
```
