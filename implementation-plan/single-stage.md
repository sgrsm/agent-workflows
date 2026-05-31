# Stage <N> Implementation Plan: <Stage Name>

> Template instructions
> - Copy this file once per implementation stage referenced from the outline.
> - Replace every `<placeholder>`.
> - Delete bullets or subsections that do not apply.
> - Keep the stage narrow. If one stage needs multiple unrelated responsibilities, split it.
> - Make every instruction concrete enough that a coding agent can implement or verify it.
> - Naming and placeholder guidance: see [naming-and-placeholders.md](naming-and-placeholders.md).

## Working Agreement

This stage plan inherits the `Implementation Working Agreement` from the corresponding feature
outline document.

It also inherits the outline's `Requirement Coverage`, `Open Questions And Assumptions`, and
`Repository Workflow Constraints`. Use those sections to confirm this stage's source requirements,
resolved assumptions, module scope, toolchain, mandatory flags, and repository-specific rules.

Do not duplicate those shared rules here unless this stage needs stricter or more specific
constraints. Any additional rules in this document should be additive and tightly scoped to this
stage.

## Objective

State the exact result of this stage in a few short paragraphs or bullets.

This stage should:

- `<primary responsibility>`
- `<secondary responsibility, if still part of the same narrow scope>`

This stage must not:

- `<next-stage behavior>`
- `<out-of-scope behavior>`

## Prerequisites

List earlier stages, existing contracts, or environment assumptions that should already exist.

If a prerequisite is missing, either stop and clarify or include only the minimum missing work
needed to unblock this stage. Do not silently broaden scope.

## Stage-Specific Questions And Assumptions

List only questions or assumptions that are unique to this stage. If there are none, write `None`.

| Question or assumption | Impact | Status / owner |
|---|---|---|
| `<stage-specific decision needed before implementation>` | `<affected behavior, contract, file, or test>` | `<blocking / non-blocking / clarified in source>` |

Do not encode a blocking assumption as an implementation step or acceptance criterion. Ask for
clarification before coding the affected behavior. Non-blocking assumptions must be clearly labeled
and must not widen this stage beyond its objective.

## Source Of Truth

Use `<authoritative spec / ticket / ADR / contract>` as the source of truth for exact behavior,
data shape, configuration names, error handling, and acceptance criteria.

Use this stage plan for repository-local workflow, suggested structure, and verification
expectations.

Use the outline's `Requirement Coverage` table to confirm which source requirements this stage owns.
Do not implement requirements assigned to other stages unless the outline is updated first.

If this stage plan appears to disagree with the authoritative source, prefer the authoritative
source for behavior and configuration, then ask for clarification before implementing.

## Files To Change

Expected changes are limited to:

- `<main source path or specific file>`
- `<test path or specific file>`
- `<resource / configuration / fixture path, if applicable>`

If exact file names are known, list them. If not, list the smallest reasonable directories and
naming conventions.

Every listed path should either already exist or be intentionally new. Keep paths synchronized with
the implementation plan outline and acceptance checklist.

## Files Not To Change

Do not change:

- `<implementation-plan-outline.md>`
- `<other stage plan docs>`
- `<other modules or services>`
- `<generated code / migrations / public APIs / deployment files, if out of scope>`
- `<any file family owned by another stage>`

## Implementation Steps

1. `<Describe the first concrete change, including exact class, property, API, schema, or file names when known.>`
   - Required input or contract: `<source section, field, property, interface, schema, or rule>`
   - Expected result: `<what must work after this step>`
   - Boundary: `<what must still remain out of scope>`

2. `<Describe the second concrete change.>`
   - Required input or contract: `<source section, field, property, interface, schema, or rule>`
   - Expected result: `<what must work after this step>`
   - Boundary: `<what must still remain out of scope>`

3. `<Describe the third concrete change.>`
   - Required input or contract: `<source section, field, property, interface, schema, or rule>`
   - Expected result: `<what must work after this step>`
   - Boundary: `<what must still remain out of scope>`

4. Add more steps only if they still belong to the same narrowly scoped stage.

If useful, include exact:

- property names or environment variables;
- classes, methods, endpoints, or database objects;
- supported values and unknown-value behavior;
- logging or observability constraints;
- startup, failure, retry, and cleanup behavior.

## Test Or Verification Plan

Rename this section to `Unit Test Plan`, `Integration Test Plan`, or `Verification Plan` if that
makes the stage clearer.

Cover the smallest set of checks that proves this stage:

- happy-path behavior;
- important edge cases and unsupported inputs;
- failure isolation or retry behavior, if the stage owns it;
- configuration binding or contract correctness;
- explicit non-behavior: what must not happen.

Guidance:

- Prefer focused tests over broad context tests when they provide the same confidence.
- If the stage is config-only or docs-only, replace tests with validation or review checks.
- If the stage depends on external services or containers, describe readiness, isolation, and
  cleanup.
- If a test cannot be run in the current environment, keep the plan intact and document the blocker
  rather than weakening the stage.

## Explicit Exclusions

Do not add:

- `<next-stage behavior>`
- `<business logic, persistence, publishing, or API changes if out of scope>`
- `<unapproved properties, environment variables, or external contracts>`
- `<unrelated refactors>`
- `<cleanup work that changes behavior outside this stage>`

List prohibited contracts explicitly when they matter. This section is most useful when it prevents
common scope creep.

## Acceptance Checklist

- `<observable condition that proves the main stage goal>`
- `<observable condition that proves the key contract or configuration>`
- `<observable condition that proves the main boundary or exclusion>`
- `<focused tests, validations, or review checks exist for the stage>`
- `<no out-of-scope files or modules changed>`

Every item should be objectively verifiable in code, configuration, or command output.

## Verification

Run the smallest useful verification scope from:

```text
<working directory>
```

Use the project-required toolchain or runtime:

```text
<toolchain selection command, if any>
```

Run required formatting or validation steps:

```text
<format / lint / validation command>
```

Recommended focused verification commands:

```text
<focused test or build command>
```

Optional broader verification commands:

```text
<broader module or integration command, only if helpful>
```

Include any mandatory repository flags or environment prerequisites in the commands above.

If execution is blocked by missing dependencies, such as Docker, credentials, external services, or
platform tooling, do not weaken the plan. Record the blocker and the exact command that should be
run in a suitable environment.

## Reporting Expectations

When this stage is implemented, report:

- changed files;
- exact commands run;
- pass / fail / skipped status for each command;
- any assumptions or clarifications needed;
- residual risk carried into the next stage.
