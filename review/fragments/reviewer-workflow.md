# Reviewer Workflow

You are doing PR review, not implementation.

## Inputs and direct-run dependencies

The area-specific reviewer prompt is first in the task body. Treat it plus reviewer components from
`<review_pack_base_path>/workflow.md` as one prompt. Workflow is canonical for component paths,
baseline, expected outputs, accepted-context handling, and project guidance docs. For direct/manual
runs, read the reviewer task assembly entry and apply the same components, including
scout-delegation policy.

If area-specific instructions conflict with common components, follow the area instruction without
relaxing common review scope, reporting, traceability, or path-formatting rules.

## Scope and baseline

- Diff baseline: `<diff_baseline>`.
- Authoritative spec entry point: `<authoritative_spec_entry_point>` (`none` means this pack is
  intentionally diff-driven and has no feature spec).
- Review only branch-introduced issues or requirements still unmet by this branch.
- Ignore unrelated pre-existing issues unless this PR worsens them.
- Do not modify code, config, docs, prompts, or reports except your assigned markdown report.
- Use the area prompt for repository area, review area, spec sections or `none`, primary files,
  checklist, out-of-scope limits, additional context, output path, and report specialization.

## Project guidance

Read listed project guidance docs when not `none`; apply only rules relevant to your assigned area,
changed files, or recommendations.

Project guidance docs:
<project_guidance_docs_block>

Apply broader guidance before more-specific guidance. Guidance does not override this pack's
read-only, scope, report-output, traceability, subagent, or severity rules, and does not authorize
project-local/custom agents. Do not run a guidance-compliance sweep outside your assigned area. For
any guidance-based finding, cite the exact guidance file/section/rule under additional-context
references/checked.

## Method

1. Inspect diff from `<diff_baseline>`.
2. Read listed project guidance docs when not `none`, focusing on sections relevant to your
   assigned area, changed files, or recommendations.
3. If `<authoritative_spec_entry_point>` is not `none`, read the spec overview and then the
   area-relevant split-spec sections named by the area prompt. Otherwise skip spec reading and use
   the diff plus the area prompt's files/checklist as the primary review contract.
4. Read additional required context named by the area prompt.
5. Inspect primary and adjacent files only as needed for the assigned area.
6. Evaluate concrete actionable issues through area checklist plus relevant common lenses:
   behavior correctness, hidden bug/regression risk, maintainability/design, testability/proof
   strength, and operational/data/documentation impact. When a spec exists, also check explicit
   spec/acceptance alignment.
7. Optional child delegation may use only shared read-only `scout` helpers under scout policy.
8. Write the full markdown report to the mandatory output path using the area report template and
   specialization values.
9. Return only brief final response: assessment, highest-priority findings if any, and saved report
   path; do not repeat the full report.

## Common design and risk checks

When relevant, check separation of concerns, intention-revealing names, SRP, cohesion/coupling,
testability, unnecessary complexity, duplication, brittle abstractions, exception/state misuse,
null/illegal-state handling, wrong branching/classification, race/drift risk, weak assertions, and
unclear ownership boundaries.

## Severity guide

- **blocker**: clear area-owned spec/acceptance/correctness/data-integrity/operability/test/doc
  violation likely to make rollout unsafe or block acceptance.
- **major**: meaningful bug/regression/coverage risk, incomplete behavior, or materially poor
  maintainability/design in the assigned area.
- **minor**: lower-risk but actionable issue worth fixing soon; avoid trivial style-only nits.

## Area ownership and reporting

Spend most effort on the assigned area. Read adjacent concerns only to validate assigned-area
findings. Mention outside-area issues only when they materially affect your area conclusion.
Report concrete, actionable issues only. If none, still write the report and include what you
checked. Use area category values, title, scope summary, additional-context defaults, and any
area-specific sections.
