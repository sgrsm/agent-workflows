# Resolver Pack Instantiation Prompt

Use from repository root to create/update a feature-specific resolver pack from this template.
This is template-instantiation only, not a resolver runtime stage. The copied pack need not keep
this file unless the user wants an operator copy.

## Role

Collect required feature/path values, discover applicable repository/module `AGENTS.md` context
files, copy runtime pack files, replace instantiation placeholders, audit the result, and report
blockers. Do not run the resolver workflow.

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

If required values are missing, first derive safe defaults and discover applicable context files,
then ask once in a compact block. Use structured clarification when available; otherwise normal
chat. Accept YAML, bullets, or key/value list.

When presenting assumed/discovered values for confirmation, include the discovered
`projectContextFiles` list so the user can confirm or adjust both repo-wide and module-owned
`AGENTS.md` files.

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
projectContextFiles: "repo-root-relative AGENTS.md list, broadest to most specific; [] allowed"
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

## Project Context File Discovery

Discover context files before the confirmation prompt whenever enough path information is available.
Use explicit filesystem/path checks because `AGENTS.md` may be ignored by Git and by search tools
that respect `.gitignore`.

Algorithm:

1. Determine repository root with `git rev-parse --show-toplevel` when available; otherwise use the
   current working directory as the tentative root and clearly mark that assumption.
2. Build anchor paths from any supplied or safely inferred `targetResolvePackPath`, `sourceDocPath`,
   `reviewReportsDir`, and `runDocsBasePath`. Use each file's parent directory and each directory
   itself. If those paths are not yet known, use the current working directory as a provisional
   anchor, label the discovered list provisional in the confirmation prompt, and rediscover before
   copying once the user supplies paths. If rediscovery materially changes the context list, ask for
   compact confirmation of the changed list before copying.
3. For each anchor, walk from repository root down to the anchor directory and explicitly check for
   `AGENTS.md` in every directory along that chain. Do not rely on broad `find`/`grep` discovery
   alone.
4. Deduplicate and order discovered files broadest to most specific, using repo-root-relative paths.
   Prefer the repo-root `AGENTS.md` plus the deepest applicable module-local `AGENTS.md`; include
   intermediate `AGENTS.md` files if they exist and apply to the path chain.
5. Render confirmed `projectContextFiles` as a Markdown bullet list for the
   `<project_context_files>` placeholder, for example:
   ```md
   - `AGENTS.md`
   - `path/to/module/AGENTS.md`
   ```
   If no applicable files exist, render exactly `- none`.
6. If the user provides an explicit `projectContextFiles` list, validate each listed file with an
   explicit path check and preserve the user's ordering unless it is clearly not broadest-to-most
   specific; ask before reordering materially.

## Instantiation Procedure

1. Read source template `README.md`, `workflow.md`, and `naming-and-placeholders.md`.
2. Confirm source template path, target pack path, and discovered/confirmed `projectContextFiles`.
   If target exists, ask whether to abort, overwrite selected files, or update in place.
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
   - `<project_context_files>` -> confirmed `projectContextFiles` rendered as a Markdown bullet
     list, or `- none`
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
- copied files contain the confirmed Project context files list and no `<project_context_files>`
  placeholder remains; if the list is not `- none`, every listed repo-root-relative path exists and
  is readable via explicit path checks
- `sourceDocPath` exists and has `## Current Resolution State` plus at least one
  `Resolution status: pending` or `Confirmation status: pending`
- `reviewReportsDir` exists and matches source-report refs expected by `SOURCE_DOC`
- policy/prompt refs point to `targetResolvePackPath`, not source template pack
- no stale feature names, slugs, branch examples, paths, or source-doc refs remain in active files;
  if `staleFeatureTermsToCheck` exists, search each term
- copied files still describe the intended resolver-pack scope and do not broaden
  source-doc schema

If a check fails, fix safely or report blocker. Do not guess values.

## Completion Report

Reply briefly with: target path; files/dirs created or updated; confirmed Project context files;
`SOURCE_DOC`/`REVIEW_REPORTS_DIR` audit result; unresolved placeholders/stale terms; ready for
orchestrated use, manual use, or blocked; user decisions still needed. Do not paste generated file
contents.
