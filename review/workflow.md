# Review Workflow

This file is the canonical role, path, output, clean-output-gate, and task-assembly map for the
instantiated review prompt pack. Use repository-root-relative paths everywhere.

## Constants

- Review scope: `<review_scope>`
- Documentation review scope: `<documentation_review_scope>`
- Diff baseline: `<diff_baseline>`
- Working directory for top-level agents: repository root
- Specification entry point: `<authoritative_spec_entry_point>`
- Specification base path: `<specification_base_path>`
- Accepted context docs:
  <accepted_context_docs_block>
- Project guidance docs:
<project_guidance_docs_block>
- Reports base path: `<reports_base_path>`

## Task component paths

### Reviewer components

- Reviewer workflow: `<review_pack_base_path>/fragments/reviewer-workflow.md`
- Reporting rules: `<review_pack_base_path>/fragments/reporting-rules.md`
- Reference format: `<review_pack_base_path>/fragments/reference-format.md`
- Area report template: `<review_pack_base_path>/templates/area-report.md`
- Scout delegation policy: `<review_pack_base_path>/policies/scout-delegation.md`

### Consolidator components

- Consolidation policy: `<review_pack_base_path>/policies/consolidation.md`
- Consolidated report template: `<review_pack_base_path>/templates/consolidated-report.md`

### Post-consolidation components

- Verifier prompt: `<review_pack_base_path>/prompts/verifier.md`
- Finding verification policy: `<review_pack_base_path>/policies/finding-verification.md`
- Final evaluation template: `<review_pack_base_path>/templates/final-evaluation.md`

## Top-level reviewer agents

Each `Prompt file` should point to a concrete copied reviewer prompt whose per-area placeholders are
resolved for that row. The source files `prompts/reviewers/area-reviewer.md` and
`prompts/reviewers/tests-reviewer.md` must not be shared as unresolved runtime prompts. In an
instantiated pack, the default tests row may point at `prompts/reviewers/tests-reviewer.md` only
after that target file has been copied/enriched and resolved for that one tests reviewer. Use
consecutive zero-padded two-digit reviewer IDs and matching report filename prefixes in table order:
`01` with `01-...`, `02` with `02-...`, and so on. Do not number area reports as `10-`, `20-`,
etc. unless the user explicitly asks for that scheme.

| ID | Reviewer | Role/profile | Prompt file | Report template | Expected report |
|---|---|---|---|---|---|
<reviewer_rows_block>

## Consolidator agent

- Consolidator: Consolidated review synthesis
- Role/profile: `worker`
- Prompt file: `<review_pack_base_path>/prompts/consolidator.md`
- Task components, in assembly order:
  1. `<review_pack_base_path>/workflow.md`
  2. `<review_pack_base_path>/policies/consolidation.md`
  3. `<review_pack_base_path>/templates/consolidated-report.md`
- Expected consolidated report: `<consolidated_report_output_path>`

## Post-consolidation finding verification

- Role/profile: `reviewer`
- Base prompt file: `<review_pack_base_path>/prompts/verifier.md`
- Source report: `<consolidated_report_output_path>`
- Task cardinality: one finding-verification subagent per consolidated finding
- Task assembly: verifier prompt file, one finding-specific payload, and
  `<review_pack_base_path>/policies/finding-verification.md`
- Output handling: finding-verification subagents return results to the parent session only;
  only the main orchestration agent writes the final evaluation report

## Final evaluation artifact

- Written by: main orchestration agent
- Template: `<review_pack_base_path>/templates/final-evaluation.md`
- Output path: `<final_evaluation_output_path>`

## Clean-output gate expected files

Before a full orchestrated run, these files must not already exist:

<clean_output_gate_files_block>

## Delegated task assembly summary

### Reviewer task assembly

For each top-level reviewer row, concatenate exact file contents in this order:

1. the row's `Prompt file`
2. reviewer workflow component
3. reporting-rules component
4. reference-format component
5. the row's `Report template`
6. scout-delegation policy

### Consolidator task assembly

Concatenate exact file contents in this order:

1. consolidator prompt file
2. this workflow file
3. consolidation policy
4. consolidated report template

### Finding-verification task assembly

For each consolidated finding, concatenate task content in this order:

1. verifier prompt file
2. finding-specific payload containing:
   - review scope
   - diff baseline
   - exact consolidated finding entry
   - finding number and title
   - copied source report filenames plus spec, additional-context, and file:line references
   - read-only / non-delegating instructions
3. finding-verification policy
