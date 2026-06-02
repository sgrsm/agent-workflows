# Scout Delegation Policy

Governs child-subagent delegation in resolver workflows.

## Scope

The main orchestrator runs one top-level subagent at a time. That active parent may optionally
use up to 3 read-only child scouts. If scout support is unavailable, continue without delegation.

## Rules

1. Child type: `scout` only; no planner/worker/reviewer/custom implementation children.
2. Delegation is optional; max 3 scouts per top-level task; no grandchildren.
3. Scouts are read-only: inspect/search code, tests, specs, docs, and summarize evidence. They must
   not edit/create files, format, commit, clean, or run destructive commands.
4. Parent prompts must include the same `Project context files` / `PROJECT_CONTEXT_FILES` list given
   to the top-level stage. Unless the list is `- none`, scouts must read every listed file before
   issue-specific inspection and apply those instructions as binding repository rules.
5. Context must be issue-scoped and minimal for the current finding.
6. Implementation-reviewer scouts stay plan-blind; do not disclose, request, or infer plan content;
   do not search `PLANS_DIR` or planner/implementer handoffs. They must not read unprovided review
   reports under `REVIEWS_DIR`; in post-remediation review, the parent reviewer handles the
   explicit same-finding prior review report and delegates only narrow code/test/spec questions.
7. Implementer scouts may do only read-only plan/code/test discovery; implementer owns edits,
   commands, and final reporting.
8. Local repo evidence is primary. Web/web_search is allowed for external APIs/tooling/background;
   report material URLs/provenance.
9. Scouts may run concurrently inside the active parent if supported; main orchestrator still waits
   for the parent before starting another top-level task.
10. Parent reconciles/de-duplicates scout output, never dumps raw scout output, and preserves its
    output contract.
11. Scout output: concise facts, paths/lines where practical, material URLs/provenance,
    uncertainty, and no unrelated recommendations.

## Scout Task Archetypes

- Code/test locator
- Spec/evidence verifier
- Verification command finder
- Nearby-pattern scout
- Issue-scoped risk/edge-case scout

## Standard Permission Snippet

```text
Delegation:
- You may spawn up to 3 child `scout` subagents if materially useful; otherwise continue without delegation.
- Pass the full `Project context files` list to every scout and, unless the list is `- none`, require scouts to read those files before issue-specific inspection.
- Scouts must be read-only, issue-scoped, non-delegating, and governed by `<resolve_pack_base_path>/policies/scout-delegation.md`.
- Reconcile scout output yourself and preserve your output contract.
```

For implementation reviewers add:

```text
- Scouts must remain plan-blind; do not disclose, request, or infer implementation plan content.
- Scouts must not search `PLANS_DIR` or planner/implementer handoffs; if plan content appears in
  command output, stop and report contamination to the parent.
- Scouts must not read unprovided review reports under `REVIEWS_DIR`. If unrelated review-report
  content appears accidentally, ignore it, do not use it as evidence, and report that exposure to
  the parent without treating it as plan contamination.
```

For implementers add:

```text
- Scouts may only perform read-only plan/code/test discovery; they must not edit, format, commit, clean, implement, or run destructive commands.
```
