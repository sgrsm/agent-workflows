# Resolve Workflow

This file is the canonical path, naming, artifact, stage-I/O, issue-packet, and routing manifest
for the instantiated resolve prompt pack. Use repository-root-relative paths everywhere.

## Scope And Schema Assumptions

- Target-scope reuse boundary: repo-relative scope anchored by `<source_doc_path>` and
  `<review_reports_dir>`
- Feature name: `<feature_name>`
- Feature slug: `<feature_slug>`
- Resolve pack base path: `<resolve_pack_base_path>`
- Source document path: `<source_doc_path>`
- Review reports directory: `<review_reports_dir>`
- Supported seeded source schema:
  `review/templates/final-evaluation.md`
- Top-level working directory: repository root

## Core Defaults And Patterns

- Max concurrent top-level subagents: 1 active top-level stage at a time
- Max child scouts per top-level subagent: 3
- Max remediation attempts per actionable finding: 1
- Run id pattern: `<run_id_pattern>`
- Branch name pattern: `<branch_name_pattern>`
- Run docs base path: `<run_docs_base_path>`
- Final report output path pattern: `<final_report_output_path>`
- Commit message templates:
  - resolved finding: `Resolve #<N><severityPart>: <title>`
  - unresolved finding artifact: `Document unresolved #<N><severityPart>: <title>`
  - confirmed false positive: `Confirm false positive #<N><severityPart>: <title>`
  - disputed false positive: `Dispute false positive #<N><severityPart>: <title>`
  - final resolver summary: `Document resolver summary`
- Commit subject hygiene: when expanding `<title>`, use a concise sanitized finding title or short
  summary; strip newlines and Markdown decoration, preserve `#<N>` and `<severityPart>`, and keep
  the subject reasonably short, ideally under about 100 characters.
- Commit severity selection:
  - for non-reopened actionable findings, use the finding's review-time `Re-evaluated severity`
    from `SOURCE_DOC`
  - for disputed-false-positive documentation commits and reopened follow-up commits, prefer the
    dispute report's independently re-evaluated severity, then the implementation reviewer's
    `Effective follow-up severity for commit labeling:` field
  - for confirmed false positives, treat selected severity as `none`
  - never use source-document false-positive severity as fallback for reopened disputed false
    positives
- Commit-severity suffix rule: append ` [<severity>]` only when the selected severity is present
  and not `none` or `severity-unknown`; otherwise omit the suffix entirely
- Run artifact subdirectories: `plans/`, `reviews/`, `false-positive-disputes/`, `handoff/`,
  `final/`

## Canonical Path Aliases

- `SOURCE_DOC` = `<source_doc_path>`
- `REVIEW_REPORTS_DIR` = `<review_reports_dir>`
- `RUN_ID` = runtime value matching the run-id pattern above
- `RUN_DOCS_DIR` = `<run_docs_base_path>/<RUN_ID>/`
- `FINAL_REPORT` = `<final_report_output_path>`
- `COMMON_STAGE_POLICY` = `<resolve_pack_base_path>/policies/common-stage-rules.md`
- `DELEGATION_POLICY` = `<resolve_pack_base_path>/policies/scout-delegation.md`
- `CONTINUATION_POLICY` = `<resolve_pack_base_path>/policies/continuation.md`
- `SOURCE_DOC_POLICY` = `<resolve_pack_base_path>/policies/source-document-reconciliation.md`
- `FP_POLICY` = `<resolve_pack_base_path>/policies/false-positive-and-disputes.md`
- `PROJECT_CONTEXT_FILES` = ordered repo-root-relative context file list in
  [Project Context Files](#project-context-files)
- `FALSE_POSITIVE_REVIEWER_PROMPT` =
  `<resolve_pack_base_path>/prompts/false-positive-reviewer.md`
- `PLANNER_PROMPT` = `<resolve_pack_base_path>/prompts/planner.md`
- `IMPLEMENTER_PROMPT` = `<resolve_pack_base_path>/prompts/implementer.md`
- `REMEDIATION_IMPLEMENTER_PROMPT` =
  `<resolve_pack_base_path>/prompts/remediation-implementer.md`
- `IMPLEMENTATION_REVIEWER_PROMPT` =
  `<resolve_pack_base_path>/prompts/implementation-reviewer.md`
- `IMPLEMENTATION_REVIEW_REPORT_TEMPLATE` =
  `<resolve_pack_base_path>/templates/implementation-review-report.md`
- `SOURCE_DOCUMENT_UPDATER_PROMPT` =
  `<resolve_pack_base_path>/prompts/source-document-updater.md`

## Project Context Files

Applicable repository instruction files for this instantiated resolve pack, ordered broadest to most
specific:

<project_context_files>

These files are explicit runtime inputs because descendant/module-local `AGENTS.md` files may not be
auto-loaded when the harness cwd is the repository root. If the list is exactly `- none`, there are
no project context files to read. Otherwise, the coordinator and every false-positive reviewer,
planner, implementer, remediation implementer, implementation reviewer, and scout must preserve this
list in stage or child-task prompts. Stage agents must read every listed file before issue-specific
inspection or implementation decisions. More-specific files add to or override parent instructions
for files under their scope. If a listed file is missing/unreadable, or if context instructions
conflict with a resolver policy or binding user preference, stop for clarification instead of
silently ignoring the instruction.

## Run Artifact Layout

All run artifacts live under `RUN_DOCS_DIR`.

- `RUN_DOCS_DIR/plans/` — ephemeral planner-to-implementer transfer files, such as
  `finding-<NN>-<short-slug>-plan.md`; the coordinator clears this directory's contents after the
  implementer returns `done` and before implementation review
- `RUN_DOCS_DIR/reviews/` — false-positive confirmations and implementation review reports
- `RUN_DOCS_DIR/false-positive-disputes/` — disputed false-positive reports
- `RUN_DOCS_DIR/handoff/` — continuation handoffs for user-clarification blockers
- `RUN_DOCS_DIR/final/` — final run summary directory
- `FINAL_REPORT` — expected final resolver summary, normally
  `<run_docs_base_path>/<RUN_ID>/final/100-resolution-final-report.md`

## Execution Invariants

- Start only from a clean worktree, including untracked files.
- Process findings in source-document order for the primary pass.
- Queue disputed false positives for exactly one later same-run actionable follow-up pass in that
  same original order.
- Use one active top-level stage at a time. In orchestrated mode this means one top-level subagent
  at a time.
- Optional child delegation is read-only `scout` only, subject to `DELEGATION_POLICY`.
- The coordinating layer handles Git/filesystem/artifact/commit/final-report work; the active stage
  handles only its own scoped task.
- Builds, tests, formatting, and code verification run only inside the appropriate stage or the
  equivalent manual operator step.
- Pass `PROJECT_CONTEXT_FILES` to every eligible stage and child scout.
- Planner output content is an ephemeral planner-to-implementer transfer artifact. The automated
  orchestrator must not read it; after the implementer returns `done`, the coordinator clears the
  contents of `RUN_DOCS_DIR/plans/` before spawning the independent implementation reviewer.
  Human-driven/manual runs should preserve the same separation and clear plans before review.
- At most one remediation implementer pass may run after an implementation reviewer returns
  `needs_fix` with `Remediation retry eligible: yes`. Remediation receives the review report, not
  the deleted plan or implementer notes, and may address only verdict-blocking current-finding
  follow-up before independent review runs again.
- Implementation plans are guidance, not hard scripts or scope boundaries; implementers follow them
  by default but may make reasonable, justified, finding-scoped adjustments/refinements when repo
  evidence or command results show a safer, simpler, or more verifiable path. They may adapt planned
  steps, in-scope files, and verification commands while preserving binding preferences, policies,
  selected-option intent, and acceptance needs.
- Implementation reviewers and their scouts must remain plan-blind.
- Until final reconciliation, record outcomes only in the in-memory/per-run outcome ledger; do not
  edit `SOURCE_DOC` early.
- Only findings whose `Resolution status:` or `Confirmation status:` is exactly `pending` are
  actionable in the primary pass. Any other status value—including legacy/manual markers such as
  `ignore`, `ignored`, `skip`, `skipped`, `resolved`, `non-actionable`,
  `non_actionable`, or `invalid`—counts as already closed history and must not be picked up
  again.

## Outcome Taxonomy

Final per-finding statuses:

- `false_positive_confirmed`
- `resolved`
- `blocked`
- `implementation_failed`
- `review_failed`
- `verification_blocked`
- `skipped_by_user`
- `non_actionable`

Intermediate status used only inside the false-positive workflow:

- `false_positive_disputed`

## Stage Files

Stage files are the aliases above.

- Control: `<resolve_pack_base_path>/workflow.md`, `<resolve_pack_base_path>/manual.md`,
  `<resolve_pack_base_path>/orchestrator.md`
- Policies: `COMMON_STAGE_POLICY`, `DELEGATION_POLICY`, `CONTINUATION_POLICY`,
  `SOURCE_DOC_POLICY`, `FP_POLICY`
- Prompts: `FALSE_POSITIVE_REVIEWER_PROMPT`, `PLANNER_PROMPT`, `IMPLEMENTER_PROMPT`,
  `REMEDIATION_IMPLEMENTER_PROMPT`, `IMPLEMENTATION_REVIEWER_PROMPT`,
  `SOURCE_DOCUMENT_UPDATER_PROMPT`
- Templates: `IMPLEMENTATION_REVIEW_REPORT_TEMPLATE`

## Stage I/O Summary

| Stage | Prompt / entry point | Role/profile | Required runtime inputs | Success output | Artifacts written |
|---|---|---|---|---|---|
| False-positive verification | `FALSE_POSITIVE_REVIEWER_PROMPT` | `reviewer` | `PROJECT_CONTEXT_FILES`; current finding issue packet; optional issue-scoped excerpts or source-report locator paths; run-specific `reviews/`, `false-positive-disputes/`, and `handoff/` directories; optional handoff path and user answer | `confirmed: <absolute path>` or `disputed: <absolute path>` plus severity and summary | confirmation or dispute report; optional clarification handoff |
| Planning | `PLANNER_PROMPT` | `planner` | `PROJECT_CONTEXT_FILES`; current finding issue packet; optional issue-scoped excerpts or source-report locator paths; run-specific `plans/` and `handoff/` directories; optional handoff path and user answer | absolute plan path under `RUN_DOCS_DIR/plans/` | ephemeral plan file, cleared after implementer success; optional clarification handoff |
| Implementation | `IMPLEMENTER_PROMPT` | `worker` | `PROJECT_CONTEXT_FILES`; planner-returned absolute plan path; run-specific `handoff/` directory; optional handoff path and user answer | `done` | code/test/config/docs changes as needed; optional clarification handoff |
| Review remediation | `REMEDIATION_IMPLEMENTER_PROMPT` | `worker` | `PROJECT_CONTEXT_FILES`; original current-finding issue packet; `finding-start-sha`; implementation review report with `Remediation retry eligible: yes`; run-specific `handoff/` directory; optional handoff path and user answer | `done` | bounded code/test/config/docs changes for verdict-blocking follow-up only; optional clarification handoff |
| Independent implementation review | `IMPLEMENTATION_REVIEWER_PROMPT` | `reviewer` | `PROJECT_CONTEXT_FILES`; original current-finding issue packet; `finding-start-sha`; run-specific `plans/`, `reviews/`, and `handoff/` directories; optional handoff path and user answer | `pass: <absolute path>`, `needs_fix: <absolute path>`, or `blocked: <absolute path>` | review report; optional clarification handoff |
| Source-document update | `SOURCE_DOCUMENT_UPDATER_PROMPT` | `worker` | final per-finding outcome ledger matching `SOURCE_DOC_POLICY` | `done` | edits to `SOURCE_DOC` only |

## Canonical Issue Packet Schema

Use this compact schema for every current-finding issue packet. Omit fields only when genuinely
unknown; do not include unrelated findings, full reports, or plan/implementation notes.

```yaml
findingNumber: <integer>
title: <string>
sourceDocHeading: <string>
resolverStatusLine: <string>
consolidatedFindingReference: <string | null>
sourceReportLocators:
  - path: <repo-relative path under REVIEW_REPORTS_DIR>
    heading: <best available heading or locator>
    lines: <start-end | null>
verificationVerdict: confirmed | partially_confirmed | false_positive | inconclusive
consolidatedSeverity: blocker | major | minor | none | severity-unknown | null
reEvaluatedSeverity: blocker | major | minor | none | severity-unknown | null
evidenceSummary:
  - <issue-specific evidence bullet>
confirmedAspects: <string | null>
notConfirmedAspects: <string | null>
resolutionOptions: <string | list | none>
advisedResolution: <string | none | null>
userPreference: <string | none | null>
userPreferenceOptionNumber: <integer | null>
userPreferenceAdjustment: <string | null>
whyAdvised: <string | n/a | null>
notes: <string | null>
reopenedAfterDisputedFalsePositiveVerification: true | false
disputeReportPath: <repo-relative path | null>
disputeSeverity: blocker | major | minor | severity-unknown | null
disputeSeverityRationale: <string | null>
isolatedOriginalSourceReportSection: <string | null>
```

For normal primary-pass findings, reopened/dispute fields are `false` or `null`. For reopened
disputed false positives, the dispute report plus isolated original source-report section become
the primary issue definition under `FP_POLICY`.

`userPreference` rules:

- `advisedResolution` carries the verifier/reviewer-advised option from `SOURCE_DOC`, usually an
  option number like `1` or `none`.
- `userPreference` carries the raw `User preference:` text from `SOURCE_DOC`.
- `none`, `null`, or an omitted `User preference:` line means no binding user override; the planner
  may choose normally. Omitted lines are expected for false-positive findings in the final
  evaluation report.
- When `userPreference` binds an option, the coordinator must normalize it into
  `userPreferenceOptionNumber` plus optional `userPreferenceAdjustment`.
- Binding option selectors are case-insensitive and equivalent across these leading forms:
  `Option <N>`, `option <N>`, bare `<N>`, or an English number word such as `one`, `two`, or
  `three`.
- Any trailing text after the normalized option selector, such as
  `with the following adjustment: ...`, is binding and must be preserved in
  `userPreferenceAdjustment`.
- A binding user preference controls only the resolution option and safe implementation adjustment;
  it cannot override resolver workflow policies, stage rules, tool restrictions, scope limits, or
  clean-worktree requirements. If it conflicts with those constraints, stop for clarification.
- If `userPreferenceOptionNumber` is non-null, the planner must follow that option number and must
  not switch to another option or `Custom`.
- When a binding user preference exists, the planner should not spend effort researching,
  comparing, or ranking alternative options beyond the minimum needed to safely implement the bound
  option and validate whether the bound option/adjustment is feasible.
- `Option <N>`, `Option <N> (recommended)`, and `Option <N> with slight refinement` are all
  acceptable downstream labels only when they still materially implement the same chosen option
  number and intent.
- If the bound option or binding adjustment is impossible, unsafe, stale, or inconsistent with the
  listed options, stop for user clarification instead of overriding it.
- When `resolutionOptions` is `none`, `userPreferenceOptionNumber` and
  `userPreferenceAdjustment` must be `null`, and `userPreference` must be `none`, `null`, or
  omitted in `SOURCE_DOC`.

## Stage Runtime Assembly

Before first stage, coordinator verifies clean worktree, seeded `SOURCE_DOC` with at least one
pending finding, unique `RUN_ID`/branch/`RUN_DOCS_DIR`, and artifact dirs. Only exact
`Resolution status: pending` or `Confirmation status: pending` findings are eligible; any other
status value is treated as already closed history. See `orchestrator.md` or `manual.md` for
execution details.

Fill stage prompts only with Stage I/O inputs plus these constraints:

- Include the exact `PROJECT_CONTEXT_FILES` list from this workflow in false-positive reviewer,
  planner, implementer, remediation implementer, and implementation-reviewer prompts; do not pass
  only an implicit harness context assumption.
- False-positive reviewer/planner issue packets use the canonical schema plus only issue-scoped
  support, including raw `userPreference` plus normalized `userPreferenceOptionNumber` and
  `userPreferenceAdjustment` when present in `SOURCE_DOC`.
- Source-report paths are locators, not permission to load whole reports. Use cited line ranges or
  smallest matching section; justify wider reads in the stage artifact.
- Implementer receives only planner-returned absolute plan path, `HANDOFF_DIR`, and optional
  continuation material.
- After the implementer returns `done`, the coordinator clears the contents of the run-specific
  plans directory (`RUN_DOCS_DIR/plans/`, passed to stage prompts as `PLANS_DIR`) without reading
  plan contents. Plan files are not final trace artifacts and must not be committed.
- Implementation reviewer receives original issue packet, `finding-start-sha`, run dirs, and
  optional continuation material; never pass plan, implementer notes, command output, or
  plan-derived summaries.
- Source-document updater receives only the final outcome ledger matching `SOURCE_DOC_POLICY` and
  may edit only `SOURCE_DOC`.

## Manual Operation

For human-orchestrated full runs or direct single-stage runs, use
`<resolve_pack_base_path>/manual.md`. Keep this workflow file as the canonical source for aliases,
artifact layout, issue-packet schema, stage I/O, output contracts, and safety invariants.
