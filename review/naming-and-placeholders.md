# Naming And Placeholders Guide

Use this helper when turning the review template pack under `review/` into a real
feature-specific review pack.

## File Naming

- Keep the canonical control files named `README.md`, `workflow.md`, and `orchestrator.md`.
- Template-only helpers in the source template: `instantiate.md`, `naming-and-placeholders.md`,
  `focus-checklist-catalog.md`; copy them only when deliberately wanted for operator/reference use.
- Keep shared task components under `fragments/`, `policies/`, `prompts/`, and `templates/`.
- Keep the base reviewer template filenames stable and lowercase kebab-case:
  - `prompts/reviewers/area-reviewer.md`
  - `prompts/reviewers/tests-reviewer.md`
- Treat `prompts/reviewers/area-reviewer.md` as a source template, not as a shared unresolved
  runtime prompt. Copy and rename it into one concrete prompt per non-test reviewer row, for
  example:
  - `prompts/reviewers/business-flow-reviewer.md`
  - `prompts/reviewers/integration-resilience-reviewer.md`
  - `prompts/reviewers/persistence-reviewer.md`
  - `prompts/reviewers/runtime-operability-reviewer.md`
  - `prompts/reviewers/documentation-reviewer.md`
  - `prompts/reviewers/clean-code-solid-reviewer.md`
- `prompts/reviewers/documentation-reviewer.md` is optional. Create it only if the user explicitly
  chooses a dedicated documentation reviewer; otherwise omit it and resolve
  `<documentation_reviewer_report_filename>` to `none`.
- For dedicated cross-cutting Java maintainability/design review, prefer one combined reviewer for
  most feature reviews using `prompts/reviewers/clean-code-solid-reviewer.md`. If the user wants a
  different shape, `prompts/reviewers/clean-code-reviewer.md` and/or
  `prompts/reviewers/solid-reviewer.md` are acceptable for split, Clean Code only, or SOLID only
  variants.
- Treat `prompts/reviewers/tests-reviewer.md` the same way for rows that need the extra test-gap
  ROI/practicality gate and optional pre-findings sections.
- For the default auto-generated tests/proof-strength reviewer created by the instantiator, use
  concrete filename `prompts/reviewers/tests-reviewer.md` unless the user explicitly overrides the
  filename or rejects the reviewer. If helper reviewer templates are also retained, do not keep a
  second source-template copy at that same path unless you intentionally rename it.
- Do not point multiple workflow rows at the same unresolved reviewer source template unless you
  also add explicit row-specific payload assembly to `workflow.md`.
- When instantiating report filenames in `workflow.md`, prefer zero-padded numeric prefixes for
  reviewer outputs. By default, use `99-consolidated-report.md` for the consolidated artifact and
  `100-final-evaluation.md` for the final evaluation unless the user explicitly overrides those
  names.
- Keep workflow rows, expected report filenames, and any copied reviewer customizations synchronized
  if you rename, remove, or add reviewer areas.

## Title Conventions

- Keep the template-pack guide title as `# Review Template Pack`.
- Keep the canonical manifest title as `# Review Workflow`.
- Keep the multi-agent entry point title as `# Main Orchestrator Prompt — Multi-Agent PR Review`.
- Keep the instantiation prompt title as `# Review Pack Instantiation Prompt`.
- Keep the template source titles clear and generic; if you fork a reviewer prompt for a
  feature-specific row, give the copy a responsibility-based title.
- Keep shared report-template titles generic; instantiated report titles should come from the area
  contract or runtime report content.

## Placeholder Rules

This pack uses two placeholder classes:

1. **Instantiation placeholders** — replace these when copying the template pack into a real review
   pack.
2. **Runtime report-skeleton placeholders** — leave these in the fenced report or verification
   skeletons so the review agent can fill them while writing output.

Some fenced Markdown skeletons contain both kinds at once. For example, path or baseline metadata
may still use instantiation placeholders, while finding text remains runtime data.

### Instantiation placeholders

Replace these in active workflow files before using the copied pack as an actual feature review workflow.

#### Core placeholders

- `<feature_name>`: human-readable feature or review-pack name used in operator-facing text.
- `<feature_slug>`: lowercase kebab-case identifier used when naming the instantiated feature folder or related files.
- `<review_pack_base_path>`: repo-root-relative path to the instantiated review-pack directory.
- `<review_scope>`: repo-root-relative scope reviewers should treat as the primary review boundary.
- `<documentation_review_scope>`: repo-root-relative scope for documentation-only review coverage when the workflow defines it.
- `<diff_baseline>`: branch comparison baseline, for example `main...HEAD`.
- `<authoritative_spec_entry_point>`: primary specification or overview document that anchors the review, or literal `none` for a diff-only review pack.
- `<specification_base_path>`: repo-root-relative base directory containing the feature specification set, or literal `none` for a diff-only review pack.
- `<reports_base_path>`: repo-root-relative directory where the instantiated reviewer reports are written.
- `<report_output_path>`: exact output path for one reviewer report; replace this separately in
  each copied reviewer prompt and do not leave it in the shared `templates/area-report.md`.
- `<consolidated_report_output_path>`: exact output path for the consolidated review report.
- `<final_evaluation_output_path>`: exact output path for the final evaluation report.
- `<documentation_reviewer_report_filename>`: documentation reviewer report filename used by finding verification, or `none` if the workflow has no dedicated documentation reviewer.

#### Block placeholders

- `<accepted_context_docs_block>`: full nested Markdown block placed under `Accepted context docs:` in `workflow.md`; preserve list indentation when replacing it.
- `<reviewer_rows_block>`: full Markdown table body for the top-level reviewer matrix in `workflow.md`.
- `<clean_output_gate_files_block>`: full Markdown bullet list of files that must not already exist before a clean orchestrated run.
- `<relevant_spec_sections_block>`: full Markdown bullet block naming the spec files and sections owned by a reviewer archetype, or `none` when the pack is intentionally diff-only.
- `<primary_files_block>`: full Markdown bullet block naming the primary code, config, doc, or SQL files for a reviewer archetype.
- `<focus_checklist_block>`: full Markdown bullet block describing the area-specific review checks. In non-test reviewer prompts, this is often generated from approved categories in `focus-checklist-catalog.md`. In `tests-reviewer.md`, this is for feature-specific additions beyond the built-in default checklist and may be `none`.
- `<out_of_scope_block>`: full Markdown bullet block listing the boundaries the reviewer should not expand past. In `tests-reviewer.md`, this is for feature-specific additions beyond the built-in default boundaries and may be `none`.
- `<additional_required_context_block>`: full Markdown block or `none` for extra ADR/contract/context documents the reviewer must read.
- `<special_report_sections_block>`: full Markdown block inserted before `## Findings` when an archetype wants extra pre-findings sections. In `tests-reviewer.md`, this is for additions beyond the built-in Coverage Matrix / optional Info sections and may be `none`.
- `<test_gap_roi_gate_block>`: full Markdown block describing extra test-gap ROI/practicality rules. In `tests-reviewer.md`, this is an addendum or stricter replacement note beyond the built-in default gate and may be `none`.

#### Theme placeholders

- `<theme_1>`: editable theme heading used in the consolidated report grouping section.
- `<theme_2>`: editable theme heading used in the consolidated report grouping section.
- `<theme_3>`: editable theme heading used in the consolidated report grouping section.
- `<theme_4>`: editable theme heading used in the consolidated report grouping section.

#### Reviewer-area placeholders

- `<repository_area>`: repo-root-relative repository area owned by a reviewer archetype.
- `<review_area>`: plain-language review responsibility statement for a reviewer archetype.
- `<report_title>`: report H1 title that the reviewer should write into the instantiated area report.
- `<scope_summary>`: one-line scope summary for the reviewer report metadata.
- `<finding_categories>`: allowed category values for findings in that reviewer area.
- `<additional_context_defaults_line>`: default value for additional-context references when the reviewer does not need extra context or cites none.
- `<additional_context_checked_defaults_line>`: default value for the `Additional context checked` metadata when an archetype tracks that separately.

### Runtime report-skeleton placeholders

Do **not** replace these when instantiating the template pack. They are filled in later by the
reviewer, consolidator, verifier, or orchestrator when a report or verification block is produced.

- `<additional context checked or none>`: runtime nested list of additional context checked, or `none`.
- `<additional context references or none>`: runtime nested accepted-context-reference content, or `none` when not applicable.
- `<checked item>`: runtime item that was checked with no issue found.
- `<confirmed aspects>`: runtime list or summary of what remains valid after verification.
- `<consolidated severity>`: runtime original severity from the consolidated report.
- `<coverage note>`: runtime note about reviewer coverage, gaps, or meaningful no-finding coverage.
- `<decisive evidence>`: runtime evidence bullet summarizing the strongest support for the verification result.
- `<files inspected grouped by directory>`: runtime grouped file list under shared parent directories.
- `<finding category>`: runtime category value chosen from the reviewer prompt's allowed categories.
- `<finding count>`: runtime numeric count used in investigation-status metadata.
- `<finding number>`: runtime finding index written by a reviewer or verifier.
- `<finding numbers>`: runtime list of consolidated finding numbers grouped under a theme heading.
- `<finding title>`: runtime concise title for a reviewer, consolidated, or verified finding.
- `<merged explanation>`: runtime synthesized problem statement for a consolidated finding.
- `<merged follow-up>`: runtime synthesized follow-up guidance for a consolidated finding.
- `<most important issue>`: runtime first shortlist entry in the consolidated report.
- `<next issue>`: runtime second shortlist entry in the consolidated report.
- `<not confirmed aspects>`: runtime list or summary of what was disproven, unsupported, or overstated.
- `<optional notes>`: runtime optional note for missing investigation details or extra context.
- `<optional overlap note>`: runtime note about overlap, disagreement, or leave empty/brief when unnecessary.
- `<optional pre-findings sections or omit>`: runtime optional Markdown block inserted before `## Findings`, such as a coverage matrix or informational section.
- `<overall assessment>`: runtime closing synthesis for one reviewer report.
- `<overall recommendation>`: runtime closing recommendation for the final evaluation report.
- `<overall synthesis>`: runtime final synthesis for the consolidated report.
- `<pending false-positive verification count>`: runtime count of findings whose current pending state is false-positive verification handling.
- `<pending finding count>`: runtime count of findings still pending resolver handling.
- `<preferred option number or none>`: runtime preferred remediation choice from the listed options, or `none`.
- `<preferred-option justification or n/a>`: runtime explanation for the preferred option, or `n/a` when no preference applies.
- `<problem statement>`: runtime explanation of why the finding matters.
- `<re-evaluated severity>`: runtime severity after verification.
- `<recommended follow-up>`: runtime fix direction or missing-proof follow-up.
- `<repo-root-relative path:line or range>`: runtime file reference in repo-root-relative `path:line` or `path:start-end` form.
- `<repo-root-relative path:line>`: runtime repo-root-relative file reference in single-line form.
- `<report filename>`: runtime reviewer-report filename without changing the shared reports base path.
- `<report title>`: runtime H1 title used in a generated area report.
- `<resolution options>`: runtime list of acceptable remediation options, or `none` when the policy requires none.
- `<scope summary>`: runtime area-specific scope sentence in a generated report.
- `<shared category value>`: runtime merged category value used in the consolidated report.
- `<spec references or none>`: runtime nested spec-reference content, or `none` when not applicable.
- `<spec sections checked>`: runtime nested list of spec sections the reviewer checked.
- `<verification reasoning>`: runtime explanation of why the verification verdict is correct.
- `<verification verdict>`: runtime verification verdict copied from the finding-verification result.

## Linking Rules

- Use repo-root-relative paths everywhere in the instantiated review pack.
- When a template file refers to a sibling file in the future instantiated pack, keep the reference
  in `<review_pack_base_path>/...` form inside the template.
- Treat `workflow.md` as the canonical source for paths, outputs, accepted context docs,
  reviewer-row definitions, clean-output gates, and task assembly.
- Verify every Markdown link or path-like reference after instantiation, especially anything copied
  into `workflow.md` or `README.md`.

## Path And Workflow Validation

- Fill `workflow.md` first; other files should read naturally after those values are resolved.
- Verify every repo-root-relative input path exists, unless the workflow explicitly says the file
  will be created by a review run.
- Ensure `<reports_base_path>` exists or can be created before the first review run.
- Keep accepted context docs, reviewer rows, copied reviewer prompt paths, consolidated/final report
  paths, and clean-output-gate files synchronized.
- Confirm every reviewer row points to a concrete copied reviewer prompt whose per-area placeholders
  are resolved for exactly that row.
- Keep the shared `templates/area-report.md` neutral: replace instantiation placeholders such as
  `<diff_baseline>` and `<specification_base_path>`, but do not insert one reviewer-specific report
  path there.
- If the instantiated workflow has no dedicated documentation reviewer, set
  `<documentation_reviewer_report_filename>` to `none`; the verifier will then rely on the finding's
  own category/scope to decide whether the documentation-only special case clearly applies.
- If a block placeholder expands to nested bullets or table rows, preserve the required Markdown
  indentation or table-row shape when replacing it.

## Instantiation Support

Interactive instantiation prompt:

`review/instantiate.md`

It collects core pack values, inspects the authoritative feature spec when available or the branch
diff when it is not, uses `focus-checklist-catalog.md` to propose non-test focus-checklist
categories, gets reviewer-area approval together with review-theme approval in that same first
choice block, then asks separate Clean Code / SOLID and documentation-reviewer choices. So the
minimum flow is three separate choice blocks: reviewer areas + themes, Clean Code / SOLID, and
documentation reviewer. It should not collapse the later two into the first block. It creates
concrete reviewer prompts, keeps a tests/proof-strength reviewer by default unless the user
explicitly rejects it, replaces instantiation placeholders, and runs a normal file/search audit. It
does not run the review workflow.

## Manual Post-Instantiation Audit

Before running a manually instantiated pack, confirm:

- target pack contains `README.md`, `workflow.md`, `orchestrator.md`, `fragments/`, `policies/`,
  `templates/`, `prompts/consolidator.md`, `prompts/verifier.md`, and one concrete reviewer prompt
  per workflow row
- no instantiation placeholders remain in active files except optional copied helper files or
  intentionally retained unreferenced source reviewer templates
- workflow reviewer rows point only to concrete reviewer prompts and reviewer expected report paths
  are unique
- instantiated `README.md` includes copy/paste-ready minimal prompt examples for the concrete
  orchestrator path plus the direct/manual single-agent entry points using actual local prompt
  paths
- a tests/proof-strength reviewer prompt exists unless it was explicitly rejected during
  instantiation; if it was auto-generated without an explicit filename override, it should be
  `prompts/reviewers/tests-reviewer.md`
- non-test reviewer focus-checklist blocks are concrete prompt text and do not leak raw catalog ids
  or helper prose unless the user explicitly wanted that wording
- `<documentation_reviewer_report_filename>` is `none` or matches one reviewer report filename
- clean-output gate matches reviewer reports plus consolidated/final outputs
- accepted context docs exist when specified
- spec entry point and specification base path both exist for spec-backed packs, or both are
  literal `none` for diff-only packs
- reports base path exists or can be created without path conflicts
- policy/prompt/template refs point to the instantiated target pack, not the source template pack
- no stale feature names, slugs, or old reviewer/report paths remain in active files
- readiness is classified as ready for orchestrated use, ready for direct/manual use, or blocked

## Cleanup Before Final Use

- Replace every instantiation placeholder in files referenced by `workflow.md` or the operator
  entry points before running the copied review pack.
- Remove or archive unresolved source reviewer templates after creating the concrete reviewer
  prompts, or clearly leave them as unreferenced source templates that are not part of the active
  workflow.
- Remove unused reviewer rows, copied reviewer variants, or theme headings that do not apply to
  the feature.
- Keep only the accepted context docs and output files that the real workflow needs.
- Confirm there are no unintended source-feature identifiers, stale paths, or unresolved
  instantiation placeholders left in active workflow files.
- Confirm the only remaining angle-bracket placeholders in active workflow files are the runtime
  placeholders inside the report and verification skeletons.
