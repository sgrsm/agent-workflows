# Review Template Pack

Human/operator guide for the reusable review template pack. `workflow.md` is the canonical
execution map.

All paths in this README are repo-root-relative unless noted otherwise. If you're unsure where to
start, start with `review/instantiate.md`.

## Summary

This directory is a copy-and-adapt template pack for building a feature-specific review pack for
`<feature_name>`.

Recommended order:

1. Preferred: from the repository root, run `review/instantiate.md`. Let the agent collect core
   pack values, inspect the feature spec or branch diff, suggest reviewer areas and
   focus-checklist categories, accept your edits/additions, create concrete reviewer prompts,
   replace placeholders, and audit the result.
2. Manual alternative: copy the runtime files to the real review-pack location.
3. Fill `workflow.md` first.
4. Copy/rename reviewer prompt templates for each feature-specific review area.
5. Wire workflow reviewer rows to those concrete reviewer prompt files.
6. Resolve the remaining placeholders.
7. Run from `orchestrator.md` or whichever direct/manual prompt you need.

Do not generate review artifacts inside this template directory. The instantiated review pack should
write its outputs under `<reports_base_path>`.

## Reviewer templates

- `prompts/reviewers/area-reviewer.md` is the source template for most non-test reviewer rows.
  Copy and rename it into one feature-specific reviewer prompt per review area, then replace that
  copy's area contract, spec sections, primary files, focus checklist, out-of-scope block, and
  output path.
- `prompts/reviewers/tests-reviewer.md` is the source template for test-review rows. Copy and
  rename it for each test/proof-strength reviewer row because test review needs the extra
  ROI/practicality gate and optional pre-findings sections. In the interactive instantiation flow,
  a concrete tests review prompt should be created by default as
  `prompts/reviewers/tests-review.md` and removed only if the user explicitly rejects it.
- Do not point multiple reviewer rows at the unresolved source template files unless you first add
  an explicit row-specific payload mechanism to the workflow task assembly.

The template-only instantiation prompt `review/instantiate.md` can inspect the feature spec when
available, or fall back to the branch diff when it is not, then propose a reviewer plan plus
focus-checklist category suggestions for you to accept, edit, remove, or extend before it creates
the concrete reviewer prompts.

Typical area-reviewer copies or row specializations include:

- business flow / validation / handler orchestration
- external integration / resilience / error classification
- persistence / data integrity / schema compatibility
- runtime / operability / logging / startup validation
- documentation follow-up / cross-document consistency
- API or contract compatibility
- rollout / migration / backfill safety
- configuration / packaging / deployment wiring

## Instantiation checklist

Preferred: from the repository root, run `review/instantiate.md` and provide the requested core
pack/path values. The instantiator inspects the feature spec when available, or the branch diff
otherwise, proposes reviewer areas, suggests focus-checklist categories from the template catalog,
lets you accept or adjust them, and materializes the concrete reviewer prompts. The
tests/proof-strength reviewer is included by default unless you explicitly reject it.

### Minimal prompt examples

Spec-backed initiation:

```text
Run `review/instantiate.md`.
Create the review pack at:
`docs/features/<feature_slug>/review`
Use this spec:
`docs/features/<feature_slug>/specification/00-overview.md`
Ask me for any missing values.
```

Diff-only initiation when no feature spec is available:

```text
Run `review/instantiate.md`.
Create the review pack at:
`docs/features/<feature_slug>/review`
No authoritative spec is available.
Ask me for any missing values.
```

Manual alternative:

1. Copy this directory to the future review-pack location and set `<review_pack_base_path>`.
2. Fill `workflow.md` before editing other files; it is the canonical source for scope, paths,
   accepted context docs, reviewer rows, outputs, clean-output gates, and task assembly.
3. Copy and rename `prompts/reviewers/area-reviewer.md` into one concrete prompt file per
   non-test reviewer row. Copy and rename `prompts/reviewers/tests-reviewer.md` for each test
   coverage/proof-strength row.
4. Use `focus-checklist-catalog.md` to seed non-test reviewer focus-checklist categories or
   concrete checklist bullets.
5. Replace each copied reviewer prompt's per-area placeholders and point the corresponding
   `workflow.md` row at that concrete prompt file.
6. Set `<authoritative_spec_entry_point>` / `<specification_base_path>` when a feature spec exists,
   or use literal `none` for both in a diff-only review pack.
7. Set the real report/output paths used by the instantiated pack.
8. Confirm the orchestrator, consolidator, verifier, and all copied reviewer prompts still read
   naturally after placeholder replacement.

## Post-instantiation validation checklist

Before running the copied pack, verify:

- no unresolved instantiation placeholders remain in active workflow files; only runtime report
  placeholders may remain inside report/verification skeletons
- every reviewer row points to a concrete copied reviewer prompt with area-specific values filled in
- a tests/proof-strength reviewer prompt exists unless it was explicitly rejected during
  instantiation; if it was auto-generated without an explicit filename override, it should be
  `prompts/reviewers/tests-review.md`
- reviewer expected report paths are unique
- `<documentation_reviewer_report_filename>` is `none` or matches one reviewer report filename
- feature-spec fields are either valid repo-root-relative paths or both literal `none` for a
  diff-only review pack
- `<reports_base_path>` exists or can be created
- clean-output-gate files match all reviewer, consolidated, and final-evaluation output paths
- consolidated and final-evaluation output paths match `workflow.md`
- policy, prompt, and template refs point to the instantiated pack, not the source template pack
- non-test reviewer focus checklists are concrete and feature-specific rather than raw catalog dumps
- source reviewer templates are either removed/unreferenced or intentionally kept as helpers only
- all referenced source/spec/context paths exist, except files explicitly created by the review run

## Modes

Choose a mode after instantiation:

| Mode | Use when | Start here | What happens |
|---|---|---|---|
| **Orchestrated multi-agent** | Your harness can launch subagents and you want the full end-to-end review. | `<review_pack_base_path>/orchestrator.md` | Runs one isolated area reviewer per reviewer row in `workflow.md`, consolidates their reports, verifies each consolidated finding, and writes the final evaluation artifact defined by `workflow.md`. |
| **Direct/manual single-agent** | Subagents are unavailable, or you want to run one review step manually. | A concrete copied reviewer prompt under `<review_pack_base_path>/prompts/reviewers/`, `<review_pack_base_path>/prompts/consolidator.md`, or `<review_pack_base_path>/prompts/verifier.md` | Runs one area review, consolidation, or per-finding verification at a time. Consolidation requires existing reviewer reports; verification requires one full consolidated finding block pasted into the same request. |

## Orchestrated multi-agent mode

Use this mode for the full end-to-end review when the harness can launch subagents.

### Requirements

- Harness that can launch subagents.
- Shared roles/profiles required: `reviewer` and `worker`.
- Reports base path from `workflow.md` must exist or be creatable.
- Accepted context doc paths listed in `workflow.md`, if any, must exist.
- Clean-output-gate files listed in `workflow.md` must not already exist before the run starts.
- Reviewer rows must point to concrete copied reviewer prompts with per-area placeholders resolved.
- Optional child delegation uses only shared, read-only `scout` helpers as defined in
  `policies/scout-delegation.md`.
- Top-level reviewer, consolidation, and finding-verification tasks run from the repository
  root.

### Flow

1. Start with no pre-existing clean-output-gate files listed in `workflow.md`.
2. Run the area reviewers listed in `workflow.md`.
3. Run consolidation to produce the consolidated report path defined in `workflow.md`.
4. Run per-finding verification.
5. Write the final evaluation report path defined in `workflow.md`.

### Minimal prompt

```text
Run the full multi-agent review workflow from:
<review_pack_base_path>/orchestrator.md

Follow the prompt pack exactly. Use workflow.md as canonical. Stop before launching agents if
reports-directory, accepted-context, clean-output, reviewer-prompt instantiation, or role/profile
preflight fails.
```

## Direct/manual single-agent mode

Use this mode when the harness cannot launch subagents, or when you want to run one review step
manually with a repo-aware agent.

### Requirements

- Repo-aware agent that can read referenced files.
- For area review and consolidation, the agent should be able to write the expected markdown output
  path from the prompt or `workflow.md`.
- For per-finding verification, the agent should return one verification block to the session and
  not write files.
- `workflow.md` remains canonical for exact paths, expected outputs, and default scope/baseline
  values.
- The consolidator prompt synthesizes reviewer reports only; it must not invent new primary
  findings.

### Supported single-agent runs

| Task | Entry point | Needs existing reports? | Extra input needed |
|---|---|---:|---|
| Area review | A concrete copied reviewer prompt under `<review_pack_base_path>/prompts/reviewers/` | No | No |
| Consolidation | `<review_pack_base_path>/prompts/consolidator.md` | Yes — existing reviewer reports; missing expected reports are recorded | No |
| Per-finding verification | `<review_pack_base_path>/prompts/verifier.md` | Yes — consolidated report defined by `workflow.md` | Yes — paste one full finding block into the same request |

### Standalone area review

Run the concrete copied reviewer prompt for the area you want to review. The source templates
`area-reviewer.md` and `tests-reviewer.md` are not intended to be run directly after copying unless
all per-area placeholders in that file have been resolved for exactly one reviewer row.

Typical area names include business flow, persistence, integration/resilience, runtime/
operability, documentation follow-up, rollout safety, or configuration/deployment review. The
reviewer prompt tells the agent which shared components to read and where to write the report.

### Standalone consolidation

Use `<review_pack_base_path>/prompts/consolidator.md` after the reviewer reports referenced by
`workflow.md` exist or have been confirmed missing.

### Standalone per-finding verification

Use `<review_pack_base_path>/prompts/verifier.md` only after the consolidated report defined by
`workflow.md` exists.

1. Open the consolidated report path from `workflow.md` and choose one finding under
   `## Consolidated Findings`.
2. Copy the **full finding block** for that finding, including `Source reports` and all reference
   blocks.
3. Start the verifier run and paste that full finding block in the **same request** that invokes
   the verifier prompt.
4. Let the agent use `workflow.md` for review scope and diff baseline unless you explicitly want to
   override them.

## Canonical files

`README.md` is the human-oriented usage guide. `workflow.md` remains the canonical execution map
for agents and exact path/task-assembly maintenance.

- `review/instantiate.md` — template-only
  interactive prompt for creating a feature-specific review pack; not a review runtime stage
- `review/naming-and-placeholders.md` —
  template-only naming, placeholder, and audit guide; optional to copy for local operator use
- `review/focus-checklist-catalog.md` —
  template-only catalog of sanitized focus-checklist categories for non-test reviewer prompts
- `workflow.md` — canonical role, path, output, clean-output-gate, and task-assembly map
- `orchestrator.md` — multi-agent entry point
- `prompts/reviewers/area-reviewer.md` — source template to copy into concrete non-test reviewer
  prompts
- `prompts/reviewers/tests-reviewer.md` — source template to copy into concrete test-review prompts
- `prompts/consolidator.md` — direct consolidation entry point after reviewer reports exist
- `prompts/verifier.md` — direct per-finding verification entry point after a consolidated finding
  exists
- `fragments/`, `policies/`, `templates/` — shared task components
- generated reports under `<reports_base_path>` — artifacts for the copied review pack, not for
  this template directory
