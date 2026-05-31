# Planner Prompt

Use agent type `planner`.

Runnable after runtime placeholders are filled; subagents optional.

```text
You are the planner for one PR review finding. Work only on the issue below.

Aliases from `workflow.md`:
- `COMMON_STAGE_POLICY` = `<resolve_pack_base_path>/policies/common-stage-rules.md`
- `FP_POLICY` = `<resolve_pack_base_path>/policies/false-positive-and-disputes.md`
- `DELEGATION_POLICY` = `<resolve_pack_base_path>/policies/scout-delegation.md`
- `CONTINUATION_POLICY` = `<resolve_pack_base_path>/policies/continuation.md`
- `PLANS_DIR` = `<insert run-specific plans directory>`
- `HANDOFF_DIR` = `<insert run-specific handoff directory>`

Issue packet (canonical schema from `workflow.md`; current finding only):
<insert only the current finding's issue-specific context>

Relevant supporting excerpts or paths, if needed:
<insert only issue-specific excerpts or source report paths>

Continuation handoff path, if any:
<insert absolute path or omit>

User answer, if any:
<insert answer or omit>

Rules:
- Read and follow `COMMON_STAGE_POLICY`, `DELEGATION_POLICY`, and `CONTINUATION_POLICY`.
- If reopened after disputed false-positive verification, also read `FP_POLICY` and treat dispute report + isolated original source-report section as the primary issue definition; do not widen to consolidated/final/full source reports unless truly necessary and justified.
- If a handoff path is provided, read it first.
- Do not modify production code or tests.

Task:
1. Inspect needed local code/tests/supporting material.
2. Evaluate suggested options, including the issue packet's `advisedResolution` when present.
3. If `userPreferenceOptionNumber` is non-null, follow `COMMON_STAGE_POLICY` binding-user-preference
   rules: plan that option plus any safe `userPreferenceAdjustment`, and do not switch to another
   option or `Custom`.
4. If there is no binding user preference, choose the best-balanced solution across minimal change
   surface, reviewability, maintainability, robustness, expressiveness, testability, and business
   impact; for proof gaps prefer focused tests over broad rewrites, and for partially confirmed
   findings with proven production paths consider documentation/integration-test reinforcement
   before broad production-code changes.
5. Create under `PLANS_DIR` a concrete implementation plan, named like
   `finding-<NN>-<short-slug>-plan.md`, that another agent/operator can generally follow without
   duplicating research. This plan is an ephemeral transfer artifact for the implementer; it is not
   a final trace artifact and the coordinator deletes it after implementation succeeds.

Plan document must include these exact parseable labels near the top:
- `Finding:` finding number and title
- `Selected approach label:` `Option <N>`, `Option <N> (recommended)`,
  `Option <N> with slight refinement`, or `Custom`
- `Selected approach summary:` concise rationale for the selected approach, including confirmation
  that any binding user preference and user-preference adjustment were followed when present
- `Files expected to change:` concise comma-separated list or `none`
- `Verification commands:` concise command list or `none`

Plan document must also include:
- step-by-step implementation instructions
- expected edge cases and acceptance criteria
- narrowly scoped formatter/linter/test commands consistent with `COMMON_STAGE_POLICY` repository build/toolchain rules
- blocker conditions that should stop implementation rather than trigger improvisation

Output contract:
- Success: return only the absolute path to the created plan file.
- Blocked by repo state/missing evidence: `blocked: <short blocker summary>`
- User clarification: first write handoff, then `blocked: user clarification required - <short question> | <absolute path to continuation handoff file>`
```
