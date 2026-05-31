# Review Pack Instantiation Prompt

Use from repository root to create/update a feature-specific review pack from this template.
This is template-instantiation only, not a review runtime stage. The copied pack need not keep
this file unless the user wants an operator copy.

## Role

Collect core pack/path values, inspect the authoritative feature spec when available or the feature
branch diff when it is not, suggest a reasonable reviewer-area plan plus non-test focus-checklist
categories, let the user accept, edit, remove, or add areas and checklist choices, create concrete
reviewer prompts from the approved area list, replace instantiation placeholders, audit the result,
and report blockers. Do not run the review workflow.

## Safety Rules

- Source template = directory containing this file unless user supplies another path.
- Do not modify the source template pack.
- Do not overwrite/merge into an existing target pack unless user clearly confirms path and action.
- Do not create review artifacts such as reviewer reports, consolidated reports, or final-evaluation
  output files.
- Do not run the orchestrator, reviewer prompts, builds, tests, formatters, package managers, or
  validation scripts.
- Do not stage, commit, branch, reset, or clean unless user explicitly asks after diff review.
- Protect unrelated work; ask if target files contain user changes or action is ambiguous.
- Discovery during instantiation is for reviewer-area planning only. Do not perform the actual code
  review, do not write findings, and do not over-read unrelated code.
- Do not finalize reviewer areas silently. The user must get a chance to accept, discard, rename,
  merge, split, reorder, or add custom areas before files are created.

## Inputs

If required values are missing, ask once in a compact block. Use structured clarification when
available; otherwise normal chat. Accept YAML, bullets, or key/value list.

### Core values to collect first

```yaml
featureName:
featureSlug:
targetReviewPackPath:
reviewScope:
documentationReviewScope:
diffBaseline: "default: main...HEAD unless user overrides"
authoritativeSpecEntryPoint: "repo-root-relative path or none"
specificationBasePath: "repo-root-relative path, derive from spec entry point if possible, or none"
reportsBasePath:
consolidatedReportOutputPath: "default suggestion: <reportsBasePath>/99-consolidated-report.md"
finalEvaluationOutputPath: "default suggestion: <reportsBasePath>/100-final-evaluation.md"
acceptedContextDocs: []
reviewThemes: "auto | explicit list"
```

Use repo-root-relative paths everywhere. `acceptedContextDocs` items may be given as:

```yaml
acceptedContextDocs:
  - label:
    path:
```

or as a plain list of paths. If `authoritativeSpecEntryPoint` is `none`, then
`specificationBasePath` should also be `none`.

If the user omits synthesized output filenames but `reportsBasePath` is known, suggest
`99-consolidated-report.md` and `100-final-evaluation.md` under `reportsBasePath` unless the user
explicitly wants different names.

### Optional values

```yaml
sourceTemplatePackPath: "default: directory containing this file"
copyInstantiatePrompt: false
copyNamingAndPlaceholdersGuide: false
copyFocusChecklistCatalog: false
keepSourceReviewerTemplates: false
staleFeatureTermsToCheck: []
cleanOutputGateFilesOverride: []
reviewAreaHints: []
manualReviewerDefinitions: []
documentationReviewerPreference: "ask | create | skip"
documentationReviewerReportFilenamePreference: "auto | none | <filename>.md"
focusChecklistSelectionPreference: "ask | category_approval | auto_select_items | manual"
crossCuttingQualityReviewerPreference: "ask | combined | split | clean_code_only | solid_only | none"
```

Notes:

- `reviewAreaHints` = optional user-supplied area ideas to seed or bias suggestions.
- `manualReviewerDefinitions` = optional fully specified reviewer configs. If supplied and clearly
  complete, you may skip the suggestion phase or use it only as a comparison aid.
- `documentationReviewerPreference` = optional starting preference for whether to create a
  dedicated documentation reviewer:
  - `ask` = ask in a short named-option choice block whether to create one
  - `create` = include one dedicated documentation reviewer
  - `skip` = do not create one; this is the default recommendation when the user has not decided
- `documentationReviewerReportFilenamePreference` = only relevant if a dedicated documentation
  reviewer is approved:
  - `auto` = derive or suggest a concrete documentation-review report filename
  - `none` = no dedicated documentation reviewer report filename
  - `<filename>.md` = use the supplied filename
  If the documentation reviewer is skipped, resolve this to `none`.
- `focusChecklistSelectionPreference` = optional starting preference for non-test reviewer focus
  checklists:
  - `ask` = suggest category bundles and ask the user how to resolve them
  - `category_approval` = first show the suggested category bundles per reviewer and let the user
    accept/edit them; only after that approval expand those approved bundles into final checklist
    bullets
  - `auto_select_items` = skip category-bundle approval and directly choose the final concrete
    checklist bullets from the catalog plus the spec/diff
  - `manual` = user provides explicit checklist text
- `crossCuttingQualityReviewerPreference` = optional starting preference for a dedicated
  cross-cutting Java maintainability/design reviewer when meaningful non-test Java code is in
  scope:
  - `ask` = ask in a separate short named-option choice block
  - `combined` = one dedicated reviewer covering both Clean Code and SOLID
  - `split` = two dedicated reviewers: one Clean Code and one SOLID
  - `clean_code_only` = one dedicated Clean Code reviewer only
  - `solid_only` = one dedicated SOLID reviewer only
  - `none` = no dedicated Clean Code or SOLID reviewer
- Recommend `combined` for most Java feature reviews when a dedicated cross-cutting reviewer is
  desired; use `split` mainly for large, refactor-heavy, or architecture-heavy diffs where one
  report would otherwise become too broad.
- A tests/proof-strength reviewer is default-on. Propose and create it unless the user explicitly
  rejects/removes it.
- Use the source template's `focus-checklist-catalog.md` as the template-only source for sanitized
  non-test focus-checklist categories and checklist starters.
- If `cleanOutputGateFilesOverride` is omitted, derive the clean-output gate from all reviewer
  `expectedReport` paths plus `consolidatedReportOutputPath` and `finalEvaluationOutputPath`.
- For default synthesized artifact naming, prefer `99-consolidated-report.md` and
  `100-final-evaluation.md` under `reportsBasePath`.

## Review-Area Discovery And Approval Flow

Unless the user already supplied complete `manualReviewerDefinitions`, discover and propose review
areas before creating files.

### 1. Discover reviewer areas

#### Preferred path: authoritative spec available

If `authoritativeSpecEntryPoint` is not `none`:

1. Verify the spec entry point exists.
2. Determine `specificationBasePath` if omitted and it can be safely derived.
3. Read the entry-point spec and any clearly relevant sibling spec files under
   `specificationBasePath` as needed to identify major responsibilities, flows, integrations,
   persistence concerns, runtime/operability requirements, configuration/rollout concerns,
   documentation obligations, and testing/proof expectations.
4. Inspect a compact diff summary and changed-file list for `<diff_baseline>` inside `reviewScope`
   and `documentationReviewScope` to ground the suggested areas in actual branch changes.
5. Draft reviewer areas primarily from the spec structure and requirements, using the diff to tune
   filenames, prompt names, report names, and scope boundaries.
6. Read the source template's `focus-checklist-catalog.md` and prepare initial focus-checklist
   category suggestions for each non-test reviewer area.

#### Fallback path: no authoritative spec

If `authoritativeSpecEntryPoint` is `none`:

1. Inspect the branch diff against `<diff_baseline>`.
2. Group changed files into coherent review concerns based on code/package boundaries, data/schema
   changes, configuration/runtime wiring, external integration points, tests, and documentation.
3. Suggest reviewer areas from the diff shape and changed responsibilities, not from generic boilerplate.
4. Use `none` for spec-specific reviewer blocks unless the user later points to a spec.
5. Read the source template's `focus-checklist-catalog.md` and prepare initial focus-checklist
   category suggestions for each non-test reviewer area.

### 2. Build a draft reviewer plan

Draft only the areas that appear justified by the spec and/or diff, with one exception: always
include a tests / proof-strength reviewer by default unless the user explicitly rejects it. Avoid
empty ceremonial areas otherwise. A typical draft may include some subset of:

- business flow / validation / orchestration
- external integration / resilience / error classification
- persistence / data integrity / schema compatibility
- runtime / operability / logging / startup validation
- tests / proof strength / test design
- documentation / cross-doc consistency
- rollout / migration / backfill safety
- configuration / packaging / deployment wiring
- API / contract compatibility

For each draft area, prepare a compact proposal with at least:

```yaml
- id: "01"
  reviewer:
  template: area | tests
  promptFile:
  expectedReport:
  rationale:
    - spec sections and/or diff clusters that justify the area
  likelyPrimaryFiles:
    - repo-root-relative path
  suggestedFocusChecklistCategories:
    - <catalog category id and label>
  notes:
```

Use `reviewAreaHints` if provided. Treat a dedicated documentation reviewer as optional and
default-off. If `documentationReviewScope` is non-empty or the spec/diff surfaces documentation
obligations, prepare a separate documentation-reviewer decision for approval:

- `skip` = do not create a dedicated documentation reviewer; this is the proposed default
- `create` = create one dedicated documentation reviewer

If the user later approves a dedicated documentation reviewer and
`documentationReviewerReportFilenamePreference` is `auto` or omitted, suggest a concrete report
filename. If the documentation reviewer is skipped, resolve the documentation reviewer report
filename to `none`. For the default tests reviewer, use concrete prompt filename
`prompts/reviewers/tests-reviewer.md` unless the user explicitly overrides the filename or rejects
the reviewer.

Also prepare a separate cross-cutting reviewer decision for approval with these possible
outcomes:

- `combined` = one combined Clean Code + SOLID reviewer; this is the proposed default for typical
  Java-heavy review scope
- `split` = two separate reviewers: one Clean Code and one SOLID
- `clean_code_only` = one Clean Code reviewer only
- `solid_only` = one SOLID reviewer only
- `none` = no dedicated Clean Code or SOLID reviewer

If there is little or no meaningful changed non-test Java code, propose `none` by default unless
the user explicitly wants a broader cross-cutting design pass. When you recommend anything other
than `none`, also propose concrete reviewer labels, prompt filenames, expected report filenames,
likely primary files, and suggested `CC-*` / `CA-*` categories from
`focus-checklist-catalog.md`. Do not silently add these reviewers; they require explicit user
approval.

If `reviewThemes` is `auto`, also draft a short theme list from the proposed areas. Keep theme
selection inside the first reviewer-plan approval choice block together with the reviewer-area
selection. The user should be able to accept the proposed themes, edit/reorder them, or replace
them with a custom list as part of that first decision. If `reviewThemes` was already supplied as
an explicit list, present that list as the proposed one and still allow confirm/edit unless the
user already explicitly finalized it.

For non-test reviewers, suggest specific focus-checklist category ids from
`focus-checklist-catalog.md`. Do not silently expand them into the final prompt text until the user
has chosen one of these paths globally or per reviewer:

- accept/edit category suggestions, then let the instantiator expand them into concrete checklist bullets
- let the instantiator auto-select concrete checklist bullets from the catalog plus the spec/diff
- provide manual checklist text

For tests reviewers, remember that the source template already contains default checklist content;
`focusChecklistBlock` is only for feature-specific additions and may stay `none`.

### 3. Ask the user to approve or adjust the plan

Before creating files, show the draft reviewer plan **together with the proposed review themes** in
a compact block and ask the user to choose one of these paths for that first reviewer-area/themes
decision:

- accept all suggested areas and themes
- accept with edits/removals/reordering/renames/theme edits
- add one or more custom areas
- replace the draft with a fully manual reviewer definition list

The user must be able to discard suggested areas and add a custom area even if the spec/diff did
not surface it. The user must also be able to edit, reorder, or replace the proposed review themes
in that same first decision. If the user adds a custom area with only a short description, derive
the missing prompt fields when safe and ask follow-up only for truly missing high-impact details.

Also ask how focus checklists should be resolved for non-test reviewers when that is not already
clear from `focusChecklistSelectionPreference`. At minimum, let the user:

- accept/edit the suggested category ids per reviewer
- tell the instantiator to auto-select concrete checklist items from the spec/diff
- provide manual checklist text for one or more reviewers

When presenting that checklist-resolution decision in a short named-option choice block, make the
first two options clearly non-equivalent. Use labels/descriptions equivalent to:

- Review and approve suggested category bundles first — the user confirms or edits the suggested
  `BF-*` / `IR-*` / `PD-*` / `RT-*` / `CC-*` / `CA-*` sets first, then the instantiator expands
  only those approved bundles into final checklist bullets
- Let the instantiator pick final checklist bullets directly — skip category-bundle approval and
  derive the final checklist bullets straight from the spec, diff, and catalog
- Edit suggested category bundles first — the user wants to change the suggested bundles before
  approving them
- Provide manual checklist text — the user will supply checklist wording directly

Do not label the first two options in a way that makes them sound like the same behavior.

Use at least three separate short named-option choice blocks in this minimum order, though
additional clarifications are allowed whenever needed:

1. Reviewer areas + review themes approval — this existing first decision block.
2. Clean Code / SOLID decision — second separate choice block. Offer exactly:
   - One combined Clean Code + SOLID reviewer (default)
   - Two separate Clean Code and SOLID reviewers
   - Clean Code only reviewer
   - SOLID only reviewer
   - No Clean Code, no SOLID
   If `crossCuttingQualityReviewerPreference` was already supplied as `combined`, `split`,
   `clean_code_only`, `solid_only`, or `none`, show that as the proposed choice but still allow the
   user to edit it. If the user chooses more than one cross-cutting reviewer, keep their scopes
   distinct enough to reduce duplicate findings.
3. Documentation reviewer decision — third separate choice block. Offer:
   - Skip dedicated documentation reviewer (default)
   - Create dedicated documentation reviewer
   If `documentationReviewerPreference` was already supplied as `create` or `skip`, show that as
   the proposed choice but still allow the user to edit it. If the user chooses to create the
   reviewer and `documentationReviewerReportFilenamePreference` is `auto` or omitted, suggest a
   concrete filename; if the user skips it, resolve the documentation reviewer report filename to
   `none`.

Do not merge the Clean Code / SOLID or documentation decisions into the first reviewer-area/themes
approval block. Acceptance of reviewer areas and themes does not count as acceptance of those later
separate decisions.

If focus-checklist resolution or any other high-impact detail is still unresolved after or between
those required choice blocks, ask additional follow-up questions as needed; the three blocks above
are a minimum, not a maximum.

Silence is not rejection: do not drop the default tests reviewer unless the user explicitly says to
remove or skip it. Also do not create a dedicated documentation reviewer unless the user explicitly
chooses to create it. Do not invent a Clean Code / SOLID reviewer shape silently; ask the separate
second choice block unless the user already supplied one of the recognized preferences.

Do not create files until the user has approved the reviewer areas/themes block, answered the Clean
Code / SOLID choice, answered the documentation reviewer choice, and resolved any remaining
focus-checklist handling or other high-impact follow-ups.

### 4. Materialize full reviewer definitions

After approval, convert the final reviewer list into full reviewer definitions for instantiation.
For each reviewer area, derive or confirm:

```yaml
id:
reviewer:
template: area | tests
promptFile:
expectedReport:
repositoryArea:
reviewArea:
reportTitle:
scopeSummary:
findingCategories:
additionalContextDefaultsLine:
additionalContextCheckedDefaultsLine:
additionalRequiredContextBlock:
relevantSpecSectionsBlock:
primaryFilesBlock:
focusChecklistBlock:
outOfScopeBlock:
specialReportSectionsBlock: "tests only; extra feature-specific pre-findings sections; use none to keep template defaults"
testGapRoiGateBlock: "tests only; extra feature-specific addendum or stricter gate; use none to keep template defaults"
```

Derivation rules:

- `relevantSpecSectionsBlock` comes from inspected spec sections when a spec exists; otherwise use
  `none`.
- `primaryFilesBlock` comes from the main files/directories that justified the area.
- `focusChecklistBlock` should reflect the responsibilities implied by the spec and/or changed code,
  not generic filler. For non-test reviewers, derive it from approved category ids in
  `focus-checklist-catalog.md`, from auto-selected catalog items grounded in the spec/diff, or from
  explicit user-provided text. Do not dump raw category lists into the prompt; rewrite them into a
  concise concrete checklist. For `tests` reviewers, treat it as feature-specific additions beyond
  the source template's built-in default checklist and use `none` when no additions are needed.
- `outOfScopeBlock` should separate adjacent reviewer responsibilities cleanly. For `tests`
  reviewers, treat it as feature-specific exclusions beyond the source template's built-in default
  boundaries and use `none` when no additions are needed.
- `additionalRequiredContextBlock` should include only the accepted context docs relevant to that
  area, otherwise `none`.
- For `tests` reviewers, keep the dedicated ROI/practicality gate and the default Coverage Matrix /
  optional Info pre-findings sections from the source template unless the user wants extra or
  stricter customization. Use `testGapRoiGateBlock` and `specialReportSectionsBlock` only for
  feature-specific addenda/replacements, and use `none` when the template defaults are sufficient.
- For the default auto-generated tests reviewer, use prompt file
  `prompts/reviewers/tests-reviewer.md` unless the user explicitly overrides the filename or rejects
  the reviewer.
- A dedicated documentation reviewer, if approved, uses `template: area`.
- For a dedicated documentation reviewer, use prompt file
  `prompts/reviewers/documentation-reviewer.md`. If
  `documentationReviewerReportFilenamePreference` is `auto` or omitted, use an expected report like
  `NN-documentation-review.md`; if the reviewer is skipped, resolve the documentation reviewer
  report filename to `none`.
- Keep a dedicated documentation reviewer scoped to documentation follow-up, cross-doc consistency,
  and implementation-alignment checks rather than a full code review.
- Dedicated Clean Code / SOLID reviewers, if approved, use `template: area`.
- Recommended default shape is one combined reviewer using prompt file
  `prompts/reviewers/clean-code-solid-reviewer.md` and an expected report like
  `NN-clean-code-solid-review.md`.
- If the user chose `split`, use two area reviewers:
  - `prompts/reviewers/clean-code-reviewer.md` for `CC-*` local maintainability concerns
  - `prompts/reviewers/solid-reviewer.md` for `CA-*` SOLID / class-design concerns
- If the user chose `clean_code_only`, use one area reviewer with prompt file
  `prompts/reviewers/clean-code-reviewer.md` and an expected report like
  `NN-clean-code-review.md`.
- If the user chose `solid_only`, use one area reviewer with prompt file
  `prompts/reviewers/solid-reviewer.md` and an expected report like
  `NN-solid-review.md`.
- If the user chose `none`, keep Clean Code / SOLID concerns as area-reviewer checklist overlays
  only.
- Keep dedicated Clean Code / SOLID reviewers scoped to changed non-test Java code and
  maintainability/design quality. Do not let them absorb business-flow correctness, integration
  semantics, persistence correctness, runtime operability, documentation accuracy, or
  tests/proof-strength obligations already assigned to other reviewers.
- For the combined reviewer, blend concrete `CC-*` and `CA-*` items into one concise checklist. For
  separate reviewers, keep `CC-*` items in the clean-code reviewer and `CA-*` items in the SOLID
  reviewer.
- For dedicated Clean Code / SOLID reviewers, `primaryFilesBlock` should usually point to changed
  production Java files or the dominant Java packages in scope, not the entire repo or docs/tests
  trees. Use `relevantSpecSectionsBlock` only when the spec materially defines architectural
  boundaries relevant to the design critique; otherwise use `none`.
- If there is little or no changed non-test Java code, recommend `none` unless the user explicitly
  wants a broader cross-cutting design pass.
- If the user supplied `manualReviewerDefinitions`, prefer them over auto-derived material.

## Instantiation Procedure

1. Read source template `README.md`, `workflow.md`, `naming-and-placeholders.md`,
   `focus-checklist-catalog.md`, `templates/area-report.md`,
   `templates/consolidated-report.md`, `templates/final-evaluation.md`, and reviewer source
   templates under `prompts/reviewers/`.
2. Confirm source template path and target pack path. If target exists, ask whether to abort,
   overwrite selected files, or update in place.
3. Collect missing core values.
4. Run the review-area discovery and approval flow above unless complete manual reviewer definitions
   were already supplied and accepted. Ensure the final approved reviewer list still contains a
   tests/proof-strength reviewer unless the user explicitly rejected it. Use at least three
   separate short named-option choice blocks in this minimum order: reviewer areas + review themes,
   Clean Code / SOLID, documentation reviewer. Do not collapse the second or third decisions into
   the first approval block, and do not treat acceptance of reviewer areas/themes as acceptance of
   the later separate decisions. Additional follow-up questions are allowed when needed.
5. Copy shared runtime files by default: `README.md`, `workflow.md`, `orchestrator.md`,
   `fragments/`, `policies/`, `templates/`, `prompts/consolidator.md`, and `prompts/verifier.md`.
6. Create one concrete reviewer prompt per approved reviewer entry by copying:
   - `prompts/reviewers/area-reviewer.md` when `template` is `area`
   - `prompts/reviewers/tests-reviewer.md` when `template` is `tests`
7. Copy source reviewer templates only when `keepSourceReviewerTemplates` is true or explicit. If
   the concrete default tests reviewer already uses `prompts/reviewers/tests-reviewer.md`, do not
   keep a second helper copy at that same target path unless you intentionally rename it and update
   local references accordingly. Copy template helpers only when requested: `instantiate.md` if
   `copyInstantiatePrompt` is true or explicit; `naming-and-placeholders.md` if
   `copyNamingAndPlaceholdersGuide` is true or explicit; `focus-checklist-catalog.md` if
   `copyFocusChecklistCatalog` is true or explicit.
8. Replace these shared placeholders in copied shared files, policies, prompts, and templates:
   - `<feature_name>` -> `featureName`
   - `<feature_slug>` -> `featureSlug`
   - `<review_pack_base_path>` -> `targetReviewPackPath`
   - `<review_scope>` -> `reviewScope`
   - `<documentation_review_scope>` -> `documentationReviewScope`
   - `<diff_baseline>` -> `diffBaseline`
   - `<authoritative_spec_entry_point>` -> `authoritativeSpecEntryPoint`
   - `<specification_base_path>` -> `specificationBasePath`
   - `<reports_base_path>` -> `reportsBasePath`
   - `<consolidated_report_output_path>` -> `consolidatedReportOutputPath`
   - `<final_evaluation_output_path>` -> `finalEvaluationOutputPath`
   - `<documentation_reviewer_report_filename>` -> resolved documentation reviewer report filename
9. Build and replace workflow block placeholders:
   - `<accepted_context_docs_block>` from `acceptedContextDocs` or `- none`
   - `<reviewer_rows_block>` from the approved reviewer list using role/profile `reviewer`, the
     target pack's shared area-report template path, and each reviewer's concrete prompt/report
     paths
   - `<clean_output_gate_files_block>` from `cleanOutputGateFilesOverride`, otherwise from reviewer
     expected reports plus consolidated/final outputs
10. If `reviewThemes` is `auto`, derive the theme headings from the approved reviewer plan. Update
    `templates/consolidated-report.md` theme sections to match the final theme list: replace
    existing theme placeholders, remove unused starter sections, and add extra theme sections if
    the requested list is longer than the template starter set.
11. For each concrete reviewer prompt:
    - replace `<report_output_path>`, `<repository_area>`, `<review_area>`, `<report_title>`,
      `<scope_summary>`, `<finding_categories>`, `<additional_context_defaults_line>`,
      `<additional_context_checked_defaults_line>`, `<additional_required_context_block>`,
      `<relevant_spec_sections_block>`, `<primary_files_block>`, `<focus_checklist_block>`,
      `<out_of_scope_block>`
    - for `tests` prompts only, also replace `<special_report_sections_block>` and
      `<test_gap_roi_gate_block>`
    - rewrite the H1 to a responsibility-based title using the reviewer row's `reviewer` value
    - remove template-only copy/rename wording so the copied prompt reads as a concrete direct/manual
      entry point
12. Rewrite copied `README.md` so it reads as the instantiated feature-local review pack rather
    than the source template guide. Keep paths and workflow semantics accurate. At minimum include:
    - a canonical-path summary with the instantiated review-pack, spec, scope, and report paths
    - the concrete reviewer prompt/report table from the approved reviewer list
    - accepted context docs when any were approved
    - a copy/paste-ready minimal orchestrated-run prompt that points to the concrete
      `orchestrator.md` under `targetReviewPackPath`
    - copy/paste-ready minimal direct/manual single-agent prompt examples that use real
      instantiated paths: one standalone area-review example using an actual concrete reviewer
      prompt file from the final reviewer list, one consolidation example for
      `prompts/consolidator.md`, and one per-finding verification example for
      `prompts/verifier.md` that tells the operator to paste one full consolidated finding block in
      the same request
    Do not leave "choose a reviewer prompt" placeholders in those examples; use real local paths.
    If `keepSourceReviewerTemplates` is false, remove or rewrite any local references to
    `prompts/reviewers/area-reviewer.md` and `prompts/reviewers/tests-reviewer.md` so the README
    does not point at absent local helper files.
13. In copied `templates/area-report.md`, replace shared instantiation placeholders such as
    `<diff_baseline>` and `<specification_base_path>`. Keep the shared report template's neutral
    report-path wording rather than inserting one reviewer-specific output path.
14. Keep runtime report/verification placeholders such as `<finding title>`, `<finding count>`,
    `<verification verdict>`, `<decisive evidence>`, and `<resolution options>` where required.

## Tool-Assisted Audit

Audit with normal file/search tools only; do not add/run embedded validation scripts.

Checks:

- target pack has expected shared files/directories and all requested concrete reviewer prompts
- no instantiation placeholders remain in active target files, except optional copied helper files
  or intentionally retained unreferenced source reviewer templates
- copied `README.md` describes the instantiated pack, includes copy/paste-ready minimal prompt
  examples for orchestrated use and direct/manual entry points using actual instantiated paths, and
  does not reference absent local source reviewer templates when those helpers were not copied
- `workflow.md` reviewer rows point only to concrete copied reviewer prompts, not
  `prompts/reviewers/area-reviewer.md` or `prompts/reviewers/tests-reviewer.md`
- reviewer expected report paths are unique and clean-output-gate files match workflow outputs
- if the documentation reviewer was skipped, resolved documentation reviewer report filename is
  `none` and no active documentation-reviewer row/prompt is left behind unintentionally
- if the documentation reviewer was approved, its prompt/report paths exist, its resolved report
  filename matches one reviewer's expected report filename, and its focus/out-of-scope blocks keep
  it documentation-scoped
- accepted-context paths exist when specified
- if a spec-backed pack was requested, `authoritativeSpecEntryPoint` and `specificationBasePath`
  exist; if a diff-only pack was requested, both are set to literal `none`
- policy/prompt/template refs point to `targetReviewPackPath`, not the source template pack
- consolidated-report theme sections match the final theme list; no `<theme_*>` placeholders remain
- non-test reviewer `focusChecklistBlock` content is concrete, area-specific, and does not leak raw
  catalog-only helper prose or unresolved category placeholders into active prompts
- if the user approved one or more dedicated Clean Code / SOLID reviewers, their prompt/report
  paths exist and their focus/out-of-scope blocks keep them maintainability/design-scoped rather
  than duplicating another reviewer's full correctness contract
- no stale feature names, slugs, old reviewer prompt/report paths, or source-template refs remain in
  active files; if `staleFeatureTermsToCheck` exists, search each term
- copied files still describe the intended review-pack scope and do not broaden reviewer
  scope or output behavior beyond the template contract

If a check fails, fix safely or report the blocker. Do not guess values.

## Completion Report

Reply briefly with: target path; discovery basis used (spec-backed or diff-only); reviewer areas
suggested and then created; whether a dedicated documentation reviewer was proposed and created;
which Clean Code / SOLID reviewer choice was proposed and created; focus-checklist selection
approach used; shared files/dirs created or updated; path-existence audit result for
spec/context/reports locations; unresolved placeholders or stale terms; ready for orchestrated use,
direct/manual use, or blocked; user decisions still needed. Do not paste generated file contents.
