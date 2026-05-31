# Reviewer Prompt — Documentation Follow-Up / Cross-Feature Doc Consistency

Direct/manual runs: apply reviewer task assembly from
`docs/features/example-event-sync/review/workflow.md`, including
`docs/features/example-event-sync/review/policies/scout-delegation.md`.

## Mandatory output file

Write full markdown report to:
`docs/features/example-event-sync/review/reports/06-documentation.md`

## Area contract

- Repository area: `docs`
- Review area: documentation follow-up, stale-reference detection, and cross-document consistency
- Report title: `# Documentation Follow-Up Review Report`
- Scope summary: `Cross-feature documentation follow-up, stale-reference detection, and ownership/behavior consistency.`
- Finding categories: `documentation | spec_compliance | bug_risk | code_quality`
- Additional context defaults: `none`

## Relevant spec sections

- `docs/features/example-event-sync/specification/01-runtime-behavior.md`
  - `7.1 Target Table` — only where it clarifies `app.entity_mapping` ownership or responsibility
  - `12. Relationship To Downstream Read Model`
- `docs/features/example-event-sync/specification/05-verification-and-acceptance.md`
  - `15. Documentation Follow-Up`

## Primary files

- `docs/features/example-upstream-cdc-source/upstream-cdc-source-detailed-spec.md`
- `docs/features/example-upstream-cdc-source/upstream-cdc-source-overview.md`
- `docs/features/example-downstream-read-model/downstream-read-model-spec.md`
- `docs/features/example-downstream-read-model/downstream-read-model-overview.md`
- nearby documentation files if needed to validate whether an overview or related page also needs the follow-up update

## Focus checklist

- Whether upstream CDC connector documentation now states that business handling is implemented by this feature
- Whether downstream read-model documentation now states that `app.entity_mapping` is maintained by this CDC business handler for new source mappings
- Whether overview docs are updated when they describe affected responsibilities
- Stale, contradictory, ambiguous, or misleading ownership/behavior statements across touched documentation
- Documentation quality: precise scope, consistent terminology, maintainable cross-feature references, and wording that will not mislead future feature work, rollout planning, or troubleshooting
- Verification/readiness context only where needed to check that docs match required implementation follow-up

## Out of scope

- Fresh primary review of production code, tests, configuration, or SQL except where needed to validate a documentation claim
- Broad editorial/style commentary not tied to accuracy, maintainability, or change safety
- Re-review of the full feature spec set beyond what is needed to judge documentation follow-up correctness

## Report specialization

Use `docs/features/example-event-sync/review/templates/area-report.md` with area contract values; report path:
`docs/features/example-event-sync/review/reports/06-documentation.md`.
