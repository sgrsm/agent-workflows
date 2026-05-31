# Focus Checklist Category Catalog

Template-only helper for instantiating non-test reviewer prompts in
`review/`.

Use it to suggest reusable focus-checklist categories, including area-specific reviewer concerns and
cross-cutting clean-code / clean-architecture overlays, not to force one fixed checklist into every
review pack.

## How To Use

- Use this file during instantiation only; do not treat it as a runtime reviewer component.
- First choose the reviewer areas.
- Then, for each non-test reviewer area, suggest a small set of relevant category ids from this
  catalog.
- If the user wants category-based control, let them accept/edit/remove category ids per reviewer.
- If the user wants auto-selection, use the chosen categories plus the concrete spec and/or diff to
  generate a concise feature-specific `focusChecklistBlock`.
- For Java-heavy non-test reviewers, combine area-specific categories with cross-cutting `CC-*`
  and `CA-*` categories only when they materially fit the area.
- If the user asks whether to add a dedicated cross-cutting Java maintainability/design reviewer,
  recommend one combined reviewer for most feature reviews; split into separate clean-code and
  SOLID/class-design reviewers only for large or design-heavy diffs where one report would become
  too broad.
- A dedicated cross-cutting reviewer is additive. Keep it scoped to changed non-test Java code and
  maintainability/design quality; do not let it replace area-specific correctness review.
- Do not dump every checklist starter into one prompt. Keep only the items that materially fit the
  reviewer area and current feature.
- Tests review has its own built-in default checklist in
  `prompts/reviewers/tests-reviewer.md`; use this catalog mainly for non-test reviewers.

## Suggested Category Bundles By Reviewer Archetype

These are starting bundles, not mandatory presets.

- Business flow / validation / orchestration reviewer:
  area-specific `BF-1`, `BF-2`, `BF-3`, `BF-4`, `BF-5`
  optional overlays `CC-1`, `CC-2`, `CC-3`, `CC-5`, `CA-1`, `CA-2`
- External integration / response classification / resilience reviewer:
  area-specific `IR-1`, `IR-2`, `IR-3`, `IR-4`, `IR-5`, `IR-6`
  optional overlays `CC-1`, `CC-2`, `CC-5`, `CA-1`, `CA-4`, `CA-5`
- Persistence / data integrity / schema compatibility reviewer:
  area-specific `PD-1`, `PD-2`, `PD-3`, `PD-4`, `PD-5`, `PD-6`, `PD-7`
  optional overlays `CC-1`, `CC-2`, `CC-4`, `CC-5`, `CA-1`, `CA-4`, `CA-5`
- Runtime / operability / logging / startup-config reviewer:
  area-specific `RT-1`, `RT-2`, `RT-3`, `RT-4`, `RT-5`, `RT-6`, `RT-7`, `RT-8`
  optional overlays `CC-1`, `CC-2`, `CC-5`, `CA-1`, `CA-4`, `CA-5`
- Documentation follow-up / cross-doc consistency reviewer:
  `DOC-1`, `DOC-2`, `DOC-3`, `DOC-4`, `DOC-5`, `DOC-6`
- Dedicated cross-cutting clean-code / SOLID reviewer (recommended when a dedicated reviewer is
  wanted):
  `CC-1`, `CC-2`, `CC-3`, `CC-4`, `CC-5`, `CC-6`, `CA-1`, `CA-2`, `CA-3`, `CA-4`, `CA-5`
- Dedicated clean-code / local maintainability reviewer (split option):
  `CC-1`, `CC-2`, `CC-3`, `CC-4`, `CC-5`, `CC-6`
- Dedicated SOLID / class-design reviewer (split option):
  `CA-1`, `CA-2`, `CA-3`, `CA-4`, `CA-5`
- Cross-cutting Java clean-code overlay for non-test code reviewers:
  `CC-1`, `CC-2`, `CC-3`, `CC-4`, `CC-5`, `CC-6`
- Cross-cutting Java clean-architecture overlay for non-test code reviewers:
  `CA-1`, `CA-2`, `CA-3`, `CA-4`, `CA-5`

## Business Flow / Validation / Orchestration

### BF-1. Operation semantics and branching

Use when the area covers action/event classification, handler selection, or flow routing.

Checklist starters:

- exact behavior for each supported action/state and for unsupported, unknown, or null variants
- correct branch routing with no accidental fall-through or wrong default path
- return-vs-throw or terminal-vs-retry boundary matches the intended control-flow contract

### BF-2. Validation and fail-fast rules

Use when the area owns field validation, preconditions, or source-shape enforcement.

Checklist starters:

- exact field-validation rules and validation order
- fail-fast behavior occurs at the correct boundary
- missing/invalid required inputs are handled consistently and explicitly
- no forbidden normalization, coercion, or silent cleanup of source values

### BF-3. Source record/state usage and side-effect gating

Use when behavior depends on selecting the correct source section/state and on calling downstream
systems only on eligible paths.

Checklist starters:

- correct use of source-state variants such as previous/current, before/after, old/new, or draft/final data
- downstream lookup/persistence/publication/side effects happen only on allowed paths
- forbidden paths do not leak partial side effects or hidden work

### BF-4. Orchestration boundaries and maintainability

Use when the area spans validation, extraction, mapping, orchestration, downstream calls, or
persistence coordination.

Checklist starters:

- clean separation between validation, extraction/mapping, orchestration, downstream invocation, and persistence
- intention-revealing structure, naming, SRP, readability, and testability
- orchestration boundaries stay explicit rather than being smeared across helpers or branches

### BF-5. Latent branching and state bug patterns

Use when the area is branch-heavy or state-sensitive.

Checklist starters:

- null handling, illegal state, and missed-branch coverage
- wrong state-section usage or ordering mistakes
- hidden branch coupling or assumptions that break on new enum/state values

## External Integration / Response Classification / Resilience

### IR-1. Request construction and protocol assumptions

Use when the area owns external request setup or client protocol behavior.

Checklist starters:

- request URL/path/query construction is correct and safely encoded
- required request headers/content negotiation are present
- redirect handling, retry assumptions, and protocol defaults match the intended contract
- no accidental dependence on client-library defaults that change semantics

### IR-2. Response classification and payload validation

Use when the area classifies status codes, content types, or successful payload bodies.

Checklist starters:

- HTTP/result/status classification is explicit and complete
- non-success payloads and malformed-success payloads are handled distinctly where required
- content-type/schema/body validation rules are enforced at the correct layer
- required response fields are validated before downstream use

### IR-3. Exception taxonomy and failure mapping

Use when the area converts client/protocol failures into domain/runtime exceptions.

Checklist starters:

- exception taxonomy is clear, centralized, and maintainable
- retryable vs fatal vs non-actionable failures are mapped consistently
- cause-chain inspection and fallback behavior are robust rather than brittle
- broad catches do not erase important classification detail

### IR-4. Retry and circuit-breaker behavior

Use when the feature owns retry rules, breaker rules, or interaction between resilience mechanisms.

Checklist starters:

- retry predicates and breaker record/ignore predicates match the intended classification model
- retry-vs-breaker interaction is coherent, including short-circuit or call-not-permitted cases
- no hidden automatic retry path undermines the intended failure policy

### IR-5. Operability and logging mapping

Use when integration classification affects log fields, repairability, or status mapping.

Checklist starters:

- failure-category/status mapping is consistent with the intended operational contract
- logs surface the right classification without ambiguity or silent fallback
- repair/troubleshooting fields stay aligned with integration outcomes

### IR-6. Maintainability and latent integration bug patterns

Use when the area has non-trivial client, parsing, classification, and resilience code.

Checklist starters:

- HTTP, parsing, classification, exception, and resilience responsibilities remain cleanly separated
- unsafe URI construction, bad defaults, or redirect assumptions are avoided
- over-broad catches, fallback mistakes, or parsing shortcuts do not hide real failures

## Persistence / Data Integrity / Schema Compatibility

### PD-1. Write outcome semantics

Use when the area defines write-result categories or business meaning for repository outcomes.

Checklist starters:

- insert / update / noop / idempotent / conflict semantics are explicit and correct
- repository results align with the intended business contract, not just raw DB behavior
- no ambiguous outcome bucket hides materially different data-integrity states

### PD-2. Idempotency, uniqueness, and conflict detection

Use when the area relies on uniqueness keys, equivalent-value comparison, or idempotent writes.

Checklist starters:

- uniqueness and idempotency rules match the intended collation/equivalence behavior
- benign duplicates are separated from true conflicts correctly
- value comparison and conflict detection avoid false positives and false negatives

### PD-3. Duplicate-key reread, freshness, and concurrency safety

Use when the area rereads after write races or depends on post-conflict reads.

Checklist starters:

- duplicate-key reread logic is robust and freshness assumptions are explicit
- concurrent write races do not misclassify outcomes or lose association integrity
- reread paths do not depend on stale snapshots or fragile timing assumptions

### PD-4. Transaction boundaries and DB failure classification

Use when the area distinguishes retryable DB failures, fatal DB failures, or transaction scopes.

Checklist starters:

- transaction boundaries support the intended consistency model
- stale-read/snapshot risks are understood and controlled
- retryable vs fatal DB classification is explicit and maintainable
- exception classification does not accidentally swallow integrity failures

### PD-5. Schema/import compatibility and drift handling

Use when the feature depends on migrations, import SQL, or a fixed target schema.

Checklist starters:

- schema and import assets stay compatible with runtime expectations
- no hidden runtime compensation masks schema/import drift unless explicitly intended
- migration and import assumptions stay aligned with application code

### PD-6. Downstream read and handoff compatibility

Use when persistence changes must remain compatible with downstream readers or handoff flows.

Checklist starters:

- downstream queries/finders/handoffs still match the written data shape and semantics
- new write behavior does not silently break adjacent consumers of the same table or view
- compatibility boundaries are explicit where multiple features share data ownership assumptions

### PD-7. Maintainability and latent persistence bug patterns

Use when the repository layer contains classification, reread, or schema-sensitive logic.

Checklist starters:

- responsibilities for data-integrity handling stay centralized and readable
- regex/string matching or SQL-text assumptions are not brittle
- duplicate-key handling, exception classification, and schema drift checks avoid fragile shortcuts

## Runtime / Operability / Logging / Startup Config

### RT-1. Listener or consumer shape and acknowledgement semantics

Use when the area owns listener/consumer mode, ack boundaries, or commit behavior.

Checklist starters:

- batch-vs-record processing shape matches the intended contract
- manual ack / auto-commit / immediate-ack settings align with expected behavior
- acknowledgement happens only on the intended success/skip boundaries

### RT-2. Invalid input and deserialization handling

Use when the area must tolerate malformed, null, or otherwise invalid incoming records.

Checklist starters:

- invalid-input path acknowledges, skips, retries, or stops exactly as intended
- malformed or literal-null payload handling is explicit
- invalid-input logging and commit behavior are consistent and safe

### RT-3. Retryable, fatal, error-handler, and container-stop behavior

Use when runtime failure handling is delegated through framework wiring.

Checklist starters:

- retryable vs fatal propagation is correct after listener/entry-point failure
- error-handler delegation and backoff behavior match the intended runtime contract
- stop/pause/continue behavior is explicit for fatal or exhausted-retry cases
- runtime proof relies on convincing public-behavior evidence; framework-private inspection is optional unless explicitly required

### RT-4. Startup validation and feature gating

Use when startup validation, enablement flags, or configuration gating affect safety.

Checklist starters:

- startup validation checks the right properties and only when the feature is enabled or otherwise in scope
- feature gating avoids both under-validation and over-validation
- packaged defaults and environment-specific overrides do not create unsafe startup assumptions

### RT-5. Logging contract and repairability

Use when logs are part of the operational or repair contract.

Checklist starters:

- required log fields, categories, and status mappings are consistently populated
- single-line safety, raw-value handling, and redaction/verbosity boundaries are sane
- logs remain usable for repair, triage, and audit of runtime outcomes

### RT-6. Config, packaging, and rollout mapping

Use when runtime behavior depends on application config, deployment config, or first-start behavior.

Checklist starters:

- application config, environment mapping, and deployment/Helm wiring are aligned
- first-start, offset, enablement, and rollout sequencing semantics match expectations
- config naming/defaults do not create hidden rollout or operability traps

### RT-7. Operational-surface boundaries

Use when the spec limits which operational features this area should own.

Checklist starters:

- only explicitly owned operational surfaces are introduced (for example DLQ, metrics, alerts, repair flows, or background recovery logic)
- adjacent operational capabilities are not silently broadened without clear ownership
- absence of a feature-owned operational surface is intentional rather than accidental

### RT-8. Architecture and latent runtime bug patterns

Use when runtime logic spans listener wiring, config, validation, logging, and error handling.

Checklist starters:

- listener, handler, invalid-record processing, logging, and config responsibilities remain clearly separated
- hidden ack/non-ack mistakes, validation gaps, logging inconsistencies, and rollout breakage risks are checked explicitly
- runtime wiring stays understandable enough to debug safely under production pressure

## Documentation / Cross-Document Consistency

### DOC-1. Ownership and responsibility statements

Use when docs must state which component, service, or workflow owns a behavior or dataset.

Checklist starters:

- documentation states ownership/responsibility clearly and consistently
- ownership wording matches the intended implementation and operational model
- no outdated statements imply the wrong owning component or workflow

### DOC-2. Cross-document consistency

Use when multiple specs, overviews, or related feature docs describe the same capability.

Checklist starters:

- overview, detailed, and related docs tell a consistent story
- behavior/responsibility language does not diverge across neighboring documents
- changes in one doc are reflected wherever the same contract is summarized elsewhere

### DOC-3. Follow-up completeness

Use when a feature should trigger updates outside its own local spec set.

Checklist starters:

- all obviously affected overviews and related pages are updated
- documentation follow-up is complete enough for rollout, troubleshooting, and future change work
- no known dependent doc is left stale merely because it lives outside the feature folder

### DOC-4. Stale, contradictory, or ambiguous references

Use when docs can drift or leave misleading breadcrumbs.

Checklist starters:

- stale ownership/behavior references are removed or corrected
- contradictory statements are reconciled rather than left for readers to interpret
- ambiguous wording is tightened where it could mislead design, rollout, or support work

### DOC-5. Terminology and future maintainability

Use when docs introduce or rename terms and cross-feature references.

Checklist starters:

- terminology stays precise and consistent across docs
- cross-feature references remain understandable and maintainable
- wording is future-safe enough not to mislead the next feature touching the same area

### DOC-6. Implementation-alignment checks

Use when docs must match actual behavior but this reviewer should stay documentation-focused.

Checklist starters:

- implementation/spec verification is used only as needed to validate the documentation claim
- documentation does not promise behavior the implementation or accepted spec does not support
- documentation review stays scoped to accuracy and follow-up correctness rather than expanding into a full code review

## Clean Code / Local Maintainability

### CC-1. Naming clarity and domain language

Use when local readability depends on whether names explain intent and use the right domain terms.

Checklist starters:

- names are intention-revealing, pronounceable, searchable, and use domain language rather than vague technical noise
- one concept keeps one consistent term across the area instead of switching between near-synonyms
- generic buckets such as `data`, `info`, `manager`, numbered variants, or type-noise names are avoided
- names add enough context without repeating surrounding class/package noise

### CC-2. Method size, focus, and abstraction level

Use when readability and safe change depend on keeping methods coherent and easy to scan.

Checklist starters:

- methods stay small enough to read quickly and focus on one thing
- one level of abstraction is kept per method instead of mixing policy with low-level detail
- guard clauses keep the happy path visible and reduce deep nesting
- orchestration is separated from detailed work such as parsing, validation, persistence, or formatting

### CC-3. API clarity, parameters, and side effects

Use when call sites or helper APIs feel hard to understand or easy to misuse.

Checklist starters:

- parameter lists stay small and obvious; data clumps become dedicated request/value types when helpful
- flag arguments are avoided in favor of explicit operations or stable variation points
- command/query boundaries are clear enough that readers can tell whether state changes occur
- side effects are explicit rather than hidden behind innocent-looking method names

### CC-4. Duplication, comments, and local readability

Use when the area risks copy-paste drift or uses comments to compensate for unclear code.

Checklist starters:

- duplication is removed by extraction or moving behavior instead of letting parallel code paths drift
- comments explain why, constraints, or trade-offs rather than restating the code line by line
- misleading comments, commented-out code, and other stale local noise are removed
- related code stays close enough together to scan and reason about without jumping through the file unnecessarily

### CC-5. Null handling, exceptions, and boundary hygiene

Use when correctness and maintenance depend on explicit contracts and robust failure handling.

Checklist starters:

- null acceptance and null return contracts are explicit and not left as reader guesswork
- exceptions are specific, informative, and not swallowed or converted into ambiguous fallbacks
- exceptional control flow is kept separate from normal-path logic
- boundary/framework/vendor exceptions are translated at the right layer instead of leaking everywhere

### CC-6. Refactoring safety and test support

Use when local cleanup is risky unless behavior is protected by tests and incremental refactoring seams.

Checklist starters:

- tests are readable and behavior-focused enough to support safe refactoring
- characterization tests exist before risky legacy cleanup where needed
- repeated conditionals, primitive obsession, or long parameter lists are reduced incrementally rather than in one risky rewrite
- state/concurrency assumptions are kept explicit enough that cleanup does not silently break behavior

## Clean Architecture / SOLID / Class Design

### CA-1. Responsibility boundaries and cohesion

Use when a class or cluster may be carrying too many reasons to change.

Checklist starters:

- each class or component owns one coherent responsibility and avoids mixing unrelated change drivers
- validation, orchestration, persistence, transport, formatting, notification, and similar concerns are split when coupling becomes costly
- collaborators remain few enough that the class boundary still feels purposeful and cohesive
- large services/managers are challenged for hidden god-class growth

### CA-2. Extension points and variation handling

Use when new business variations currently require repeated edits across existing code.

Checklist starters:

- stable variation is handled through explicit strategies, policies, handlers, or other clear extension points when that reduces ripple edits
- repeated type checks or parallel switch/if trees for the same concept are treated as design pressure
- adding a new case should not require touching many unrelated classes when the variation is part of the core model
- abstraction is introduced only where the variation is real and recurring, not as ceremony

### CA-3. Contract safety, inheritance, and interface fit

Use when the design relies on inheritance, shared contracts, or broad interfaces.

Checklist starters:

- subtypes preserve caller expectations instead of weakening guarantees or disabling core behavior
- composition is preferred when inheritance is being used only for code reuse
- interfaces stay role-focused instead of forcing consumers to depend on unused operations
- callers should not need `instanceof` checks or `UnsupportedOperationException` to use the abstraction safely

### CA-4. Dependency direction and boundary inversion

Use when high-level logic risks depending directly on framework, transport, persistence, or vendor details.

Checklist starters:

- high-level policy depends on stable abstractions where change safety or testability matters
- infrastructure details plug into the core through seams instead of driving the core design directly
- business logic does not need to construct or understand low-level implementation details unnecessarily
- key behavior stays testable without full framework startup unless that dependency is truly part of the contract

### CA-5. Pragmatic abstraction and change-surface control

Use when the design may have either too little structure or too much ceremony.

Checklist starters:

- abstractions are justified by clarity, replaceability, or reduced coupling rather than habit
- utility dumping grounds, service locators, and other hidden-coupling patterns are challenged
- behavior lives near the data or concept it belongs to when that improves cohesion
- the overall indirection level stays low enough that change paths remain understandable
