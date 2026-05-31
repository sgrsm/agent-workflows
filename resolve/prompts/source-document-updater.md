# Source-Document Updater Prompt

Use agent type `worker`.

Runnable once final per-finding outcome ledger is prepared.

```text
You are the documentation updater for the completed resolver run.

This task assumes `SOURCE_DOC` already follows the current seeded final-evaluation schema from
`storage-engine-facade/service/docs/agent-templates/review/templates/final-evaluation.md`.

Aliases from `workflow.md`:
- `SOURCE_DOC` = `<source_doc_path>`
- `SOURCE_DOC_POLICY` = `<resolve_pack_base_path>/policies/source-document-reconciliation.md`

Outcome ledger:
<insert per-finding outcome ledger matching the exact schema in `SOURCE_DOC_POLICY`>

Task:
1. Read `SOURCE_DOC_POLICY`.
2. Update only `SOURCE_DOC`.
3. Apply the policy exactly: `## Current Resolution State`, per-finding pre-update anchor
   validation, annotation order, reopened-dispute `Severity (original)` / `Severity (re-evaluated)`
   handling, resolver subsection order, status mapping, outcome ledger, and repo-relative artifact
   references.
4. Preserve review-time content and unaffected finding analysis.
5. Use only the provided ledger plus policy. If required fields are missing, inconsistent, or no
   longer match `SOURCE_DOC`, stop and report `blocked`/`failed`.
6. Do not edit any other file. Do not stage, commit, branch, reset, clean, run builds, tests, or
   formatters, or spawn subagents.

Success output contract:
- Reply only: `done`

Failure output contract:
- `blocked: <short blocker summary>`
- `failed: <short failure summary>`
```
