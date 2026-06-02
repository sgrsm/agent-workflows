# Manual Resolver Operation Guide

Use this guide when a human operator drives the resolver workflow without relying on the automated
orchestrator in `<resolve_pack_base_path>/orchestrator.md`.

`workflow.md` remains canonical for path aliases, run-artifact layout, issue-packet schema, stage
I/O, output contracts, outcome taxonomy, and safety invariants. This file is only the manual
runbook.

## Human-Orchestrated Full Run

1. Read `<resolve_pack_base_path>/workflow.md` first.
2. If `PROJECT_CONTEXT_FILES` is not `- none`, read and verify every listed file. Keep the exact
   list available for every stage prompt and child scout.
3. Read the relevant policy and prompt files before each stage you run.
4. Perform the same preflight as the automated orchestrator:
   - verify a clean worktree, including untracked files
   - verify `SOURCE_DOC` exists and follows the seeded resolver-ready final-evaluation shape
   - confirm at least one finding is still pending via exact `Resolution status: pending` or
     `Confirmation status: pending`; do not count legacy/manual non-pending markers such as
     `ignore`, `ignored`, `skip`, `skipped`, `resolved`, `non-actionable`,
     `non_actionable`, or `invalid`
   - choose a unique `RUN_ID`, branch name, and `RUN_DOCS_DIR`
   - create `RUN_DOCS_DIR/{plans,reviews,false-positive-disputes,handoff,final}/`
5. Enumerate pending findings from `SOURCE_DOC` in source-document order. Do not pick findings
   already marked with any non-`pending` `Resolution status:` or `Confirmation status:` value,
   including the legacy/manual markers above.
6. Build one canonical issue packet for the current finding only, using the schema in
   `workflow.md`, and carry forward raw `User preference:` from `SOURCE_DOC` when present plus the
   normalized `userPreferenceOptionNumber` and any `userPreferenceAdjustment`. If the line is
   omitted, treat it as no user override.
7. If user preference binds an option in any equivalent form (`Option <N>`, `option <N>`, `<N>`,
   or an English number word such as `one`), apply `COMMON_STAGE_POLICY` binding-user-preference
   rules: follow that option plus any safe adjustment, do not override it with another option or
   `Custom`, and stop for clarification if it conflicts with workflow/stage policy or safety.
8. Pass the Project context files list to every eligible stage. For a false-positive candidate
   matching `FP_POLICY`, run the false-positive reviewer prompt. Otherwise run planner ->
   implementer; after the implementer returns `done`, clear `RUN_DOCS_DIR/plans/` without reading
   plan contents, then run the independent implementation reviewer. Verify the deletion target is
   the current run's `plans/` directory before clearing. If the reviewer returns `needs_fix` with
   `Remediation retry eligible: yes`, run one remediation implementer pass using only the review
   report and original issue packet, then run independent implementation review again. After that
   second review, follow the normal reviewer outcome mapping; no further remediation attempt is
   allowed. Otherwise treat `needs_fix` as `review_failed`.
9. If any stage returns a user-clarification blocker, follow `CONTINUATION_POLICY`: ask the user,
   preserve safe artifacts, then rerun only the same role/stage with the original inputs, user
   answer, and handoff path.
10. Maintain the per-finding outcome ledger throughout the run. Do not update `SOURCE_DOC` until the
    primary pass and disputed-false-positive follow-up pass are complete.
11. After the primary pass, process queued disputed false positives in original order through the
    normal actionable planner -> implementer -> reviewer path, including the optional single
    bounded remediation pass when eligible.
12. Run the source-document updater with the final ledger, write `FINAL_REPORT`, and commit the
    final documentation changes if you are mirroring the automated workflow.

## Direct Single-Stage Run

Use a stage prompt directly only when rerunning or debugging that stage in isolation. Before
running it, fill the runtime placeholders listed by `workflow.md` for that stage. Before any
standalone implementation-reviewer run, clear the contents of the current run's `PLANS_DIR` without
reading plan contents.

Typical inputs:

All false-positive reviewer, planner, implementer, remediation implementer, and
implementation-reviewer runs also require `PROJECT_CONTEXT_FILES` from `workflow.md`.

- false-positive reviewer: current-finding issue packet, issue-scoped support, `REVIEWS_DIR`,
  `DISPUTES_DIR`, `HANDOFF_DIR`, optional continuation handoff and user answer
- planner: current-finding issue packet, issue-scoped support, `PLANS_DIR`, `HANDOFF_DIR`, optional
  continuation handoff and user answer
- implementer: absolute plan path, `HANDOFF_DIR`, optional continuation handoff and user answer
- remediation implementer: original issue packet, `finding-start-sha`, implementation review path,
  `HANDOFF_DIR`, optional continuation handoff and user answer
- implementation reviewer: original issue packet, `finding-start-sha`, cleared `PLANS_DIR`,
  `REVIEWS_DIR`, `HANDOFF_DIR`, optional continuation handoff and user answer
- source-document updater: final outcome ledger matching `SOURCE_DOC_POLICY`

## How To Invoke A Stage Prompt

Each file under `<resolve_pack_base_path>/prompts/` has operator metadata followed by a fenced
`text` prompt. For manual single-agent use, send the fenced prompt body after replacing runtime
placeholders. If your harness supports roles, select the `Use agent type ...` role; otherwise paste
the filled prompt body into the current agent/session. Do not leave `<insert ...>` placeholders
unfilled.

Minimal file-reading launcher:

```text
Run one manual resolver stage from repository root.

Use prompt file: <resolve_pack_base_path>/prompts/implementer.md
Act as: worker

Runtime values:
- COMMON_STAGE_POLICY = <resolve_pack_base_path>/policies/common-stage-rules.md
- DELEGATION_POLICY = <resolve_pack_base_path>/policies/scout-delegation.md
- CONTINUATION_POLICY = <resolve_pack_base_path>/policies/continuation.md
- Project context files = paste the PROJECT_CONTEXT_FILES list from workflow.md
- HANDOFF_DIR = <run_docs_base_path>/<RUN_ID>/handoff/
- Plan path = <absolute path returned by planner>
- Continuation handoff path = none
- User answer = none

Read the prompt file, fill these values into its fenced `text` prompt body, then execute only that
stage and preserve its output contract.
```

Copy/paste style: paste the full fenced prompt body from the stage prompt file. For an implementer
run, it starts like this; continue with that file's remaining rules and output contracts unchanged.

```text
You are the implementer for one PR review finding.

Aliases from `workflow.md`:
- `COMMON_STAGE_POLICY` = `<resolve_pack_base_path>/policies/common-stage-rules.md`
- `DELEGATION_POLICY` = `<resolve_pack_base_path>/policies/scout-delegation.md`
- `CONTINUATION_POLICY` = `<resolve_pack_base_path>/policies/continuation.md`
- `HANDOFF_DIR` = `<run_docs_base_path>/<RUN_ID>/handoff/`

Project context files (repo-root-relative, ordered broadest to most specific):
paste the PROJECT_CONTEXT_FILES list from workflow.md here

Plan path:
<absolute path returned by planner>

Continuation handoff path, if any:
omit

User answer, if any:
omit

[continue with the remaining Rules, Success output contract, and Failure output contract sections
from `<resolve_pack_base_path>/prompts/implementer.md`, with placeholders resolved]
```

Use the same pattern for planner, false-positive reviewer, remediation implementer,
implementation reviewer, and source-document updater: replace the prompt-specific aliases/inputs
with the values listed above and in `workflow.md`.

## Reviewer Independence In Manual Runs

Manual operation is supported, but independence is strongest when planner, implementer, and
implementation reviewer run in separate sessions or separate agents.

If one agent or person performs multiple roles, treat that as reduced assurance and record the
tradeoff in operator notes or the final summary rather than presenting it as fully independent
review.
