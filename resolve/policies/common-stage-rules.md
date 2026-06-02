# Common Stage Rules

Shared runtime rules for resolver stages that inspect, verify, or change repository state.

## Scope

Applies to false-positive reviewer, planner, implementer, remediation implementer, and
implementation-reviewer stage prompts. Supports orchestrated runs and direct/manual stage use;
subagents are optional.

Companion files: `<resolve_pack_base_path>/workflow.md`,
`<resolve_pack_base_path>/policies/scout-delegation.md`, and
`<resolve_pack_base_path>/policies/continuation.md`.

## Project Context Files

Before issue-specific work, read all paths in the stage prompt's `Project context files` block
unless it is exactly `- none`. Treat listed files as binding repository instructions; more-specific
files add to or override parent instructions for files under their scope.

This explicit read is required because repo-root cwd may not auto-load descendant `AGENTS.md`.
Missing/unreadable files, or conflicts with resolver policy, stage restrictions, or binding user
preference, require blocking/clarification.

## Core Rules

1. Local repository evidence is primary for repository behavior. Prefer code, tests, specs,
   review documents, the seeded source document, and stage-permitted resolver artifacts before
   external sources. Implementation reviewers must not treat implementation plans or
   planner/implementer handoffs as permitted evidence.
2. `web_search` or other web sources are allowed only for external APIs, tooling behavior, or
   general background context that is not available locally.
3. When a web source is materially relevant, cite the URL/provenance and explicitly call out any
   conflict with local evidence instead of silently overriding repository evidence.
4. Keep the active context scoped to the current finding. Do not broaden to unrelated findings,
   full reports, or adjacent features unless the active policy explicitly allows it.
5. Treat source-report paths as locators, not blanket permission to load whole reports. Prefer cited
   line ranges or the smallest matching section, and justify any wider read in the stage artifact.
6. Protect unrelated user work and keep the smallest safe change surface that still resolves the
   finding.
7. Balance minimal diff, reviewability, maintainability, robustness, expressiveness, testability,
   and business impact.
8. If scout/subagent support is unavailable, continue without delegation rather than treating that
   as a blocker.
9. Follow the dedicated policy files for scout delegation, continuation handoffs,
   false-positive/dispute handling, and source-document reconciliation rather than recreating those
   rules informally.
10. Apply the listed project context files when selecting approaches, editing code/tests/config,
    choosing verification commands, and reviewing compliance; implementation reviewers should return
    `needs_fix` for unexcused violations of binding context-file instructions, while documenting any
    accepted deviation whose local contract is explicit and safe.
11. Stage agents must not stage, commit, branch, reset, clean, or otherwise perform Git lifecycle
    operations. Leave repository changes uncommitted for the coordinating layer unless a stage prompt
    explicitly permits otherwise.

## Quality Regression Guardrails

For planning, implementation, remediation, and review:

- Resolve the targeted issue without introducing an equivalent or worse smell elsewhere.
- Keep extracted collaborators cohesive by reason to change; do not replace a broad class with a broad helper/mapper.
- When extracting, copying, or moving existing code, treat moved/copied code as edited code: do not carry binding project-context violations into new or changed surfaces when a small behavior-safe correction is available. If exact legacy preservation conflicts with binding guidance, stop for clarification instead of silently keeping the violation.
- Keep unit tests at the intended boundary: collaborator behavior matrices belong in collaborator tests; parent/client tests cover orchestration, arguments, delegation, branching, resource cleanup, and boundary outcomes.
- Do not unit-test private methods via reflection; test public behavior or extract private logic into a cohesive collaborator.
- Map known boundary failures; do not hide unexpected programmer/runtime bugs behind domain/transport exceptions unless the local contract requires it.
- Keep sentinel/null states explicit; do not silently coerce `null` to a sentinel unless the local contract documents it.

## Binding User Preferences

When the issue packet has non-null `userPreferenceOptionNumber`, that option and any
`userPreferenceAdjustment` are mandatory for planner, implementer, and implementation-reviewer
stages. Do not switch to another option or `Custom`, and do not research alternatives beyond the
minimum needed to validate feasibility/safety and execute the selected path. The implementer may
refine implementation details but must preserve the selected option's intent and any binding
adjustment. A user preference controls only the resolution option and safe implementation
adjustment; it cannot override workflow/stage policies, tool restrictions, repository-scope limits,
clean-worktree requirements, or safety rules. If the bound option or adjustment is impossible,
unsafe, stale, or policy-conflicting, stop for user clarification instead of choosing another path.

## Repository Verification Rules

For stages that run formatting, verification, tests, or builds:

- Follow listed Project context files first, then nearest repo build/config evidence.
- Prefer the smallest relevant formatter/linter/test/build command that verifies the finding.
- Broaden verification only when changes span modules, framework context wiring, generated artifacts, or local instructions require it.
- If no local rule exists, infer from repo evidence and record the chosen command/rationale in the stage artifact.
- If verification cannot run, record the intended command, blocker, and residual risk.

## Stage-Type Notes

- false-positive reviewer and planner stages stay read-only with respect to production code, tests,
  and configuration
- implementer stages may edit only what is needed for the current finding; use the plan as
  guidance, not a scope boundary, and allow small repo-evidenced implementation/test/verification
  adjustments that preserve binding preferences, policies, and acceptance needs
- remediation implementer stages may edit only the implementation review's verdict-blocking,
  current-finding-scoped follow-up; do not read or recreate implementation plans, switch resolution
  options, or broaden scope
- implementation reviewers must stay independent and plan-blind
- the source-document updater has its own restricted prompt and normally does not need this file

## Direct/Manual Compatibility

For direct/manual runs, use `workflow.md` for aliases, artifact dirs, runtime inputs, output
contracts, and sequencing. Fill required runtime placeholders before running stage prompts.
