# Reviewer Prompt — Business Flow / Validation / Handler Orchestration

Direct/manual runs: apply reviewer task assembly from
`storage-engine-facade/service/docs/feature-sync-meter-mapping-data/review/workflow.md`, including
`storage-engine-facade/service/docs/feature-sync-meter-mapping-data/review/policies/scout-delegation.md`.

## Mandatory output file

Write full markdown report to:
`storage-engine-facade/service/docs/feature-sync-meter-mapping-data/review/reports/01-business-flow.md`

## Area contract

- Repository area: `storage-engine-facade/service`
- Review area: business-event handling, validation, operation branching, and handler orchestration
- Report title: `# Business Flow Review Report`
- Scope summary: `Business-event handling, validation, operation branching, and domain-flow structure.`
- Finding categories: `spec_compliance | bug_risk | code_quality | architecture | tests`
- Additional context defaults: `none`

## Relevant spec sections

- `storage-engine-facade/service/docs/feature-sync-meter-mapping-data/specification/01-runtime-behavior.md`
  - `1. Purpose`
  - `4.3 Debezium Value Shape`
  - `4.4 Operation Handling` — especially ignored-operation log-context rules and null-operation path
  - `5. Event Field Validation`
  - `6.1 Endpoint` — only the create-event CAF lookup contract
  - `6.3 CAF Response Handling And Resilience Classification` — only where it affects handler branching
  - `7.3 Idempotent Create Semantics`
  - `8.1 Record-Level Processing` — only where it affects handler expectations
  - `8.2 Kafka Value Deserialization And Invalid Kafka Value Handling` — only the deserialized-null-`op` boundary
  - `8.3 Post-Listener Exception Handling` — only the return-vs-rethrow boundary
  - `8.4 Terminal States That Acknowledge`
  - `8.5 Failure States That Do Not Acknowledge` — only where it affects handler branching
- `storage-engine-facade/service/docs/feature-sync-meter-mapping-data/specification/03-implementation-and-configuration.md`
  - `13.1 Kafka Consumer And Invalid-Value Handling Details` — only listener-shape and acknowledgment-configuration paragraphs
  - `13.4 Debezium Event And Validation Implementation Details`
- `storage-engine-facade/service/docs/feature-sync-meter-mapping-data/specification/05-verification-and-acceptance.md`
  - `17. Acceptance Criteria` — especially items `2` through `9`

## Primary files

- `storage-engine-facade/service/src/main/java/com/arvato/smartenergy/storageengine/service/MasterDataChangeEventHandler.java`
- `storage-engine-facade/service/src/main/java/com/arvato/smartenergy/storageengine/service/MasterDataChangeRowExtractor.java`
- `storage-engine-facade/service/src/main/java/com/arvato/smartenergy/storageengine/service/MasterDataChangeRow.java`
- `storage-engine-facade/service/src/main/java/com/arvato/smartenergy/storageengine/service/MasterDataChangeEventJSONSerde.java`
- `storage-engine-facade/service/src/main/java/com/arvato/smartenergy/storageengine/pojo/DebeziumEvent.java`
- `storage-engine-facade/service/src/main/java/com/arvato/smartenergy/storageengine/pojo/Operation.java`
- nearby validation-related exception classes if needed

## Focus checklist

- Exact behavior for `CREATE`, `UPDATE`, `DELETE`, `READ`, `TRUNCATE`, `MESSAGE`, `UNKNOWN`, and null operation
- Exact field-validation rules and fail-fast order
- Exact use of `after` vs `before`
- No source-value normalization where the spec forbids it
- No CAF lookup or persistence on paths where the spec forbids it
- Correct terminal-return vs exception-throw behavior
- Clean separation between validation, extraction, orchestration, CAF invocation, and persistence
- Business-code quality: structure, naming, SRP, readability, complexity, testability, and clear orchestration boundaries
- Hidden bugs: null handling, missed branches, illegal state, accidental fall-through, wrong operation branching, wrong `before`/`after` usage, validation-order mistakes
- Test concerns only when they directly affect confidence in business-flow behavior

## Out of scope

- Detailed CAF HTTP classification/resilience rules except where needed for handler branching
- Detailed Kafka container configuration except where it changes handler expectations
- Detailed repository duplicate-key race classification except where it changes handler-level outcome semantics
- Broad test-suite sufficiency review; note only test issues tied to a business-flow finding
- Broad style commentary not tied to maintainability or correctness

## Report specialization

Use `storage-engine-facade/service/docs/feature-sync-meter-mapping-data/review/templates/area-report.md` with area contract values; report path:
`storage-engine-facade/service/docs/feature-sync-meter-mapping-data/review/reports/01-business-flow.md`.
