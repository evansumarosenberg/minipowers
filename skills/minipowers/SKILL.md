---
name: minipowers
description: Run the user-approved design, planning, subagent implementation, review, commit, and completion workflow for a non-trivial repository development task.
---

# Minipowers

This skill governs the coordinating parent thread only. Do not ask subagents to invoke or follow this skill, and do not include its full contents in delegated prompts. Give each subagent only its bounded assignment and the relevant source-of-truth files.

Use these project-scoped custom subagents from `.codex/agents/`:

- `worker`: implements an approved task.
- `explorer`: performs targeted, read-only codebase investigation.
- `spec_reviewer`: reviews a proposed design specification.
- `plan_reviewer`: reviews a proposed implementation plan.
- `compliance_reviewer`: checks implementation against the approved specification and plan.
- `quality_reviewer`: reviews correctness, maintainability, and test quality after compliance passes.

Do not substitute generic subagents or perform required reviews in the parent thread. The parent coordinates the workflow, integrates findings, updates artifacts, commits completed tasks, and communicates with the user.

## 1. Design

1. Inspect the relevant codebase and documentation, using `explorer` where useful.
2. Brainstorm viable approaches with the user and resolve material ambiguities.
3. Write the proposed design specification as Markdown under `docs/`.
4. Delegate review to `spec_reviewer`.
5. Address its findings and repeat review until it returns `PASS`.
6. Present the reviewed specification for explicit user approval.

The approved design specification is the source of truth for intended behavior and scope.

**Stop here until the user explicitly approves the design specification. Do not create an implementation plan or modify production code.**

## 2. Implementation Plan

After design approval, write an implementation plan as Markdown under `docs/`.

Organize it into focused, independently reviewable tasks suitable for `worker`. Each task must identify:

- Scope and expected outcome
- Relevant files or components
- Required tests
- Dependencies
- Acceptance criteria

Every task must use test-driven development:

1. **Red:** Add or update tests that fail for the expected reason.
2. **Green:** Implement the minimum change needed to pass.
3. **Refactor:** Improve the implementation while keeping tests green.

Each task must later pass two independent implementation review gates:

1. `compliance_reviewer`
2. `quality_reviewer`

Before presenting the plan:

1. Delegate review to `plan_reviewer`.
2. Address its findings and repeat review until it returns `PASS`.
3. Present the reviewed plan for explicit user approval.

**Stop here until the user explicitly approves the implementation plan. Do not begin implementation.**

## 3. Implementation Configuration

After plan approval, present these defaults and ask whether the user wants changes:

- `worker`: GPT-5.6 Sol, medium effort
- `compliance_reviewer`: GPT-5.6 Sol, high effort
- `quality_reviewer`: GPT-5.6 Sol, high effort

The checked-in configurations for `spec_reviewer` and `plan_reviewer` apply during design and planning.

**Do not begin implementation until the user confirms the implementation-stage configuration.**

## 4. Implementation

Use the approved design specification and implementation plan as the sources of truth. If they conflict, pause and ask the user rather than resolving the conflict silently.

Execute tasks sequentially by default. Parallelize implementation only when tasks are independent and affect non-overlapping areas.

For each task:

1. Delegate implementation to `worker`.
2. Require red/green/refactor and relevant validation.
3. Delegate compliance review to `compliance_reviewer`.
4. Return findings to `worker`; repeat until the reviewer returns `PASS`.
5. Delegate quality review to `quality_reviewer`.
6. Return findings to `worker`; repeat until the reviewer returns `PASS`.
7. Mark the task complete.
8. Create a separate commit containing only that completed task.

Reviewers report findings and do not modify code. `worker` must not review its own work. Use `explorer` for targeted, read-only investigation when useful.

If implementation requires a material deviation from the approved design:

1. Pause the affected task.
2. Explain the proposed deviation and consequences.
3. Request explicit user approval.
4. Update the design specification and implementation plan where appropriate.
5. Resume only after approval.

Do not silently expand scope, alter approved behavior, or bypass a review gate.

## 5. Completion

After all tasks pass both review gates:

1. Run the full relevant test and validation suite.
2. Confirm the implementation satisfies the approved design.
3. Review documentation, including `README.md`, for stale or inaccurate information and update it where appropriate.
4. Clean up temporary files, debugging artifacts, worktrees, and generated resources that should not remain.
5. Shut down temporary processes, including development servers and watchers.
6. Commit any tracked documentation or cleanup changes.
7. Provide a final summary covering:
   - What was implemented
   - Tests and validation performed
   - Documentation updated
   - Commits created
   - Approved deviations
   - Remaining limitations or follow-up work

If final validation reveals a defect, reopen the affected task and repeat its implementation and review gates before completing the workflow.
