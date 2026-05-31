# Reviewer Prompt — Persistence Semantics / Data Integrity / Schema Compatibility

Direct/manual runs: apply reviewer task assembly from
`storage-engine-facade/service/docs/feature-sync-meter-mapping-data/review/workflow.md`, including
`storage-engine-facade/service/docs/feature-sync-meter-mapping-data/review/policies/scout-delegation.md`.

## Mandatory output file

Write full markdown report to:
`storage-engine-facade/service/docs/feature-sync-meter-mapping-data/review/reports/03-persistence.md`

## Area contract

- Repository area: `storage-engine-facade/service`
- Review area: repository semantics, schema/import compatibility, data integrity, and DB failure classification
- Report title: `# Persistence And Data Integrity Review Report`
- Scope summary: `Repository semantics, schema/import compatibility, and DB failure classification.`
- Finding categories: `spec_compliance | bug_risk | code_quality | architecture | data_integrity | tests`
- Additional context defaults: `none`

## Relevant spec sections

- `storage-engine-facade/service/docs/feature-sync-meter-mapping-data/specification/01-runtime-behavior.md`
  - `7.1 Target Table`
  - `7.2 Target Data Model`
  - `7.3 Idempotent Create Semantics`
  - `8.6 Persistence Failure Classification`
  - `12. Relationship To MABIS Balancing Results`
- `storage-engine-facade/service/docs/feature-sync-meter-mapping-data/specification/03-implementation-and-configuration.md`
  - `13.3 Persistence Classification Implementation Details`
  - `13.5 Target Schema Reference Details`
- `storage-engine-facade/service/docs/feature-sync-meter-mapping-data/specification/04-backfill-and-rollout.md`
  - `11.1 Backfill Requirement` — especially duplicate-mapping preflight and import-SQL paragraphs
- `storage-engine-facade/service/docs/feature-sync-meter-mapping-data/specification/05-verification-and-acceptance.md`
  - `14.3 Repository Write Tests`
  - `14.4 Downstream Handoff Coverage`
  - `14.5 Failure Coverage` — persistence-related cases
  - `17. Acceptance Criteria` — especially items `2`, `3`, `4`, `10`, `11`, `15`, and `17`

## Primary files

- `storage-engine-facade/service/src/main/java/com/arvato/smartenergy/storageengine/service/MeterMappingRepository.java`
- `storage-engine-facade/service/src/main/java/com/arvato/smartenergy/storageengine/service/MeterMappingWriteResult.java`
- `storage-engine-facade/service/src/main/java/com/arvato/smartenergy/storageengine/service/BalancingResultPersistenceService.java` if needed for downstream lookup compatibility
- `storage-engine-facade/service/src/main/resources/db/import/import_mabis_meter_id.sql`
- `storage-engine-facade/service/src/main/resources/db/migrations/V1__create_meter_types.sql`
- `storage-engine-facade/service/src/main/resources/db/migrations/V2__add_identifier_column.sql`
- `storage-engine-facade/service/src/main/resources/db/migrations/V3__expand_identifier_column.sql`
- `storage-engine-facade/service/src/main/resources/db/migrations/V4__drop_meter_mapping_category.sql`
- `storage-engine-facade/service/src/main/resources/db/migrations/V5__add_unique_meter_mapping_label_identifier.sql`

## Focus checklist

- `inserted` / `idempotent_noop` / `mapping_conflict` semantics
- Target-collation-equivalent behavior
- Duplicate-key re-read logic, freshness requirements, association robustness, and concurrency/race safety
- Transaction boundaries and stale-read/snapshot risks
- Retryable vs fatal DB classification
- Schema/import compatibility and no runtime compensation for schema drift or import-SQL drift
- Downstream `findMeterId(label, identifier)` compatibility and MABIS balancing-results handoff safety
- Repository design quality: clear responsibilities, centralized data-integrity handling, readable duplicate-key classification
- Hidden bugs: false idempotency/conflict classification, regex matching mistakes, duplicate-key handling, exception classification, SQL/spec drift
- Test concerns only when they directly affect confidence in MySQL-dependent persistence semantics

## Out of scope

- Detailed CAF HTTP classification except where CAF behavior changes a persistence outcome
- Detailed Kafka container behavior except where it directly affects persistence retry/ack expectations
- Broad logging/output review except where required to explain a persistence finding
- Broad test-suite sufficiency review; note only test issues tied to persistence/data integrity
- Broad style commentary not tied to maintainability, data integrity, or correctness

## Report specialization

Use `storage-engine-facade/service/docs/feature-sync-meter-mapping-data/review/templates/area-report.md` with area contract values; report path:
`storage-engine-facade/service/docs/feature-sync-meter-mapping-data/review/reports/03-persistence.md`.
