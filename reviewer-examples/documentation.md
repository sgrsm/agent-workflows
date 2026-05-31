# Reviewer Prompt — Documentation Follow-Up / Cross-Feature Doc Consistency

Direct/manual runs: apply reviewer task assembly from
`storage-engine-facade/service/docs/feature-sync-meter-mapping-data/review/workflow.md`, including
`storage-engine-facade/service/docs/feature-sync-meter-mapping-data/review/policies/scout-delegation.md`.

## Mandatory output file

Write full markdown report to:
`storage-engine-facade/service/docs/feature-sync-meter-mapping-data/review/reports/06-documentation.md`

## Area contract

- Repository area: `storage-engine-facade/service/docs`
- Review area: documentation follow-up, stale-reference detection, and cross-document consistency
- Report title: `# Documentation Follow-Up Review Report`
- Scope summary: `Cross-feature documentation follow-up, stale-reference detection, and ownership/behavior consistency.`
- Finding categories: `documentation | spec_compliance | bug_risk | code_quality`
- Additional context defaults: `none`

## Relevant spec sections

- `storage-engine-facade/service/docs/feature-sync-meter-mapping-data/specification/01-runtime-behavior.md`
  - `7.1 Target Table` — only where it clarifies `esm_sef.meter_mapping` ownership or responsibility
  - `12. Relationship To MABIS Balancing Results`
- `storage-engine-facade/service/docs/feature-sync-meter-mapping-data/specification/05-verification-and-acceptance.md`
  - `15. Documentation Follow-Up`

## Primary files

- `storage-engine-facade/service/docs/feature-debezium-connect-master-data/feature-debezium-connect-detailed-spec.md`
- `storage-engine-facade/service/docs/feature-debezium-connect-master-data/feature-debezium-connect-overview-spec.md`
- `storage-engine-facade/service/docs/feature-mabis-balancing-results/feature-mabis-balancing-results-spec.md`
- `storage-engine-facade/service/docs/feature-mabis-balancing-results/feature-mabis-balancing-results-overview.md`
- nearby documentation files if needed to validate whether an overview or related page also needs the follow-up update

## Focus checklist

- Whether Debezium connector documentation now states that business handling is implemented by this feature
- Whether MABIS balancing-results documentation now states that `esm_sef.meter_mapping` is maintained by this CDC business handler for new source mappings
- Whether overview docs are updated when they describe affected responsibilities
- Stale, contradictory, ambiguous, or misleading ownership/behavior statements across touched documentation
- Documentation quality: precise scope, consistent terminology, maintainable cross-feature references, and wording that will not mislead future feature work, rollout planning, or troubleshooting
- Verification/readiness context only where needed to check that docs match required implementation follow-up

## Out of scope

- Fresh primary review of production code, tests, configuration, or SQL except where needed to validate a documentation claim
- Broad editorial/style commentary not tied to accuracy, maintainability, or change safety
- Re-review of the full feature spec set beyond what is needed to judge documentation follow-up correctness

## Report specialization

Use `storage-engine-facade/service/docs/feature-sync-meter-mapping-data/review/templates/area-report.md` with area contract values; report path:
`storage-engine-facade/service/docs/feature-sync-meter-mapping-data/review/reports/06-documentation.md`.
