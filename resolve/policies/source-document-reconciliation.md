# Source-Document Reconciliation Policy

Governs resolver seeding/updating of:

`<source_doc_path>`

This policy supports only source documents already seeded from the current review
final-evaluation schema at:

`review/templates/final-evaluation.md`

Companion files: main orchestrator `<resolve_pack_base_path>/orchestrator.md`, source-document
updater prompt `<resolve_pack_base_path>/prompts/source-document-updater.md`.

The outcome ledger consumed by this policy may be prepared either by the automated orchestrator or
by a human operator managing a manual resolver run. The required schema is the same either way.

## Model

Treat review-time content as historical:

- `## Investigation Status`
- original bullets under `## Final Evaluation By Finding`
- `## Overall Recommendation`

Do not rewrite historical analysis to mirror resolver outcomes. The resolver owns only:

- top-level `## Current Resolution State`
- one resolver-managed status line directly under each finding title
- optional resolver-managed lines/subsections defined below
- reopened-dispute severity relabeling defined below, preserving original severity value

## Required Seed Shape

Before a resolver run, `SOURCE_DOC` must already be seeded:

1. `## Current Resolution State` immediately follows `## Investigation Status`.
2. Each finding has exactly one resolver-managed status line immediately below the finding title,
   with `Verification verdict:` as the next bullet and both appearing before
   `Consolidated finding reference`.
3. False-positive findings use `Confirmation status: pending`; all others use
   `Resolution status: pending`.
4. At least one finding is still pending; otherwise abort as already reconciled or not seeded.
5. On reruns, non-`pending` resolver statuses are finalized historical outcomes for the current
   document version and must not be reprocessed or changed unless explicitly required by a new run
   policy.
6. Legacy/manual non-`pending` markers such as `ignore`, `ignored`, `skip`, `skipped`,
   `resolved`, `non-actionable`, `non_actionable`, or `invalid` under either status label also
   count as finalized history for routing. Do not pick them up again, even though canonical
   write-back uses only the mapped statuses below.

## `## Current Resolution State`

Use exactly this bullet shape:

```md
- Section owner: resolver workflow
- Historical review sections in this document remain unchanged and should be read as review-time state.
- Findings still pending resolution: <number>
- Findings still pending false-positive confirmation: <number>
- Findings with finalized resolver outcome: <number>
- Current resolver summary: <1-2 sentence concise summary of current post-review state>
```

Counts come from the full current document, including previously finalized findings:

- pending resolution = `Resolution status: pending`
- pending false-positive confirmation = `Confirmation status: pending`
- finalized = every non-`pending` `Resolution status:` or `Confirmation status:` line, including
  legacy/manual markers such as `ignore`, `ignored`, `skip`, `skipped`, `resolved`,
  `non-actionable`, `non_actionable`, or `invalid`

## Per-Finding Resolver Annotations

Keep original review bullets in place and order. The first two bullets in every finding must
remain:

1. `Resolution status:` or `Confirmation status:`
2. `Verification verdict:`

Additional resolver-managed top lines, when needed, go immediately after `Verification verdict:` in
this order:

1. `Resolver note:` only for findings reopened after disputed false-positive verification
2. `Selected resolution approach:` only for resolved findings

For reopened disputed false positives, normalize severity bullets in place in the finding body:

- `Severity (original): <original severity>` keeps the original/consolidated severity value
  already in `SOURCE_DOC` or the source report.
- `Severity (re-evaluated): <re-evaluated severity>` uses the dispute report's independent
  severity carried in the outcome ledger.
- Replace prior `Consolidated severity:` / `Re-evaluated severity:` labels for that finding
  instead of adding duplicate severity bullets; do not change the original severity value.

Append resolver-managed subsections only at the end of a finding section, with level `####`, in
this order when present:

1. `#### Verification note`
2. `#### Resolution`
3. `#### Reason`

## Status Mapping

False-positive path final confirmation statuses:

- `false_positive_confirmed` -> `Confirmation status: confirmed`
- `verification_blocked` -> `Confirmation status: verification_blocked`
- `skipped_by_user` -> `Confirmation status: skipped_by_user`
- `non_actionable` -> `Confirmation status: non_actionable`

All other finalized findings, including reopened disputed false positives:

- `resolved` -> `Resolution status: resolved`
- `review_failed` -> `Resolution status: review_failed`
- `blocked` -> `Resolution status: blocked`
- `implementation_failed` -> `Resolution status: implementation_failed`
- `verification_blocked` -> `Resolution status: verification_blocked`
- `skipped_by_user` -> `Resolution status: skipped_by_user`
- `non_actionable` -> `Resolution status: non_actionable`

## Per-Finding Content Rules

- Reopened false positives: replace `Confirmation status: pending` with the final
  `Resolution status:` line, keep `Verification verdict:` as the second bullet, add
  `Resolver note: reopened after disputed false-positive verification` immediately below
  `Verification verdict:`, and expose severities as `Severity (original)` plus
  `Severity (re-evaluated)` without overwriting the original severity value.
- Resolved findings: add `Selected resolution approach:` below `Resolver note:` when present,
  otherwise below `Verification verdict:`.
- Approach labels: `Option <N> (recommended)`, `Option <N> with slight refinement`, `Option <N>`,
  or `Custom`. Use the actual finding option number; do not hardcode option 1/2.
- If `Custom`, the `#### Resolution` subsection must summarize the chosen approach in about 2-3
  sentences instead of naming a missing option.
- Every `resolved` finding gets a `#### Resolution` subsection of about 2-3 sentences, citing the
  reviewer report and, if reopened, the dispute report.
- `review_failed`, `blocked`, `implementation_failed`, `verification_blocked`, `skipped_by_user`,
  and `non_actionable` get a `#### Reason` subsection of about 2-3 sentences, citing resolver
  artifacts when available.
- False-positive-path `verification_blocked`, `skipped_by_user`, and `non_actionable` also get
  `#### Reason`.
- `false_positive_confirmed` gets a concise `#### Verification note` of about 1-2 sentences citing
  the confirmation artifact.

## Outcome Ledger Schema

Before spawning the source-document updater, build a per-finding ledger matching this schema:

```yaml
- findingNumber: <integer>
  title: <string>
  sourceDocHeading: <string>
  preUpdateResolverStatusLine: <string>
  originalVerificationVerdict: confirmed | partially_confirmed | false_positive | inconclusive
  finalWorkflowStatus: false_positive_confirmed | resolved | blocked | implementation_failed | review_failed | verification_blocked | skipped_by_user | non_actionable
  reopenedAfterDisputedFalsePositiveVerification: true | false
  selectedResolutionApproachLabel: Option <N> (recommended) | Option <N> with slight refinement | Option <N> | Custom | null
  originalSeverityBeforeReopenedDispute: blocker | major | minor | none | severity-unknown | null
  reEvaluatedSeverityAfterDispute: blocker | major | minor | severity-unknown | null
  effectiveFollowUpSeverityForCommitLabeling: blocker | major | minor | severity-unknown | null
  outcomeSummary: <2-3 sentence summary suitable for the source document>
  resolverArtifactRefs:
    - <repo-relative path>
  sourceReportRefs:
    - path: <repo-relative path under review/reports/>
      heading: <best available heading or locator>
      lines: <start-end | null>
```

Field rules:

- `sourceDocHeading` and `preUpdateResolverStatusLine` are required pre-update anchors. The updater
  must find exactly one matching finding heading with that exact current resolver status line before
  editing; otherwise it must block instead of guessing or reseeding.
- `selectedResolutionApproachLabel` is required and non-null for every `resolved` finding, using
  the implementation reviewer report's exact `Selected resolution approach label:` field. The
  updater must block if this value is missing or malformed for a resolved finding; use `null` only
  for non-resolved findings.
- `originalSeverityBeforeReopenedDispute` and `reEvaluatedSeverityAfterDispute` are required for
  findings reopened after disputed false-positive verification; otherwise `null`. The original
  value must preserve the source/consolidated severity, while the re-evaluated value must come
  from the dispute report's independent severity.
- `effectiveFollowUpSeverityForCommitLabeling` is meaningful only for findings reopened after
  disputed false-positive verification; otherwise `null`. Prefer `reEvaluatedSeverityAfterDispute`;
  if absent, use the implementation reviewer report's exact
  `Effective follow-up severity for commit labeling:` field or `severity-unknown`.
- `resolverArtifactRefs` and `sourceReportRefs.path` are repo-relative only. Use
  `sourceReportRefs.lines` when a stable line range is known; otherwise use `null`.
- `outcomeSummary` stays concise and diff-friendly.

## Updater Rules

The updater must:

1. update only `SOURCE_DOC`
2. use repo-relative paths for inserted artifact refs
3. apply only the provided outcome ledger plus this policy; do not invent outcomes
4. validate each finding's `sourceDocHeading` and `preUpdateResolverStatusLine` before editing it
5. preserve review-time content and unaffected finding analysis
6. not rewrite `## Investigation Status` or `## Overall Recommendation`
7. keep the diff concise
8. not edit other files, stage, commit, branch, reset, clean, run builds/tests/formatters, or
   spawn subagents

## Mini Examples

Confirmed false positive:

```md
### 1. Example validation edge case is already safely rejected
- Confirmation status: confirmed
- Verification verdict: false_positive
...
#### Verification note
Confirmed as a false positive by `<run_docs_base_path>/20260528-1430/reviews/finding-01-example-validation-false-positive-confirmation.md` after code/spec verification.
```

Reopened disputed false positive later resolved:

```md
### 2. Example retry classification masks a local failure condition
- Resolution status: resolved
- Verification verdict: false_positive
- Resolver note: reopened after disputed false-positive verification
- Selected resolution approach: Option 2
...
- Severity (original): major
- Severity (re-evaluated): minor
...
#### Resolution
Resolved by tightening classification and adding focused verification, citing `<run_docs_base_path>/20260528-1430/reviews/finding-02-example-retry-classification-review.md`. The finding was reopened from `<run_docs_base_path>/20260528-1430/false-positive-disputes/finding-02-example-retry-classification-false-positive-dispute.md`.
```
