# False-Positive And Dispute Policy

Governs findings whose final evaluation verdict is `false_positive`.

This policy assumes `SOURCE_DOC` follows the current seeded final-evaluation schema from:

`storage-engine-facade/service/docs/agent-templates/review/templates/final-evaluation.md`

Companion files: main orchestrator `<resolve_pack_base_path>/orchestrator.md`, false-positive
reviewer prompt `<resolve_pack_base_path>/prompts/false-positive-reviewer.md`.

This workflow may be driven either by the main orchestrator or by a human operator running the same
stage prompts sequentially. The semantics, artifacts, and final status mapping stay the same.

## Core Rules

1. False-positive handling is verification-first.
2. Confirmed false positives stay on the verification track.
3. Disputed false positives are not terminal.
4. `false_positive_disputed` is an intermediate report verdict, not a final per-finding resolver
   status.
5. Disputes are queued for same-run planner -> implementer -> reviewer follow-up after the primary
   pass.
6. A dispute report must independently re-evaluate severity for the reopened issue; do not trust or
   copy severity from the source document.
7. Reopened follow-up uses a narrow issue definition: dispute report + isolated original
   source-report finding section, not consolidated report, final evaluation report, or full source
   report.

## Entry Condition

If a finding is explicitly `false_positive` with `Resolution options: none` and
`Preferred resolution: none`, run this verification workflow first. Do not auto-close as
non-actionable.

## Primary Verification Outcomes

Spawn exactly one independent false-positive reviewer.

- confirmed -> ledger `false_positive_confirmed`; preserve confirmation with documentation-only
  commit
- disputed -> preserve dispute report with documentation-only commit; queue follow-up; no final
  ledger status yet
- user clarification -> follow `CONTINUATION_POLICY`; no final ledger status while waiting
- blocked/unverifiable -> ledger `verification_blocked`; preserve useful docs only if safe; clean
  up before continuing

## Reviewer Artifacts

Confirmation document must include these exact parseable labels near the top:

- `Verdict: false_positive_confirmed`
- `Confirmation summary:` concise confirmation evidence
- `Residual uncertainty:` residual uncertainty, or `none`

It must also include important code/spec/test refs with line numbers where practical.

Dispute report must include these exact parseable labels near the top:

- `Verdict: false_positive_disputed`
- `Independent re-evaluated severity:` `blocker`, `major`, `minor`, or `severity-unknown`; do not
  trust/copy source-document severity
- `Severity rationale:` brief rationale for the independent severity
- `Open issue summary:` concise restatement of the still-open issue for planner follow-up
- `Source report locator:` repo-relative path(s), relevant chapter/section heading(s), and line
  range(s) when available; if mapping is uncertain, record best locator and uncertainty
- `Final evaluation locator:` `<source_doc_path>` plus current finding heading

It must also include concise explanation of why the false-positive classification is incorrect or
unsupported, evidence from code/tests/specs/review materials with file paths/line numbers where
practical, likely affected code/spec/test surfaces or open questions, and recommended next
action/investigation direction.

Dispute reports must be self-contained enough for a later planner without rereading the whole
resolver run.

## Lazy Source-Report Extraction

During prepare/primary pass, capture only minimal source-report locator(s), usually repo-relative
path + heading + line range when available. Do not eagerly extract full source-report sections, and
do not treat a report path as permission to load the whole report.

Only after a false-positive dispute, recover the isolated original source-report finding section
for reopened follow-up:

- isolate the original finding section only
- prefer span from relevant heading to next sibling heading
- if boundaries are uncertain, use the smallest faithful excerpt and note uncertainty

## Reopened Follow-Up

After the primary pass, process queued disputes in original finding order.

For each:

1. verify clean worktree
2. record fresh `<finding-start-sha>`
3. lazily recover isolated original source-report section if not already extracted
4. build the reopened issue packet below
5. treat that packet as authoritative for planner -> implementer -> reviewer
6. do not provide consolidated report, final evaluation report, or full source report to the
   planner unless later required by the user
7. run normal actionable workflow
8. record only a normal final ledger status: `resolved`, `blocked`, `implementation_failed`,
   `review_failed`, `verification_blocked`, `skipped_by_user`, or `non_actionable`
9. preserve reopened history in reconciliation with
   `Resolver note: reopened after disputed false-positive verification`

This follow-up may be run by the automated orchestrator or manually via the normal planner ->
implementer -> reviewer stage prompts, as long as the same narrowed issue packet is preserved.

## Reopened Issue Packet Schema

Include only:

- finding number/title
- explicit reopened-after-dispute note
- repo-relative dispute report path
- independently re-evaluated severity from the dispute report, plus rationale
- repo-relative original source report path, heading, and line range when available
- isolated original source-report finding section, not consolidated/final/full source report
- source-report section heading; include source-report severity only as historical context if
  identifiable, not for commit labeling
- minimum extra context needed for commit labeling or reviewability

## Planner Narrowing Rule

For reopened disputed false positives:

- dispute report + isolated original source-report finding section are the primary issue definition
- use the dispute report's independently re-evaluated severity for follow-up/commit-label context;
  do not fall back to source-document severity unless the user explicitly instructs
- ignore earlier final-evaluation `Resolution options: none`
- do not widen to consolidated report, final evaluation report, or full source report unless truly
  necessary and explicitly justified

## Source-Document History Rule

Reconciled reopened findings end with a normal final resolver status, not `false_positive_disputed`,
and must include `Resolver note: reopened after disputed false-positive verification`. Historical
`Verification verdict: false_positive` may remain in the review-time bullets; that tension is
intentional. Final source-document reconciliation must preserve original severity as
`Severity (original)` and record the dispute report's independent severity as
`Severity (re-evaluated)`.
