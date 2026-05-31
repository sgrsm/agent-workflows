# Reviewer Prompt — Test Coverage / Proof Strength / Test Design

Direct/manual runs: apply reviewer task assembly from
`docs/features/example-event-sync/review/workflow.md`, including
`docs/features/example-event-sync/review/policies/scout-delegation.md`.

## Mandatory output file

Write full markdown report to:
`docs/features/example-event-sync/review/reports/05-tests.md`

## Additional required context

For runtime error-handling proof, read:
`docs/features/example-event-sync/adr/0002-runtime-error-handling-test-strategy.md`

## Area contract

- Repository area: `.`
- Review area: test coverage, proof strength, test design, and test-related dependency/build hygiene
- Report title: `# Test Review Report`
- Scope summary: `Test coverage, suite design, and test code quality for the example feature.`
- Finding categories: `tests | spec_compliance | bug_risk | code_quality | architecture`
- Additional context references default: `none`, or the ADR above with referenced sections when relevant
- Additional context checked default: the ADR above with checked section references

## Relevant spec sections

- `docs/features/example-event-sync/specification/05-verification-and-acceptance.md`
  - `14.1 Connector Test Boundary`
  - `14.2 New Business-Flow Integration Test`
  - `14.3 Repository Write Tests`
  - `14.4 Downstream Handoff Coverage`
  - `14.5 Failure Coverage`
  - `17. Acceptance Criteria`
- `docs/features/example-event-sync/specification/01-runtime-behavior.md`
  - `4.4 Operation Handling`
  - `5. Event Field Validation`
  - `6.3 External Lookup Response Handling And Resilience Classification`
  - `7.3 Idempotent Create Semantics`
  - `8.1 Record-Level Processing`
  - `8.2 Kafka Value Deserialization And Invalid Kafka Value Handling`
  - `8.3 Post-Listener Exception Handling`
  - `8.4 Terminal States That Acknowledge`
  - `8.5 Failure States That Do Not Acknowledge`
  - `8.6 Persistence Failure Classification`
- `docs/features/example-event-sync/specification/02-logging-and-operational-behavior.md`
  - `9.1 Repair-Grade Logging`
  - `9.3 Startup Validation`
- `docs/features/example-event-sync/specification/03-implementation-and-configuration.md`
  - `10.1 Feature Properties`
  - `10.2 External Lookup Properties`
  - `10.4 Resilience Configuration`
  - `13.1 Kafka Consumer And Invalid-Value Handling Details`
  - `13.2 External Lookup Client And Resilience Implementation Details`
  - `13.3 Persistence Classification Implementation Details`
  - `13.4 CDC Event And Validation Implementation Details`

## Primary files

Inspect all changed test files under `src/test/java/**`, especially:

- `src/test/java/com/example/app/EventSyncConsumerTest.java`
- `src/test/java/com/example/app/DomainChangesConsumerRecordTest.java`
- `src/test/java/com/example/app/EntityMappingRepositoryMySqlTest.java`
- `src/test/java/com/example/app/config/DomainChangesConsumerConfigUnitTest.java`
- `src/test/java/com/example/app/config/DomainChangesStartupValidationUnitTest.java`
- `src/test/java/com/example/app/config/ResilienceConfigUnitTest.java`
- `src/test/java/com/example/app/controller/DomainChangesConsumerUnitTest.java`
- `src/test/java/com/example/app/service/LookupClientTest.java`
- `src/test/java/com/example/app/service/LookupClientUnitTest.java`
- `src/test/java/com/example/app/service/LookupServiceUnitTest.java`
- `src/test/java/com/example/app/service/DomainEventHandlerUnitTest.java`
- `src/test/java/com/example/app/service/DomainEventJsonSerdeUnitTest.java`
- `src/test/java/com/example/app/service/DomainChangeLogFormatterUnitTest.java`
- `src/test/java/com/example/app/service/DomainRowExtractorUnitTest.java`
- `src/test/java/com/example/app/service/EntityMappingRepositoryUnitTest.java`
- `src/test/resources/log4j2.xml`
- `pom.xml`

## Focus checklist

- Required happy path, idempotent path, conflict path, skipped path, retryable path, and fatal path coverage
- Record-level ack, redelivery, listener-container stop, and invalid Kafka value handling proof
- Runtime error-handling proof judged against ADR-0002: public-behavior config/error-handler proof plus Kafka/container runtime proof is sufficient when convincing; framework-private inspection is optional
- MySQL-specific repository semantics proof
- Correct split between unit, integration, WireMock, and Testcontainers tests
- Assertion strength, specificity, meaningful names, focused setup, low duplication, maintainability, and flake resistance
- Tests proving behavior rather than re-encoding implementation details; avoid over-mocking or overly broad tests that obscure failures
- Dependency/build hygiene: `wiremock-standalone` dependency without local version, naming convention `*Test` vs `*UnitTest`, and test-scope setup

## Test-gap ROI and practicality gate

Report missing/weak/proof-strength test findings only when all are true:

1. the uncovered behavior has meaningful business, data-integrity, operational, security, or rollout impact;
2. the regression is plausible, not merely theoretical;
3. existing tests plus code/static inspection do not already provide reasonable confidence;
4. the added test would be small, deterministic, maintainable, and not mostly duplicate framework behavior, exact static text, or exhaustive vendor/platform permutations.

Do not raise low-ROI exhaustive proof gaps such as exact timing proof, all SQL/text drift forms, all vendor exception-chain shapes, all platform-specific messages, all error codes, all schema variants, or all malformed data permutations unless the spec explicitly requires that exact proof and risk is material. If a real gap is not worth actionable follow-up, mention it only under optional `## Info` with a brief explanation.

## Out of scope

- Broad production-code review unrelated to whether tests adequately prove behavior
- Standalone production-code findings outside this area unless needed to explain missing or weak test proof
- Trivial style commentary not tied to maintainability, readability, or reliability of tests

## Report specialization

Use `docs/features/example-event-sync/review/templates/area-report.md` with area contract values; report path:
`docs/features/example-event-sync/review/reports/05-tests.md`.

Start the report with this area-specific section before `## Findings`:

```md
## Coverage Matrix

| Required area | Status | Notes |
|---|---|---|
| <area> | covered | <notes> |
```

If using non-actionable observations from the ROI gate, add this optional section before `## Findings`:

```md
## Info
- <non-actionable observations, or omit the section if none>
```
