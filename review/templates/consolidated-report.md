# Consolidated Report Template

Write the consolidated report to:
`<consolidated_report_output_path>`

Use this structure:

```md
# Consolidated Review Report

- Authoritative spec entry point: `<authoritative_spec_entry_point>`
- Specification base path: `<specification_base_path>`
- Diff baseline: `<diff_baseline>` or `not stated`
- Consolidated report path: `<consolidated_report_output_path>`
- Review reports base path: `<reports_base_path>`

## Input Status
- Present:
  - `<report filename>.md`
- Missing:
  - `<report filename>.md`, or `none`

## Consolidated Findings

### 1. <finding title>
- Severity: blocker | major | minor
- Category: <shared category value>
- Source reports:
  - `<report filename>.md`
- Problem: <merged explanation>
- Recommended follow-up: <merged follow-up>
- Notes on overlap/disagreement: <optional overlap note>

<details>
<summary>Spec references</summary>

- <spec references or none>
</details>

<details>
<summary>Additional context references</summary>

- <additional context references or none>
</details>

<details>
<summary>File:line</summary>

- <repo-root-relative path:line>
</details>

## Findings Grouped By Theme

### <theme_1>
- <finding numbers>

### <theme_2>
- <finding numbers>

### <theme_3>
- <finding numbers>

### <theme_4>
- <finding numbers>

## Reviewer Coverage Notes
- <coverage note>

## Final Prioritized Shortlist
1. <most important issue>
2. <next issue>

## Overall Assessment
- <overall synthesis>
```

If there are zero consolidated findings, write `No issues found` under `## Consolidated
Findings` and still keep input status and coverage notes.

When instantiating the pack, rename, add, or remove theme headings as needed; `<theme_1>`
through `<theme_4>` are editable starters, not a mandatory taxonomy.
