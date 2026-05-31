# Area Report Template

Write the assigned area report using this structure. If there are no findings, keep `## Findings`
and write `No issues found.` instead of numbered finding entries.

```md
# <report title>

- Scope summary: <scope summary>.

<optional pre-findings sections or omit>

## Findings

### 1. <finding title>
- Severity: blocker | major | minor
- Category: <finding category>
- Problem: <problem statement>
- Recommended follow-up: <recommended follow-up>

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

- <repo-root-relative path:line or range>
</details>

## Checked With No Issue Found
- <checked item>

## Overall Assessment
- <overall assessment>

- Diff baseline: `<diff_baseline>`
- Report path: use the mandatory output path from the concrete reviewer prompt
- Specification base path: `<specification_base_path>`

<details>
<summary>Spec sections checked</summary>

- <spec sections checked>
</details>

<details>
<summary>Additional context checked</summary>

- <additional context checked or none>
</details>

<details>
<summary>Files inspected</summary>

- <files inspected grouped by directory>
</details>
```
