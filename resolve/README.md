# Resolver Template Pack

Human/operator guide for the reusable resolver template pack. `workflow.md` is the canonical
execution map.

All paths in this README are repo-root-relative unless noted otherwise. If you're unsure where to
start, start with `resolve/instantiate.md`.

## Summary

This directory is a copy-and-adapt template pack for building a feature-specific resolver pack for
`<feature_name>` within your target repo/module scope.

Recommended order:

1. Preferred: from the repository root, run `resolve/instantiate.md`. Let the agent collect
   values, copy runtime files, replace placeholders, and audit the result.
2. Manual alternative: copy the runtime pack files to the real resolve-pack location.
3. Fill `workflow.md` first.
4. Resolve the remaining instantiation placeholders in the policy and prompt files.
5. Confirm `<source_doc_path>` already follows the seeded resolver shape from
   `review/templates/final-evaluation.md`.
6. Choose an execution mode:
   - automated: run from `<resolve_pack_base_path>/orchestrator.md`
   - human-driven/manual: use `manual.md`, `workflow.md`, and the stage prompts under
     `<resolve_pack_base_path>/prompts/`

Do not generate resolver artifacts inside this template directory. The instantiated resolve pack
should write run artifacts under `<run_docs_base_path>/<RUN_ID>/`.

## Scope And Schema Boundary

- This pack is intended for reuse across feature folders under your repo's chosen docs/module
  area.
- It is not a monorepo-generic resolver framework.
- It supports only the current seeded final-evaluation/source-document shape defined by
  `review/templates/final-evaluation.md`.
- The copied pack may be reused in another module only if that module adopts the same review output
  schema, seeded resolver fields, and report conventions.

## Entry Points

- `resolve/instantiate.md` — template-only
  interactive prompt for creating a feature-specific resolver pack; not a resolver runtime stage
- `<resolve_pack_base_path>/orchestrator.md` — automation-oriented full resolver workflow
- `<resolve_pack_base_path>/workflow.md` — canonical path, naming, artifact, stage-I/O, and
  issue-packet map
- `<resolve_pack_base_path>/manual.md` — human-operated full-run and single-stage runbook
- `resolve/naming-and-placeholders.md` —
  template-only instantiation/runtime placeholder guide; optional to copy for local operator use
- `<resolve_pack_base_path>/policies/*.md` — supporting resolver policies, including shared common
  stage rules, scout delegation, continuation, false-positive handling, and source-document
  reconciliation
- `<resolve_pack_base_path>/prompts/*.md` — directly runnable stage prompts after the operator or
  orchestrator fills the runtime placeholders

## Instantiation Checklist

Preferred: from the repository root, run `resolve/instantiate.md` and provide the requested
feature/path values.

Minimal prompt:

```text
Create/update a feature-specific resolver pack using:
resolve/instantiate.md

Run from repository root. Follow the prompt exactly: collect any missing feature/path values, copy
only the requested resolver-pack files, replace instantiation placeholders, audit the result, and
do not run the resolver workflow.
```

Manual alternative:

1. Copy the runtime pack files to the future resolve-pack location and set
   `<resolve_pack_base_path>`. Template-only helper files such as `instantiate.md` and
   `naming-and-placeholders.md` do not need to be copied unless you intentionally want local
   operator/reference copies.
2. Fill `workflow.md` before editing the other files; it is canonical for source-doc paths,
   review-report paths, run-id/branch patterns, commit templates, output locations, artifact
   directories, support file references, issue-packet shape, and stage inputs/outputs.
3. Replace the remaining instantiation placeholders in `README.md`, `manual.md`,
   `orchestrator.md`, the policy files, and the prompt files.
4. Confirm `<source_doc_path>` points to the seeded final evaluation produced from the current
   review template pack schema.
5. Confirm `<review_reports_dir>` matches the source-report references expected by the source
   document.
6. Confirm `<run_docs_base_path>` and `<final_report_output_path>` are aligned with the run layout
   in `workflow.md`.
7. Confirm the copied policy and prompt files still read naturally after placeholder replacement.

## Post-Instantiation Validation Checklist

Before running the copied pack, verify:

- no unresolved instantiation placeholders remain in active workflow files; only the runtime and
  example/schema placeholders allowed by the source template's `naming-and-placeholders.md` remain
- `<source_doc_path>` exists and is seeded for resolver work with at least one pending resolver line
- `<review_reports_dir>` exists and matches the report references cited by the source document
- `<run_docs_base_path>` exists or can be created during the run
- `<final_report_output_path>` matches the final-path convention in `workflow.md`
- all policy references under `<resolve_pack_base_path>/policies/` and all stage-prompt references
  under `<resolve_pack_base_path>/prompts/` are correct
- no stale source-feature identifiers or paths remain in active files
- the tool-assisted audit described by the source template's `instantiate.md` or the manual audit
  in the source template's `naming-and-placeholders.md` has no blockers

## Modes

| Mode | Use when | Start here | What happens |
|---|---|---|---|
| **Orchestrated multi-agent** | Normal full resolver run and your harness can launch the required top-level stages. | `<resolve_pack_base_path>/orchestrator.md` | Prepares a resolver branch/run directory, processes pending findings in order, performs same-run disputed-false-positive follow-up, reconciles the source document, and writes the final run summary. |
| **Human-orchestrated stage-by-stage** | You want the full resolver workflow without making subagent support a hard requirement. | `<resolve_pack_base_path>/manual.md`, with `workflow.md` as canonical reference | A human operator drives the same per-finding sequence manually: preflight, issue-packet assembly, false-positive or actionable path, outcome-ledger maintenance, source-document update, and final report writing. |
| **Direct/manual single-stage run** | You need to rerun or debug one stage in isolation. | The exact stage prompt you need under `<resolve_pack_base_path>/prompts/`, with operator-supplied runtime placeholders filled in | Runs only one top-level stage. The operator must supply the issue packet or plan path, run-specific directories, and any continuation handoff path. |

## Orchestrated Multi-Agent Mode

Use this mode for normal end-to-end resolver execution.

### Requirements

- Harness that can launch the required top-level subagents.
- Shared roles/profiles required by the copied workflow, normally `planner`, `worker`, and
  `reviewer`.
- Repository-root execution for the orchestrator and all top-level stages.
- Clean worktree before the run starts.
- Seeded `<source_doc_path>` with at least one `Resolution status: pending` or
  `Verification status: pending` line.
- Run-artifact output under `<run_docs_base_path>/<RUN_ID>/`.

### Minimal prompt

```text
Run the full resolver workflow from:
<resolve_pack_base_path>/orchestrator.md

Follow the prompt pack exactly. Read workflow.md first and treat it as canonical for paths,
patterns, outputs, issue-packet schema, stage I/O, and routing. Abort before branching if the
worktree is dirty or the source document is not seeded for resolver work.
```

## Human-Orchestrated Stage-By-Stage Mode

Use this mode when you want the same resolver semantics without making a multi-agent/subagent
harness a hard requirement.

Start with `<resolve_pack_base_path>/manual.md`. It contains the manual preflight, per-finding
sequence, continuation handling, disputed-false-positive follow-up, finalization steps, and reviewer
independence note. Keep `<resolve_pack_base_path>/workflow.md` open as the canonical reference for
aliases, artifact layout, issue-packet schema, stage I/O, and output contracts.

## Direct/Manual Single-Stage Run

Use the stage prompts directly when you intentionally want to run or rerun one top-level stage.
Open the needed prompt under `<resolve_pack_base_path>/prompts/`, use the fenced `text` prompt body
as the agent prompt, and replace every runtime placeholder with the current values. The Markdown
title and `Use agent type ...` line are operator metadata; select that role when the harness
supports roles, otherwise paste the filled prompt body into the single agent/session. See
`manual.md` for minimal launcher examples.

Typical direct stage entry points:

- false-positive verification — `<resolve_pack_base_path>/prompts/false-positive-reviewer.md`
- planning — `<resolve_pack_base_path>/prompts/planner.md`
- implementation — `<resolve_pack_base_path>/prompts/implementer.md`
- independent implementation review — `<resolve_pack_base_path>/prompts/implementation-reviewer.md`
- final source-document update — `<resolve_pack_base_path>/prompts/source-document-updater.md`

## Runtime Files And Template Helpers

`README.md` is the human-oriented usage guide. `workflow.md` remains the runtime-canonical
execution map for both automated and manual runs.

- `instantiate.md` — template-only interactive prompt for creating a copied resolver pack; not
  required in the instantiated runtime pack
- `naming-and-placeholders.md` — template-only file-naming, placeholder, and audit reference;
  optional to copy for local operator use
- `workflow.md` — canonical path, output, naming, issue-packet, and stage-I/O map
- `manual.md` — manual full-run and single-stage runbook
- `orchestrator.md` — automation-oriented main resolver entry point
- `policies/*.md` — shared resolver rules and specialized policies
- `prompts/*.md` — directly runnable stage prompts for false-positive verification, planning,
  implementation, review, and source-document update
- generated run artifacts under `<run_docs_base_path>/<RUN_ID>/` — outputs for the copied resolve
  pack, not for this template directory
