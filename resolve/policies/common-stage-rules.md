# Common Stage Rules

Shared runtime rules for resolver stages that inspect, verify, or change repository state.

## Scope

Applies to false-positive reviewer, planner, implementer, and implementation-reviewer stage
prompts. Supports orchestrated runs and direct/manual stage use; subagents are optional.

Companion files: `<resolve_pack_base_path>/workflow.md`,
`<resolve_pack_base_path>/policies/scout-delegation.md`, and
`<resolve_pack_base_path>/policies/continuation.md`.

## Core Rules

1. Local repository evidence is primary for repository behavior. Prefer code, tests, specs,
   review documents, the seeded source document, and resolver artifacts before external sources.
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
10. Stage agents must not stage, commit, branch, reset, clean, or otherwise perform Git lifecycle
   operations. Leave repository changes uncommitted for the coordinating layer unless a stage prompt
   explicitly permits otherwise.

## Repository Build And Toolchain Rules

For stages that run verification commands or edit Java/configuration files:

- use Java 11 via `sdk use java 11.0.30-zulu`
- include `-Dhelm.skip=true` with Maven commands
- prefer the smallest relevant Maven scope
- after Java/configuration edits, run the nearest
  `mvn fmt:format -Dhelm.skip=true` with `-DsourceDirectory=<dir> -DskipTestSourceDirectory=true`
  and/or `-DtestSourceDirectory=<dir> -DskipSourceDirectory=true`
- do not run the formatter for docs-only changes
- prefer targeted verification/test commands over broad repo-wide builds unless the finding truly
  requires a wider scope

## Stage-Type Notes

- false-positive reviewer and planner stages stay read-only with respect to production code, tests,
  and configuration
- implementer stages may edit only what is needed for the approved plan and finding scope
- implementation reviewers must stay independent and plan-blind
- the source-document updater has its own restricted prompt and normally does not need this file

## Direct/Manual Compatibility

For direct/manual runs, use `workflow.md` for aliases, artifact dirs, runtime inputs, output
contracts, and sequencing. Fill required runtime placeholders before running stage prompts.
