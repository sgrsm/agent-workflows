# Reviewer Prompt — Listener Runtime / Logging / Startup Config / Operability

Direct/manual runs: apply reviewer task assembly from
`docs/feature-sync-meter-mapping-data/review/workflow.md`, including
`docs/feature-sync-meter-mapping-data/review/policies/scout-delegation.md`.

## Mandatory output file

Write full markdown report to:
`docs/feature-sync-meter-mapping-data/review/reports/04-runtime-operability.md`

## Additional required context

Read the accepted retry-container/error-handler proof ADR:
`docs/feature-sync-meter-mapping-data/adr/0002-retry-container-contract-test-strategy.md`

## Area contract

- Repository area: `.`
- Review area: listener runtime wiring, invalid-value handling, logging, startup/config validation, and operability
- Report title: `# Kafka Config Logging Operability Review Report`
- Scope summary: `Listener/container behavior, invalid-value handling, startup/config validation, logging, and Helm/application config.`
- Finding categories: `spec_compliance | bug_risk | code_quality | architecture | operability | tests`
- Additional context references default: `none`, or the ADR above with referenced sections when relevant
- Additional context checked default: the ADR above with checked section references

## Relevant spec sections

- `docs/feature-sync-meter-mapping-data/specification/01-runtime-behavior.md`
  - `8.1 Record-Level Processing`
  - `8.2 Kafka Value Deserialization And Invalid Kafka Value Handling`
  - `8.3 Post-Listener Exception Handling`
  - `8.4 Terminal States That Acknowledge`
  - `8.5 Failure States That Do Not Acknowledge`
- `docs/feature-sync-meter-mapping-data/specification/02-logging-and-operational-behavior.md`
  - `9.1 Repair-Grade Logging`
  - `9.2 Metrics, Alerts, DLQ, And Repair`
  - `9.3 Startup Validation`
- `docs/feature-sync-meter-mapping-data/specification/03-implementation-and-configuration.md`
  - `10.1 Master-Data Properties`
  - `10.2 New CAF Properties` — only where startup validation and packaged-default caveat affect operability
  - `10.3 CAF Helm Values`
  - `13.1 Kafka Consumer And Invalid-Value Handling Details`
- `docs/feature-sync-meter-mapping-data/specification/04-backfill-and-rollout.md`
  - `11.2 Rollout Sequence` — especially earliest-offset and independent-enablement paragraphs
- `docs/feature-sync-meter-mapping-data/specification/05-verification-and-acceptance.md`
  - `14.2 New Business-Flow Integration Test` — record-level acknowledgment boundary
  - `14.5 Failure Coverage` — retryable/fatal post-listener handling and record-level acknowledgment
  - `17. Acceptance Criteria` — especially items `1`, `5` through `16`

## Primary files

- `src/main/java/com/arvato/smartenergy/storageengine/controller/MasterDataChangesConsumer.java`
- `src/main/java/com/arvato/smartenergy/storageengine/config/MasterDataChangesConsumerConfig.java`
- `src/main/java/com/arvato/smartenergy/storageengine/config/MasterDataChangesStartupValidation.java`
- `src/main/java/com/arvato/smartenergy/storageengine/service/MasterDataChangeLogFormatter.java`
- `src/main/java/com/arvato/smartenergy/storageengine/service/MasterDataChangeLogContext.java`
- `src/main/java/com/arvato/smartenergy/storageengine/service/KafkaRecordMetadata.java`
- `src/main/resources/application.properties`
- `src/main/helm/templates/configMap.yaml`
- `src/main/helm/values.yaml`

## Focus checklist

- Non-batch listener shape
- `MANUAL_IMMEDIATE`, `enable.auto.commit=false`, and record-level ack behavior
- Invalid Kafka value skip+commit path and literal null value handling
- Post-listener retryable vs fatal behavior, error-handler delegation, non-zero redelivery backoff, and listener-container stop behavior
- Retry-container contract proof judged against ADR-0002: public-behavior config/error-handler proof plus Kafka/container runtime proof is sufficient when convincing; framework-private inspection is optional
- Explicit earliest-offset behavior on first start and independent enablement
- Startup-validation correctness, feature-flag gating, CAF-property validation, and packaged-default caveat
- Fixed log fields, categories, CAF status mapping, single-line safety, and raw-value handling
- No added DLQ / metrics / feature-owned alerts / repair workflow code
- Configuration and Helm mapping correctness
- Runtime-wiring architecture: clear listener/handler/invalid-record/logging/config boundaries and operational clarity
- Hidden bugs: wrong ack/non-ack behavior, invalid-value commit mistakes, startup validation gaps/over-validation, logging inconsistency, config/Helm rollout breakage
- Test concerns only when they directly affect confidence in runtime/operability behavior

## Out of scope

- Detailed business-field validation except where it changes listener ack/runtime behavior
- Detailed CAF response classification except where it changes retry, stop, or log behavior here
- Detailed repository duplicate-key semantics except where they change retry/ack behavior
- Broad test-suite sufficiency review; note only test issues tied to runtime/operability
- Broad style commentary not tied to operability, maintainability, or correctness

## Report specialization

Use `docs/feature-sync-meter-mapping-data/review/templates/area-report.md` with area contract values; report path:
`docs/feature-sync-meter-mapping-data/review/reports/04-runtime-operability.md`.
