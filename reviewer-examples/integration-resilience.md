# Reviewer Prompt — External Integration / Response Classification / Resilience

Direct/manual runs: apply reviewer task assembly from
`docs/features/example-event-sync/review/workflow.md`, including
`docs/features/example-event-sync/review/policies/scout-delegation.md`.

## Mandatory output file

Write full markdown report to:
`docs/features/example-event-sync/review/reports/02-integration-resilience.md`

## Area contract

- Repository area: `.`
- Review area: external lookup, response classification, retry, and circuit-breaker resilience behavior
- Report title: `# External Integration And Resilience Review Report`
- Scope summary: `External lookup behavior, HTTP/result classification, exception taxonomy, retry, and circuit-breaker behavior.`
- Finding categories: `spec_compliance | bug_risk | code_quality | architecture | operability | tests`
- Additional context defaults: `none`

## Relevant spec sections

- `docs/features/example-event-sync/specification/01-runtime-behavior.md`
  - `6.1 Endpoint`
  - `6.2 External Lookup Client`
  - `6.3 External Lookup Response Handling And Resilience Classification`
  - `8.3 Post-Listener Exception Handling` — only for propagation semantics
  - `8.5 Failure States That Do Not Acknowledge`
- `docs/features/example-event-sync/specification/02-logging-and-operational-behavior.md`
  - `9.1 Repair-Grade Logging` — especially failure-category / `upstreamStatus` mapping paragraphs
  - `9.3 Startup Validation` — external lookup property validation only
- `docs/features/example-event-sync/specification/03-implementation-and-configuration.md`
  - `10.2 External Lookup Properties`
  - `10.4 Resilience Configuration`
  - `13.2 External Lookup Client And Resilience Implementation Details`
- `docs/features/example-event-sync/specification/05-verification-and-acceptance.md`
  - `14.5 Failure Coverage` — external lookup failure cases, resilience coverage, and retryable post-listener proof
  - `17. Acceptance Criteria` — especially items `10` through `13` and `15`

## Primary files

- `src/main/java/com/example/app/service/LookupClient.java`
- `src/main/java/com/example/app/service/LookupService.java`
- `src/main/java/com/example/app/config/LookupClientConfig.java`
- `src/main/java/com/example/app/config/ResilienceConfig.java`
- `src/main/java/com/example/app/exception/LookupBadRequestException.java`
- `src/main/java/com/example/app/exception/LookupEmptyKeyException.java`
- `src/main/java/com/example/app/exception/LookupFailureCategory.java`
- `src/main/java/com/example/app/exception/LookupInvalidKeyException.java`
- `src/main/java/com/example/app/exception/LookupInvalidResponseException.java`
- `src/main/java/com/example/app/exception/LookupException.java`
- `src/main/java/com/example/app/exception/LookupNotFoundException.java`
- `src/main/java/com/example/app/exception/LookupUnavailableException.java`
- `src/main/java/com/example/app/exception/RetryableDomainChangeException.java`
- `src/main/java/com/example/app/exception/FatalDomainChangeException.java`

## Focus checklist

- External lookup request URL construction and path handling
- `Accept: application/json`
- Redirect handling disabled
- No automatic HTTP retry behavior
- HTTP status classification and non-HTTP failure classification
- Content-type validation rules
- 200-body parsing and `externalKey` validation
- Retry predicates and circuit-breaker record/ignore predicates
- Retry-vs-circuit-breaker interaction, including `CallNotPermittedException`
- Repair-grade logging mappings where external lookup classification affects `failureCategory` or `upstreamStatus`
- Maintainability: centralized classification, clear exception taxonomy, clean HTTP/parsing/exception/resilience boundaries
- Hidden bugs: over-broad catches, unsafe URI construction, redirect assumptions, cause-chain fragility, bad defaults, protocol/client-failure fallback mistakes
- Test concerns only when they directly affect confidence in integration classification/resilience behavior

## Out of scope

- Detailed Kafka ack/container behavior except where needed for external lookup exception propagation semantics
- Detailed repository write classification except where persistence changes external lookup outcome expectations
- Broad logging, startup-validation, config, or Helm review except where needed for an integration/resilience finding
- Broad test-suite sufficiency review; note only test issues tied to an integration/resilience finding
- Generic coding style commentary not tied to maintainability or correctness

## Report specialization

Use `docs/features/example-event-sync/review/templates/area-report.md` with area contract values; report path:
`docs/features/example-event-sync/review/reports/02-integration-resilience.md`.
