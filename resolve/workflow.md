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
- Remediation attempts: 0
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
- `FALSE_POSITIVE_REVIEWER_PROMPT` =
  `<resolve_pack_base_path>/prompts/false-positive-reviewer.md`
- `PLANNER_PROMPT` = `<resolve_pack_base_path>/prompts/planner.md`
- `IMPLEMENTER_PROMPT` = `<resolve_pack_base_path>/prompts/implementer.md`
- `IMPLEMENTATION_REVIEWER_PROMPT` =
  `<resolve_pack_base_path>/prompts/implementation-reviewer.md`
- `SOURCE_DOCUMENT_UPDATER_PROMPT` =
  `<resolve_pack_base_path>/prompts/source-document-updater.md`

## Run Artifact Layout

All run artifacts live under `RUN_DOCS_DIR`.

- `RUN_DOCS_DIR/plans/` — planner outputs such as `finding-<NN>-<short-slug>-plan.md`
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
- The automated orchestrator must not read planner output content; human-driven/manual runs should
  preserve the same separation when practical and must not leak plan content to the independent
  implementation-review stage.
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
  `IMPLEMENTATION_REVIEWER_PROMPT`, `SOURCE_DOCUMENT_UPDATER_PROMPT`

## Stage I/O Summary

| Stage | Prompt / entry point | Role/profile | Required runtime inputs | Success output | Artifacts written |
|---|---|---|---|---|---|
| False-positive verification | `FALSE_POSITIVE_REVIEWER_PROMPT` | `reviewer` | current finding issue packet; optional issue-scoped excerpts or source-report locator paths; run-specific `reviews/`, `false-positive-disputes/`, and `handoff/` directories; optional handoff path and user answer | `confirmed: <absolute path>` or `disputed: <absolute path>` plus severity and summary | confirmation or dispute report; optional clarification handoff |
| Planning | `PLANNER_PROMPT` | `planner` | current finding issue packet; optional issue-scoped excerpts or source-report locator paths; run-specific `plans/` and `handoff/` directories; optional handoff path and user answer | absolute plan path under `RUN_DOCS_DIR/plans/` | plan file; optional clarification handoff |
| Implementation | `IMPLEMENTER_PROMPT` | `worker` | planner-returned absolute plan path; run-specific `handoff/` directory; optional handoff path and user answer | `done` | code/test/config/docs changes as needed; optional clarification handoff |
| Independent implementation review | `IMPLEMENTATION_REVIEWER_PROMPT` | `reviewer` | original current-finding issue packet; `finding-start-sha`; run-specific `plans/`, `reviews/`, and `handoff/` directories; optional handoff path and user answer | `pass: <absolute path>`, `needs_fix: <absolute path>`, or `blocked: <absolute path>` | review report; optional clarification handoff |
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
preferredResolution: <string | none | null>
whyPreferred: <string | n/a | null>
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

## Stage Runtime Assembly

Before first stage, coordinator verifies clean worktree, seeded `SOURCE_DOC` with at least one
pending finding, unique `RUN_ID`/branch/`RUN_DOCS_DIR`, and artifact dirs. Only exact
`Resolution status: pending` or `Confirmation status: pending` findings are eligible; any other
status value is treated as already closed history. See `orchestrator.md` or `manual.md` for
execution details.

Fill stage prompts only with Stage I/O inputs plus these constraints:

- False-positive reviewer/planner issue packets use the canonical schema plus only issue-scoped
  support.
- Source-report paths are locators, not permission to load whole reports. Use cited line ranges or
  smallest matching section; justify wider reads in the stage artifact.
- Implementer receives only planner-returned absolute plan path, `HANDOFF_DIR`, and optional
  continuation material.
- Implementation reviewer receives original issue packet, `finding-start-sha`, run dirs, and
  optional continuation material; never pass plan, implementer notes, command output, or
  plan-derived summaries.
- Source-document updater receives only the final outcome ledger matching `SOURCE_DOC_POLICY` and
  may edit only `SOURCE_DOC`.

## Manual Operation

For human-orchestrated full runs or direct single-stage runs, use
`<resolve_pack_base_path>/manual.md`. Keep this workflow file as the canonical source for aliases,
artifact layout, issue-packet schema, stage I/O, output contracts, and safety invariants.
