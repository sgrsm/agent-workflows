# Consolidation Policy

Use when consolidating area reviewer reports into the consolidated review report.

## Rules

- Do not add technical findings from fresh code review.
- Rephrase, merge, rank, and organize only findings present in reviewer reports.
- Merge duplicate findings into one consolidated finding and list all source reports.
- Keep partial overlaps separate unless they clearly share the same root problem.
- If severities differ, choose the higher severity and briefly note the disagreement.
- Read accepted/cited context only when needed to reconcile report disagreement or cited context;
  never invent findings from context alone.
- Record missing expected input reports.
- If all available reports say `No issues found`, say so clearly.

## Priorities

- remove duplicates
- keep output human-consumable and decision-oriented
- keep quality/architecture findings visible, not only spec/correctness findings
- preserve actionable follow-up guidance and cross-area themes from `workflow.md`

## Path and reference rules

- Use repo-root-relative paths; never `../` or `../../`.
- Normalize copied paths to repo-root-relative form.
- Write the shared reviewer-report base path once near the top, then list only report filenames in
  `Input Status` and `Source reports`.
- At the bottom of each finding, write separate collapsible `<details>` blocks for
  `Spec references`, `Additional context references`, and `File:line`.
- For spec/context blocks, group nested references by file. Do not inline multiple section,
  paragraph, or criteria refs on one line. Nest child criteria/items under their parent.
- Keep `File:line` entries repo-root relative and as a list inside the collapsible block.

## Coverage notes

- Cite source report filenames under the report-level review-reports base path.
- If a finding relies on accepted ADR/contract/context for reconciliation, cite it under
  `Additional context references`.
- Summarize useful coverage matrices or checked-with-no-issue sections only where useful for final
  consumption.
- If a no-finding report demonstrates meaningful coverage, record that in `Reviewer Coverage Notes`.
