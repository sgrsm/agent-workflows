# Resolver Pack Instantiation Prompt

Use from repository root to create/update a feature-specific resolver pack from this template.
This is template-instantiation only, not a resolver runtime stage. The copied pack need not keep
this file unless the user wants an operator copy.

## Role

Collect required feature/path values, copy runtime pack files, replace instantiation placeholders,
audit the result, and report blockers. Do not run the resolver workflow.

## Safety Rules

- Source template = directory containing this file unless user supplies another path.
- Do not modify the source template pack.
- Do not overwrite/merge into an existing target pack unless user clearly confirms path and action.
- Do not create resolver run artifacts under the target pack.
- Do not modify `SOURCE_DOC`; inspect only enough to verify seeded resolver-ready shape.
- Do not run builds, tests, formatters, package managers, or validation scripts.
- Do not stage, commit, branch, reset, or clean unless user explicitly asks after diff review.
- Protect unrelated work; ask if target files contain user changes or action is ambiguous.

## Required Inputs

If required values are missing, ask once in a compact block. Use structured clarification when
available; otherwise normal chat. Accept YAML, bullets, or key/value list.

Required values:

```yaml
featureName:
featureSlug:
targetResolvePackPath:
sourceDocPath:
reviewReportsDir:
runDocsBasePath:
runIdPattern:
branchNamePattern:
finalReportOutputPathPattern: "must retain literal <RUN_ID>"
```

Optional values:

```yaml
sourceTemplatePackPath: "default: directory containing this file"
copyInstantiatePrompt: false
copyNamingAndPlaceholdersGuide: false
staleFeatureTermsToCheck: []
commitTemplates:
  resolvedFinding: "Resolve #<N><severityPart>: <title>"
  unresolvedFindingArtifact: "Document unresolved #<N><severityPart>: <title>"
  confirmedFalsePositive: "Confirm false positive #<N><severityPart>: <title>"
  disputedFalsePositive: "Dispute false positive #<N><severityPart>: <title>"
  finalResolverSummary: "Document resolver summary"
```

If optional commit templates are omitted, keep the defaults already present in `workflow.md`.

## Instantiation Procedure

1. Read source template `README.md`, `workflow.md`, and `naming-and-placeholders.md`.
2. Confirm source template path and target pack path. If target exists, ask whether to abort,
   overwrite selected files, or update in place.
3. Copy runtime files by default: `README.md`, `workflow.md`, `manual.md`, `orchestrator.md`,
   `policies/`, and `prompts/`.
4. Copy template helpers only when requested: `instantiate.md` if `copyInstantiatePrompt` is true
   or explicit; `naming-and-placeholders.md` if `copyNamingAndPlaceholdersGuide` is true or
   explicit.
5. Replace these instantiation placeholders in copied runtime files and copied helpers:
   - `<feature_name>` -> `featureName`
   - `<feature_slug>` -> `featureSlug`
   - `<resolve_pack_base_path>` -> `targetResolvePackPath`
   - `<source_doc_path>` -> `sourceDocPath`
   - `<review_reports_dir>` -> `reviewReportsDir`
   - `<run_docs_base_path>` -> `runDocsBasePath`
   - `<final_report_output_path>` -> `finalReportOutputPathPattern`
   - `<run_id_pattern>` -> `runIdPattern`
   - `<branch_name_pattern>` -> `branchNamePattern`
6. If commit template overrides exist, update commit-message bullets in copied `workflow.md`;
   otherwise keep defaults.
7. Keep runtime placeholders such as `<RUN_ID>`, `<N>`, `<severityPart>`, and `<title>` where
   required.

## Tool-Assisted Audit

Audit with normal file/search tools only; do not add/run embedded validation scripts.

Checks:

- target pack has expected runtime files/directories from the procedure
- no instantiation placeholders remain in active target files, except explicit template-source notes
- remaining angle-bracket placeholders are runtime or example/schema placeholders allowed by
  `naming-and-placeholders.md`
- `finalReportOutputPathPattern` still contains literal `<RUN_ID>`
- `sourceDocPath` exists and has `## Current Resolution State` plus at least one
  `Resolution status: pending` or `Verification status: pending`
- `reviewReportsDir` exists and matches source-report refs expected by `SOURCE_DOC`
- policy/prompt refs point to `targetResolvePackPath`, not source template pack
- no stale feature names, slugs, branch examples, paths, or source-doc refs remain in active files;
  if `staleFeatureTermsToCheck` exists, search each term
- copied files still describe a service-scoped resolver pack and do not broaden source-doc schema

If a check fails, fix safely or report blocker. Do not guess values.

## Completion Report

Reply briefly with: target path; files/dirs created or updated; `SOURCE_DOC`/`REVIEW_REPORTS_DIR`
audit result; unresolved placeholders/stale terms; ready for orchestrated use, manual use, or
blocked; user decisions still needed. Do not paste generated file contents.
