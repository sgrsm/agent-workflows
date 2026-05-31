# Reviewer Prompt — Area Review

After copying/renaming this template and resolving its per-area placeholders, direct/manual runs
must apply reviewer task assembly from `<review_pack_base_path>/workflow.md`, including
`<review_pack_base_path>/policies/scout-delegation.md`.

Use this as a source template for one non-test review row, for example business flow, integration
and resilience, persistence, runtime and operability, or documentation follow-up. Copy and rename
this file for the concrete feature-specific review area before use, then replace every per-area
placeholder in the copy.

## Mandatory output file

Write full markdown report to:
`<report_output_path>`

## Additional required context

Use this section when the workflow row needs extra ADRs, contracts, or adjacent reference docs. If
none are needed, replace the body with `none` or remove the section in the instantiated copy.

<additional_required_context_block>

## Area contract

- Repository area: `<repository_area>`
- Review area: `<review_area>`
- Report title: `<report_title>`
- Scope summary: `<scope_summary>`
- Finding categories: `<finding_categories>`
- Additional context defaults: `<additional_context_defaults_line>`
- Additional context checked default: `<additional_context_checked_defaults_line>`

## Relevant spec sections

<relevant_spec_sections_block>

## Primary files

<primary_files_block>

## Focus checklist

<focus_checklist_block>

## Out of scope

<out_of_scope_block>

## Report specialization

Use `<review_pack_base_path>/templates/area-report.md` with area contract values; report
path:
`<report_output_path>`.
