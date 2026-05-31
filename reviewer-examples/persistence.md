# Reviewer Prompt — Persistence Semantics / Data Integrity / Schema Compatibility

Direct/manual runs: apply reviewer task assembly from
`docs/features/example-event-sync/review/workflow.md`, including
`docs/features/example-event-sync/review/policies/scout-delegation.md`.

## Mandatory output file

Write full markdown report to:
`docs/features/example-event-sync/review/reports/03-persistence.md`

## Area contract

- Repository area: `.`
- Review area: repository semantics, schema/import compatibility, data integrity, and DB failure classification
- Report title: `# Persistence And Data Integrity Review Report`
- Scope summary: `Repository semantics, schema/import compatibility, and DB failure classification.`
- Finding categories: `spec_compliance | bug_risk | code_quality | architecture | data_integrity | tests`
- Additional context defaults: `none`

## Relevant spec sections

- `docs/features/example-event-sync/specification/01-runtime-behavior.md`
  - `7.1 Target Table`
  - `7.2 Target Data Model`
  - `7.3 Idempotent Create Semantics`
  - `8.6 Persistence Failure Classification`
  - `12. Relationship To Downstream Read Model`
- `docs/features/example-event-sync/specification/03-implementation-and-configuration.md`
  - `13.3 Persistence Classification Implementation Details`
  - `13.5 Target Schema Reference Details`
- `docs/features/example-event-sync/specification/04-backfill-and-rollout.md`
  - `11.1 Backfill Requirement` — especially duplicate-mapping preflight and import-SQL paragraphs
- `docs/features/example-event-sync/specification/05-verification-and-acceptance.md`
  - `14.3 Repository Write Tests`
  - `14.4 Downstream Handoff Coverage`
  - `14.5 Failure Coverage` — persistence-related cases
  - `17. Acceptance Criteria` — especially items `2`, `3`, `4`, `10`, `11`, `15`, and `17`

## Primary files

- `src/main/java/com/example/app/service/EntityMappingRepository.java`
- `src/main/java/com/example/app/service/EntityMappingWriteResult.java`
- `src/main/java/com/example/app/service/ReadModelPersistenceService.java` if needed for downstream lookup compatibility
- `src/main/resources/db/import/import_entity_mapping_seed.sql`
- `src/main/resources/db/migrations/V1__create_entity_types.sql`
- `src/main/resources/db/migrations/V2__add_identifier_column.sql`
- `src/main/resources/db/migrations/V3__expand_identifier_column.sql`
- `src/main/resources/db/migrations/V4__drop_entity_mapping_category.sql`
- `src/main/resources/db/migrations/V5__add_unique_entity_mapping_key_identifier.sql`

## Focus checklist

- `inserted` / `idempotent_noop` / `mapping_conflict` semantics
- Target-collation-equivalent behavior
- Duplicate-key re-read logic, freshness requirements, association robustness, and concurrency/race safety
- Transaction boundaries and stale-read/snapshot risks
- Retryable vs fatal DB classification
- Schema/import compatibility and no runtime compensation for schema drift or import-SQL drift
- Downstream `findTargetId(externalKey, identifier)` compatibility and downstream read model handoff safety
- Repository design quality: clear responsibilities, centralized data-integrity handling, readable duplicate-key classification
- Hidden bugs: false idempotency/conflict classification, regex matching mistakes, duplicate-key handling, exception classification, SQL/spec drift
- Test concerns only when they directly affect confidence in MySQL-dependent persistence semantics

## Out of scope

- Detailed external lookup HTTP classification except where external lookup behavior changes a persistence outcome
- Detailed Kafka container behavior except where it directly affects persistence retry/ack expectations
- Broad logging/output review except where required to explain a persistence finding
- Broad test-suite sufficiency review; note only test issues tied to persistence/data integrity
- Broad style commentary not tied to maintainability, data integrity, or correctness

## Report specialization

Use `docs/features/example-event-sync/review/templates/area-report.md` with area contract values; report path:
`docs/features/example-event-sync/review/reports/03-persistence.md`.
