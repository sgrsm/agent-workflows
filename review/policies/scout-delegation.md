# Scout Delegation Policy

Shared scope means shared/user-level agents only; never repository-local/project-local/custom agents.
If the harness selector is named `agentScope`, choose the value that enforces shared scope.

- Child delegation is optional. If shared `scout` is unavailable, skip it rather than substituting another role.
- Use only shared-scope `scout` helpers; do not use `planner`, `reviewer`, `worker`, or other helpers.
- Maximum: 3 child subagents total.
- Keep child work strictly within your assigned review area.
- Child helpers must be read-only and non-delegating: do not ask them to modify files, write reports, or spawn subagents.
- If a child task evaluates local style, test design, or maintainability conventions, include the
  relevant project guidance doc paths or quoted rules in that child task.
- Do not read sibling reviewer prompt/report files or ask children to read them.
- Share only context needed inside your own reviewer/child tree; do not share context, findings, or tasks with other top-level reviewers or their child trees.
- Only you may write the assigned reviewer report; you remain responsible for synthesizing child outputs.
