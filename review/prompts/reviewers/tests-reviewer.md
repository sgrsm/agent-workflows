# Reviewer Prompt — Tests Review

After copying/renaming this template and resolving its per-area placeholders, direct/manual runs
must apply reviewer task assembly from `<review_pack_base_path>/workflow.md`, including
`<review_pack_base_path>/policies/scout-delegation.md`.

Use this as a source template only for one test-review row. Copy and rename this file for the
concrete feature-specific test/proof-strength review area before use, then replace every per-area
placeholder in the copy. It stays separate because test review needs the ROI/practicality gate and
optional pre-findings report sections that are not shared by the generic area-reviewer template.

## Mandatory output file

Write full markdown report to:
`<report_output_path>`

## Additional required context

Use this section when the workflow row needs extra ADRs, contracts, or adjacent reference docs. If
none are needed, replace the body with `none` or remove the section in the instantiated copy.

<additional_required_context_block>

## Area contract

- Repository area: `<repository_area>`
- Review area: `<review_area>`
- Report title: `<report_title>`
- Scope summary: `<scope_summary>`
- Finding categories: `<finding_categories>`
- Additional context defaults: `<additional_context_defaults_line>`
- Additional context checked default: `<additional_context_checked_defaults_line>`

## Relevant spec sections

<relevant_spec_sections_block>

## Primary files

Inspect changed test files and supporting test resources/build configuration relevant to the
feature, especially:

<primary_files_block>

## Focus checklist

Always cover these default tests-review concerns:

- required happy path, validation/conflict path, skipped path, retryable path, and fatal path
  coverage where those behaviors exist
- idempotent-path proof where the feature behavior requires idempotency
- correct split between unit, integration, WireMock, and Testcontainers tests when those layers are
  used
- assertion strength, specificity, meaningful names, focused setup, low duplication,
  maintainability, and flake resistance
- tests proving behavior rather than re-encoding implementation details; avoid over-mocking or
  overly broad tests that obscure failures
- dependency/build hygiene, including test-scope setup and naming conventions such as `*Test` vs
  `*UnitTest`
- when the feature includes message-driven or async processing, proof for acknowledgement,
  redelivery, stop/fatal behavior, and invalid-input or deserialization handling
- when persistence changes are in scope, proof for database-specific repository semantics
- when retry/error-handler behavior is in scope, prefer convincing public-behavior and runtime
  proof; framework-private inspection is optional unless the spec explicitly requires more

Add feature-specific checks below when needed. If no additions are needed, replace the block with
`none`.

<focus_checklist_block>

## Test-gap ROI and practicality gate

Use this default gate unless the instantiated copy intentionally adds or replaces stricter
feature-specific rules.

Report missing/weak/proof-strength test findings only when all are true:

1. the uncovered behavior has meaningful business, data-integrity, operational, security, or rollout impact;
2. the regression is plausible, not merely theoretical;
3. existing tests plus code/static inspection do not already provide reasonable confidence;
4. the added test would be small, deterministic, maintainable, and not mostly duplicate framework behavior, exact static text, or exhaustive vendor/platform permutations.

Do not raise low-ROI exhaustive proof gaps such as exact timing proof, all SQL/text drift forms,
all vendor exception-chain shapes, all platform-specific messages, all error codes, all schema
variants, or all malformed data permutations unless the spec explicitly requires that exact proof
and risk is material. If a real gap is not worth actionable follow-up, mention it only under
optional `## Info` with a brief explanation.

Feature-specific addendum or replacement, if any:

<test_gap_roi_gate_block>

## Out of scope

Always keep these default boundaries:

- broad production-code review unrelated to whether tests adequately prove behavior
- standalone production-code findings outside this area unless needed to explain missing or weak
  test proof
- trivial style commentary not tied to maintainability, readability, or reliability of tests

Add feature-specific exclusions below when needed. If none are needed, replace the block with
`none`.

<out_of_scope_block>

## Special report sections

Before `## Findings`, keep these default pre-findings sections:

```md
## Coverage Matrix

| Required area | Status | Notes |
|---|---|---|
| <area> | covered | <notes> |
```

If using non-actionable observations from the ROI gate, also add this optional section before
`## Findings`:

```md
## Info
- <non-actionable observations, or omit the section if none>
```

Add extra feature-specific pre-findings sections only when needed. If none are needed, replace the
block below with `none`.

<special_report_sections_block>

## Report specialization

Use `<review_pack_base_path>/templates/area-report.md` with area contract values; report
path:
`<report_output_path>`.
