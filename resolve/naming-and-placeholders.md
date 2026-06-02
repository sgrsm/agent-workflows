# Naming And Placeholders Guide

Source-template helper for instantiating `resolve/` into a feature-specific resolver
pack. Not required in instantiated runtime packs unless copied for operator/reference use.

## File Naming

- Runtime control files: `README.md`, `workflow.md`, `manual.md`, `orchestrator.md`.
- Template-only helpers in source template: `instantiate.md`, `naming-and-placeholders.md`; copy
  only when deliberately wanted for operator/reference use.
- Policies under `policies/`, stable lowercase kebab-case:
  - `policies/common-stage-rules.md`
  - `policies/scout-delegation.md`
  - `policies/continuation.md`
  - `policies/source-document-reconciliation.md`
  - `policies/false-positive-and-disputes.md`
- Stage prompts under `prompts/`, stable lowercase kebab-case:
  - `prompts/false-positive-reviewer.md`
  - `prompts/planner.md`
  - `prompts/implementer.md`
  - `prompts/remediation-implementer.md`
  - `prompts/implementation-reviewer.md`
  - `prompts/source-document-updater.md`
- Report/output templates under `templates/`, stable lowercase kebab-case:
  - `templates/implementation-review-report.md`
- Finding-scoped run artifacts stay stable and zero-padded, for example:
  - `finding-<NN>-<short-slug>-plan.md` for ephemeral planner-to-implementer transfer files
    that are cleared after implementation succeeds
  - `finding-<NN>-<short-slug>-review.md`
  - `finding-<NN>-<short-slug>-false-positive-confirmation.md`
  - `finding-<NN>-<short-slug>-false-positive-dispute.md`
  - `finding-<NN>-<short-slug>-planner-clarification-handoff.md`
- Final run summary filename: `100-resolution-final-report.md`.
- Keep `RUN_DOCS_DIR` subdirectory names synchronized with `workflow.md` if artifact layout is
  renamed or extended.

## Title Conventions

- Template guide: `# Resolver Template Pack`
- Canonical manifest: `# Resolve Workflow`
- Manual guide: `# Manual Resolver Operation Guide`
- Main entry point: `# Main Orchestrator Prompt — Review Finding Resolution`
- Instantiation prompt: `# Resolver Pack Instantiation Prompt`
- Policy and stage-prompt titles stay generic/responsibility-based, not feature-specific.

## Placeholder Rules

Three contexts:

1. **Instantiation placeholders** — replace when copying the template into a real resolver pack.
2. **Runtime placeholders** — keep for orchestrator, stage prompts, or generated artifacts.
3. **Example/schema placeholders** — keep only inside examples, output contracts, and YAML schemas.

Files may mix contexts; e.g. a final-report path can combine instantiation
`<run_docs_base_path>` with runtime `<RUN_ID>`. Markdown/HTML tags used for rendering, such as
`<details>`, `</details>`, `<summary>`, and `</summary>`, are not placeholders.

### Placeholder Allowlist

| Context | Allowed placeholders | Where they may remain after instantiation |
|---|---|---|
| Instantiation, replace before use | `<feature_name>`, `<feature_slug>`, `<resolve_pack_base_path>`, `<source_doc_path>`, `<review_reports_dir>`, `<run_docs_base_path>`, `<final_report_output_path>`, `<run_id_pattern>`, `<branch_name_pattern>`, `<project_context_files>` | Nowhere in the instantiated active pack, except explicit notes documenting the template source. |
| Runtime orchestration/task assembly | `<RUN_ID>`, `<run_id>`, `<finding-start-sha>`, `<starting-commit-sha>`, `<severityPart>`, `<handoff>`, `<NN>`, `<N>`, `<short-slug>`, `<title>`, `<severity>`, `<repo-relative PLANS_DIR>`, `<repo-relative REVIEWS_DIR>`, `<repo-relative HANDOFF_DIR>`, any placeholder beginning with `<absolute ...>`, and any `<insert ...>` slot | `workflow.md`, `manual.md`, `orchestrator.md`, stage prompts, output contracts, and generated run artifacts. |
| Example/schema text only | `<integer>`, `<number>`, `<string>`, schema unions such as `<string \| null>`, `<repo-relative path \| null>`, or `<start-end \| null>`, `<dir>`, `<issue-specific evidence bullet>`, `<repo-relative path>`, `<repo-relative path under review/reports/>`, `<repo-relative path under REVIEW_REPORTS_DIR>`, `<best available heading or locator>`, `<original severity>`, `<re-evaluated severity>`, `<short blocker summary>`, `<short failure summary>`, `<short question>`, `<1-2 sentence ...>`, `<2-3 sentence ...>` | Markdown examples, schema blocks, and output-contract examples only. |

### Instantiation Placeholders

Replace before using the copied pack:

- `<feature_name>`: human-readable feature or resolver-pack name.
- `<feature_slug>`: lowercase kebab-case feature id for branches/examples.
- `<resolve_pack_base_path>`: repo-root-relative path to instantiated pack.
- `<source_doc_path>`: repo-root-relative seeded final-evaluation doc updated at run end.
- `<review_reports_dir>`: repo-root-relative directory with reports referenced by `SOURCE_DOC`.
- `<run_docs_base_path>`: repo-root-relative base for per-run resolver artifacts.
- `<final_report_output_path>`: final run-summary path pattern, normally still containing runtime
  `<RUN_ID>`, e.g. `<run_docs_base_path>/<RUN_ID>/final/100-resolution-final-report.md`.
- `<run_id_pattern>`: documented new-run id format.
- `<branch_name_pattern>`: documented branch naming pattern, usually using `<feature_slug>` and
  runtime run id.
- `<project_context_files>`: Markdown bullet list of confirmed repo-root-relative `AGENTS.md`
  context files ordered broadest to most specific, or exactly `- none` when no applicable files
  exist. Typical monorepo example:
  ```md
  - `AGENTS.md`
  - `path/to/module/AGENTS.md`
  ```

`<source_doc_path>` must point to a doc already seeded from
`review/templates/final-evaluation.md`.

### Runtime Placeholders

Do **not** replace these during instantiation; they are filled later by the orchestrator, stage
agent/operator, or generated artifacts.

- `<RUN_ID>` / `<run_id>`: current resolver run id.
- `<finding-start-sha>`: SHA captured before work on one actionable finding.
- `<starting-commit-sha>`: SHA captured before overall resolver branch work.
- `<severityPart>`: commit-message suffix from `workflow.md` severity rules.
- `<handoff>`: continuation handoff path returned by a stage.
- `<NN>` / `<N>` / `<short-slug>` / `<title>` / `<severity>`: runtime filename, branch, commit, or
  report values.
- `<repo-relative PLANS_DIR>` / `<repo-relative REVIEWS_DIR>` / `<repo-relative HANDOFF_DIR>`:
  runtime repo-relative artifact dirs used in plan-blind diff examples.
- `<absolute ...>` placeholders: runtime paths from prior stages or the operator.
- `<insert ...>` placeholders: task-assembly slots supplied by orchestrator/operator.

For direct/manual stage runs, `workflow.md` is canonical for exact runtime inputs. Common inputs:
current-finding issue packet; issue-scoped excerpts/locators; run-specific `plans/`, `reviews/`,
`false-positive-disputes/`, and `handoff/` dirs; optional handoff path/user answer;
planner-returned absolute plan path; implementation review report path for bounded remediation;
same-finding prior `needs_fix` review report path for post-remediation implementation review;
cleared `plans/` before implementation review; `finding-start-sha`; final outcome ledger for
source-doc update.

### Example/Schema Placeholders

Do **not** treat angle-bracket slots inside examples, output contracts, or YAML schemas as
instantiation placeholders; classify them by the allowlist table.

## Linking Rules

- Use repo-root-relative paths everywhere in instantiated packs.
- Same-pack template refs stay as `<resolve_pack_base_path>/...` until instantiation, then become
  actual repo-root-relative paths.
- `workflow.md` is canonical for paths, artifact layout, outputs, branch/run-id patterns,
  issue-packet schema, stage I/O, and routing.
- `manual.md` is a non-canonical human runbook and must not contradict `workflow.md`.
- Verify `<source_doc_path>` and `<review_reports_dir>` agree with each other and copied review
  outputs.
- Verify the Project context files list contains applicable repo-wide and module-local `AGENTS.md`
  files for the target scope. Use explicit path checks because these files may be ignored by Git or
  by search tools.

## Path And Workflow Validation

- Fill `workflow.md` first; other files should read naturally after replacement.
- Verify `<source_doc_path>` exists and has the resolver-seed shape required by
  `<resolve_pack_base_path>/policies/source-document-reconciliation.md`.
- Verify `<review_reports_dir>` exists and matches source-report refs cited in `SOURCE_DOC`.
- Verify `<project_context_files>` has been replaced by the confirmed context list; if it is not
  `- none`, every listed repo-root-relative path exists and is readable.
- Ensure `<run_docs_base_path>` exists or can be created during resolver runs.
- Ensure `<final_report_output_path>` resolves to the
  `RUN_DOCS_DIR/final/100-resolution-final-report.md` convention and normally contains runtime
  `<RUN_ID>`.
- Confirm policy refs under `<resolve_pack_base_path>/policies/`, prompt refs under
  `<resolve_pack_base_path>/prompts/`, and report-template refs under
  `<resolve_pack_base_path>/templates/` are correct.
- If artifact naming/layout changes, keep `README.md`, `workflow.md`, `manual.md`, stage prompts,
  and report templates synchronized.

## Instantiation Support

Interactive instantiation prompt:

`resolve/instantiate.md`

It collects feature/path values, copies runtime files/templates, replaces instantiation
placeholders, and runs a normal file/search audit. It avoids embedded validation scripts and does
not copy helper files by default.

## Manual Post-Instantiation Audit

Before running a manually instantiated pack, confirm:

- target pack contains `README.md`, `workflow.md`, `manual.md`, `orchestrator.md`, `policies/`,
  `prompts/`, and `templates/`
- active files contain no instantiation placeholders except explicit template-source notes
- remaining angle-bracket placeholders are allowed runtime or example/schema placeholders
- `SOURCE_DOC` exists, follows seeded resolver-ready shape, and has at least one pending
  resolver-managed status line
- `REVIEW_REPORTS_DIR` exists and matches expected source-report refs
- `RUN_DOCS_DIR` and `FINAL_REPORT` align with `workflow.md`; final report pattern retains
  `<RUN_ID>`
- Project context files list is confirmed, ordered broadest to most specific, and includes both
  repo-wide and module-local `AGENTS.md` files when applicable
- policy/prompt/template refs point to the instantiated target pack, not source template pack
- no old feature names, slugs, branch examples, paths, or source-doc refs remain in active files
- readiness is classified as ready for orchestrated use, ready for manual use, or blocked

## Cleanup Before Final Use

- Replace all instantiation placeholders in copied operator/control files.
- Keep only explicitly allowed runtime and example/schema placeholders.
- Remove stale source-feature names, paths, branch examples, and source-doc examples that do not belong.
- Confirm copied files still describe the intended resolver-pack scope and do not broaden
  the supported source-doc schema.
- Confirm no unresolved instantiation placeholders remain in active workflow files.
