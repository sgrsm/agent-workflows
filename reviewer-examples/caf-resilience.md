# Reviewer Prompt — CAF Client / Response Classification / Resilience

Direct/manual runs: apply reviewer task assembly from
`docs/feature-sync-meter-mapping-data/review/workflow.md`, including
`docs/feature-sync-meter-mapping-data/review/policies/scout-delegation.md`.

## Mandatory output file

Write full markdown report to:
`docs/feature-sync-meter-mapping-data/review/reports/02-caf-resilience.md`

## Area contract

- Repository area: `.`
- Review area: CAF lookup, response classification, retry, and circuit-breaker resilience behavior
- Report title: `# CAF And Resilience Review Report`
- Scope summary: `CAF client behavior, HTTP/result classification, exception taxonomy, retry, and circuit-breaker behavior.`
- Finding categories: `spec_compliance | bug_risk | code_quality | architecture | operability | tests`
- Additional context defaults: `none`

## Relevant spec sections

- `docs/feature-sync-meter-mapping-data/specification/01-runtime-behavior.md`
  - `6.1 Endpoint`
  - `6.2 CAF Client`
  - `6.3 CAF Response Handling And Resilience Classification`
  - `8.3 Post-Listener Exception Handling` — only for propagation semantics
  - `8.5 Failure States That Do Not Acknowledge`
- `docs/feature-sync-meter-mapping-data/specification/02-logging-and-operational-behavior.md`
  - `9.1 Repair-Grade Logging` — especially failure-category / `cafStatus` mapping paragraphs
  - `9.3 Startup Validation` — CAF-property validation only
- `docs/feature-sync-meter-mapping-data/specification/03-implementation-and-configuration.md`
  - `10.2 New CAF Properties`
  - `10.4 Resilience Configuration`
  - `13.2 CAF Client And Resilience Implementation Details`
- `docs/feature-sync-meter-mapping-data/specification/05-verification-and-acceptance.md`
  - `14.5 Failure Coverage` — CAF-related failure cases, resilience coverage, and retryable post-listener proof
  - `17. Acceptance Criteria` — especially items `10` through `13` and `15`

## Primary files

- `src/main/java/com/arvato/smartenergy/storageengine/service/CafClient.java`
- `src/main/java/com/arvato/smartenergy/storageengine/service/CafLookupService.java`
- `src/main/java/com/arvato/smartenergy/storageengine/config/CafClientConfig.java`
- `src/main/java/com/arvato/smartenergy/storageengine/config/ResilienceConfig.java`
- `src/main/java/com/arvato/smartenergy/storageengine/exception/CafBadRequestException.java`
- `src/main/java/com/arvato/smartenergy/storageengine/exception/CafEmptyLabelException.java`
- `src/main/java/com/arvato/smartenergy/storageengine/exception/CafFailureCategory.java`
- `src/main/java/com/arvato/smartenergy/storageengine/exception/CafInvalidLabelException.java`
- `src/main/java/com/arvato/smartenergy/storageengine/exception/CafInvalidResponseException.java`
- `src/main/java/com/arvato/smartenergy/storageengine/exception/CafLookupException.java`
- `src/main/java/com/arvato/smartenergy/storageengine/exception/CafNotFoundException.java`
- `src/main/java/com/arvato/smartenergy/storageengine/exception/CafUnavailableException.java`
- `src/main/java/com/arvato/smartenergy/storageengine/exception/RetryableMasterDataChangeException.java`
- `src/main/java/com/arvato/smartenergy/storageengine/exception/FatalMasterDataChangeException.java`

## Focus checklist

- CAF request URL construction and path handling
- `Accept: application/json`
- Redirect handling disabled
- No automatic HTTP retry behavior
- HTTP status classification and non-HTTP failure classification
- Content-type validation rules
- 200-body parsing and `mabisLabel` validation
- Retry predicates and circuit-breaker record/ignore predicates
- Retry-vs-circuit-breaker interaction, including `CallNotPermittedException`
- Repair-grade logging mappings where CAF classification affects `failureCategory` or `cafStatus`
- Maintainability: centralized classification, clear exception taxonomy, clean HTTP/parsing/exception/resilience boundaries
- Hidden bugs: over-broad catches, unsafe URI construction, redirect assumptions, cause-chain fragility, bad defaults, protocol/client-failure fallback mistakes
- Test concerns only when they directly affect confidence in CAF classification/resilience behavior

## Out of scope

- Detailed Kafka ack/container behavior except where needed for CAF exception propagation semantics
- Detailed repository write classification except where persistence changes CAF-facing outcome expectations
- Broad logging, startup-validation, config, or Helm review except where needed for a CAF/resilience finding
- Broad test-suite sufficiency review; note only test issues tied to a CAF/resilience finding
- Generic coding style commentary not tied to maintainability or correctness

## Report specialization

Use `docs/feature-sync-meter-mapping-data/review/templates/area-report.md` with area contract values; report path:
`docs/feature-sync-meter-mapping-data/review/reports/02-caf-resilience.md`.
