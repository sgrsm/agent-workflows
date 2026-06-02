# Consolidator Prompt — Consolidated Review Synthesis

## Dependencies

Assembled with:

1. `<review_pack_base_path>/workflow.md`
2. `<review_pack_base_path>/policies/consolidation.md`
3. `<review_pack_base_path>/templates/consolidated-report.md`

For direct/manual runs, read those components first. `workflow.md` is canonical for expected inputs,
output paths, accepted context docs, project guidance docs, and task assembly. If instructions
conflict, follow this prompt without relaxing consolidation, traceability, reporting, or
path-formatting rules.

## Task

Synthesize existing reviewer reports into one consolidated review. You are not a primary reviewer:
do not modify code and do not invent findings absent from reviewer reports.
Do not spawn subagents during consolidation; read any needed accepted context, project guidance, or
cited context yourself.

- Authoritative spec entry point: `<authoritative_spec_entry_point>` (`none` for diff-only packs)
- Review scope: `<review_scope>`
- Expected reviewer inputs: reviewer `Expected report` paths in `<review_pack_base_path>/workflow.md`
- Output: `<consolidated_report_output_path>`
- Reconciliation context, only if needed: accepted context or project guidance docs listed in
  workflow or cited by source reports

## Workflow

1. Check every expected reviewer report path from workflow.
2. Read present reports only; record present and missing inputs.
3. Apply consolidation policy: dedupe, merge overlaps, preserve severity/category/spec/
   additional-context/file-line refs where possible.
4. For disagreements or cited context, read only accepted context, project guidance, or cited
   context needed to reconcile.
5. If an authoritative spec exists, use the spec overview only to normalize or explain split-spec
   citations; otherwise skip spec reconciliation. Do not create findings from context alone.
6. Write output using the consolidated report template.
7. Return brief outcome, highest-priority findings if any, and saved report path; do not repeat the
   full report.
