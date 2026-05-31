# Continuation Policy

Use when a top-level resolver stage needs user clarification and suspended child tasks cannot be
resumed.

## Model

Use durable handoff, not suspended state: blocked stage writes handoff -> coordinator asks user ->
respawn same role/stage -> new task reads handoff and continues.

## Rules

- Applies to top-level `planner`, `worker`, and `reviewer` stages, including false-positive and
  implementation reviewers.
- Respawn/rerun only the same role/stage for the same finding unless the user explicitly changes workflow.
- Child scouts never ask users or write handoffs; they report ambiguity to their parent.
- Implementation-reviewer handoffs stay plan-blind. Implementer handoffs may reference opaque plan
  path and verification state, but not restate large plan contents.

## Handoff Artifacts

Directory: run-specific `HANDOFF_DIR`, normally `<run_docs_base_path>/<RUN_ID>/handoff/`.

Use one concise Markdown handoff per clarification blocker; update in place when safe. Suggested
names:

- `finding-<NN>-<short-slug>-planner-clarification-handoff.md`
- `finding-<NN>-<short-slug>-implementer-clarification-handoff.md`
- `finding-<NN>-<short-slug>-reviewer-clarification-handoff.md`
- `finding-<NN>-<short-slug>-false-positive-reviewer-clarification-handoff.md`

Handoffs must be factual and issue-scoped. Include: finding number/title, role/stage, objective,
work/evidence so far, created/changed artifacts, commands/key results if any, exact blocker/user
question, useful options, continuation instructions, residual uncertainty/re-checks, plus
role-specific detail as needed:

- planner: approaches considered and what depends on the answer
- implementer: plan path, changed files, verification progress, rollback-sensitive state
- implementation reviewer: review evidence and open gap, plan-blind
- false-positive reviewer: confirmation/dispute evidence and what depends on the answer

## Blocked Output

When clarification is required: write the handoff, then reply:

```text
blocked: user clarification required - <short question> | <absolute path to continuation handoff file>
```

Final stage artifacts may wait until after clarification unless independently useful.

## Coordinator Procedure

When clarification is returned:

1. pause the current finding; do not advance or record final ledger status
2. preserve current-finding worktree state and issue-scoped artifacts unless user says abort/restart
3. ask the user via structured clarification when available
4. after the answer, rerun only the same role/stage with original inputs, answer, handoff path, and
   safe preserved artifact paths needed for continuity
5. instruct the new task to read the handoff first and continue; do not count this as retry/second
   pass

Applies equally to orchestrated and human/manual stage reruns.

## Respawned Stage Expectations

Read handoff first, apply user answer, reuse safe completed work, verify referenced artifacts,
re-check only affected areas, and avoid broad rediscovery unless required.

## Cleanup

Handoffs are resolver docs. Include `RUN_DOCS_DIR/handoff/` in artifact safety checks;
preserve/clean like other run Markdown artifacts; remove stale unrelated-run handoffs from the
active run directory.

## Standard Snippet

```text
Continuation:
- If a handoff path is provided, read it first and continue from the blocker.
- If user clarification is required, follow `<resolve_pack_base_path>/policies/continuation.md`, write a handoff under `HANDOFF_DIR`, and reply:
  `blocked: user clarification required - <short question> | <absolute path to continuation handoff file>`
```
