# <Feature Name> Implementation Plan Outline

> Template instructions
> - Copy this file into the feature-specific documentation folder.
> - Replace every `<placeholder>`.
> - Delete guidance bullets that are no longer needed once the plan is filled in.
> - Add or remove stages as needed.
> - Keep each stage small enough for one focused implementation and review pass.
> - Naming and placeholder guidance: see [naming-and-placeholders.md](naming-and-placeholders.md).

## Scope And Source Of Truth

This document outlines the staged implementation plan for `<feature name>` in `<module path>`
(`<module alias>`, if useful).

Behavior, configuration, data contracts, and acceptance criteria are based on these sources, in
priority order:

1. `<authoritative spec / ticket / ADR>`
2. `<secondary contract / schema / API doc>`
3. `<overview or context doc>` for intent only

If implementation discovers a conflict between the authoritative source and this plan, prefer the
authoritative source for product behavior, then pause and clarify before coding.

## Requirement Coverage

Before writing stage details, map each concrete requirement from the source of truth to one owning
stage and one verification target. Keep source references specific enough that a coding agent can
trace the plan back to the feature specification without guessing.

| Source reference | Requirement or decision | Owning stage | Verification target | Notes |
|---|---|---|---|---|
| `<spec section / ticket item / contract clause>` | `<observable requirement>` | `<Stage N>` | `<test, command, or review check>` | `<assumption, blocker, or out-of-scope note>` |

If a requirement is intentionally deferred or excluded, state that explicitly in `Notes` and record
where it will be clarified or implemented.

## Open Questions And Assumptions

List unresolved or underspecified decisions found while turning the feature specification into this
plan.

| Question or assumption | Impact | Status / owner |
|---|---|---|
| `<decision needed before implementation>` | `<affected behavior, contract, or stage>` | `<blocking / non-blocking / clarified in source>` |

Do not turn a guess into implementation instructions. If the plan cannot be made exact without an
answer, pause and ask for clarification before finalizing or implementing the affected stage.
Clearly label any non-blocking assumption and keep it out of acceptance criteria unless it has been
confirmed by the source of truth or user input.

## Repository Workflow Constraints

Record repository-local constraints that every stage should inherit:

- applicable `AGENTS.md` files;
- nearest relevant `pom.xml` or build root;
- module or service scope;
- required runtime or toolchain selection;
- required build, test, formatting, or validation flags;
- generated-code, migration, schema, or deployment-file rules;
- known external dependencies such as Docker, credentials, or services.

Keep this section factual and repository-specific. Put stage-only exceptions in the detailed stage
plan instead.

## Implementation Working Agreement

For code-bearing stages, follow a test-driven workflow:

1. Write a focused test that represents a concrete requirement from the source of truth or stage
   plan.
2. Confirm the test fails for the expected reason or that the requirement is currently unmet when
   practical.
3. Implement the smallest production change that makes the test pass.
4. Make only local cleanup needed for clarity while keeping tests green and staying within the
   approved stage scope.

Additional implementation rules:

- Keep changes small and reviewable.
- Prefer existing `<module_name>` patterns over new abstractions.
- Code should minimize cognitive and cyclomatic complexity for the files changed in the current
  stage without forcing cleverness. Prefer simple, readable, functional/declarative style where it
  improves clarity; use imperative code when it is clearer.
- Follow clean-code, SOLID, and refactoring principles: clear names, small methods, single
  responsibility, low duplication, explicit boundaries, and no speculative abstractions. Refactor
  only inside files directly changed for the stage, and only when needed to implement or verify that
  stage.
- Prefer constructor injection and immutable fields. Keep configuration keys and constants
  centralized when they are reused.
- Use Java local-variable type inference with `var` where possible, following existing local module
  style and preserving readability.
- Tests should be deterministic, focused, and named after observable behavior. Avoid broad
  framework or context tests when a narrow unit test gives the same confidence. Avoid over-mocking
  value objects and DTOs; use real objects where cheap. Keep test setup readable with
  Arrange/Act/Assert structure.
- Do not log secrets, credentials, full configuration objects, or unnecessarily large payloads.
- Use existing logging, test, and mocking conventions before introducing new ones.
- Use the smallest relevant build/test scope, the project-required toolchain, and any mandatory
  repository flags when verifying work.
- After code changes, include the required formatting or validation step for the lowest relevant
  changed module or directory.
- Do not perform unrelated refactors while implementing a stage. Treat formatting and focused tests
  as part of the implementation, not an afterthought.

## Stage Design Rules

Each stage should:

- have one primary responsibility;
- own one or more entries from the `Requirement Coverage` table;
- state what is in scope and what remains out of scope;
- list the expected files or directories to change;
- link to a dedicated stage plan document;
- define a concrete review focus;
- define the smallest useful verification target.

Typical stage categories, when relevant:

- static dependency or configuration contract;
- domain model / DTO / mapping;
- runtime orchestration or startup behavior;
- user-facing or integration behavior;
- observability and bounded logging;
- integration or end-to-end test infrastructure;
- final assembly, regression check, and risk review.

Use only the categories that fit the feature. The final stage should usually confirm assembled
behavior and document remaining risk.

## Stage <1>: <Stage Name>

Goal: `<one-sentence goal for this stage>`

Detailed plan:
[`<feature-slug>-implementation-plan-stage-<n>.md`](<feature-slug>-implementation-plan-stage-<n>.md)

Review focus: `<contract, boundary, or behavior most likely to need careful review>`

Verification target: `<smallest command, test class, validation step, or explicit review check>`

## Stage <2>: <Stage Name>

Goal: `<one-sentence goal for this stage>`

Detailed plan:
[`<feature-slug>-implementation-plan-stage-<n>.md`](<feature-slug>-implementation-plan-stage-<n>.md)

Review focus: `<contract, boundary, or behavior most likely to need careful review>`

Verification target: `<smallest command, test class, validation step, or explicit review check>`

## Stage <3>: <Stage Name>

Goal: `<one-sentence goal for this stage>`

Detailed plan:
[`<feature-slug>-implementation-plan-stage-<n>.md`](<feature-slug>-implementation-plan-stage-<n>.md)

Review focus: `<contract, boundary, or behavior most likely to need careful review>`

Verification target: `<smallest command, test class, validation step, or explicit review check>`

## Additional Stages

Repeat the stage block above until the feature is fully covered. Prefer more small, well-bounded
stages over fewer broad stages when the work spans multiple concerns.

Each stage entry should be understandable without opening the detailed stage plan, but the detailed
stage plan must carry the actionable implementation instructions.

## Final Verification Expectations

The last stage should normally:

- confirm every `Requirement Coverage` entry is implemented, verified, intentionally excluded, or
  still blocked with a named reason;
- confirm the assembled implementation matches the authoritative source;
- record any unresolved open questions or assumptions that still affect behavior or verification;
- run the smallest useful verification scope that exercises the completed feature;
- list the exact commands that should be run;
- record known blockers or unverified areas;
- avoid introducing net-new feature scope.
