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
- `code_reviewer`: reviews implementation compliance, correctness, maintainability, test quality, security, and computational efficiency.

Do not substitute generic subagents or perform required reviews in the parent thread. The parent coordinates the workflow, integrates findings, updates artifacts, commits completed tasks, and communicates with the user.

## 1. Subagent Configuration

Immediately present these defaults and ask whether the user wants changes:

- `worker`: GPT-5.6 Sol, high effort
- `explorer`: GPT-5.6 Terra, medium effort
- `spec_reviewer`: GPT-5.6 Sol, extra high effort
- `plan_reviewer`: GPT-5.6 Sol, extra high effort
- `code_reviewer`: GPT-5.6 Sol, extra high effort

**Do not begin design until the user confirms the subagent configuration.**

## 2. Design

1. Inspect the relevant codebase and documentation, using `explorer` where useful.
2. Conduct a thorough design interview until you and the user reach a shared understanding:
   - Explore the codebase instead of asking the user any question that the codebase can answer.
   - Walk every material branch of the decision tree, resolving prerequisite decisions before dependent decisions.
   - Ask exactly one question at a time and wait for the user's answer before continuing.
   - With each question, provide a recommended answer and concise rationale.
   - Continue until every material aspect of the design is resolved; do not stop after the first workable approach.
3. Summarize the agreed decisions and verify that no material ambiguity remains. Resume the one-question-at-a-time interview if any does.
4. Write the proposed design specification as Markdown under `docs/`.
5. Delegate review to `spec_reviewer`.
6. Address its findings and repeat review until it returns `PASS`.
7. Present the reviewed specification for explicit user approval.

The approved design specification is the source of truth for intended behavior and scope.

**Stop here until the user explicitly approves the design specification. Do not create an implementation plan or modify production code.**

## 3. Implementation Plan

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

Each task must later pass a single implementation review gate performed by `code_reviewer`.

Before presenting the plan:

1. Delegate review to `plan_reviewer`.
2. Address its findings and repeat review until it returns `PASS`.
3. Present the reviewed plan for explicit user approval.

**Stop here until the user explicitly approves the implementation plan. Do not begin implementation.**

## 4. Implementation

Use the approved design specification and implementation plan as the sources of truth. If they conflict, pause and ask the user rather than resolving the conflict silently.

Execute tasks sequentially by default. Parallelize implementation only when tasks are independent and affect non-overlapping areas.

For each task:

1. Before starting the task, retire every subagent used for the previous implementation task. Do not reuse a previous task's `worker`, `code_reviewer`, or other task-scoped subagent; start fresh subagents for the new task.
2. Delegate implementation to a fresh `worker`.
3. Require red/green/refactor and relevant validation.
4. Delegate the implementation review to a fresh `code_reviewer`.
5. Return findings to `worker`; repeat review until `code_reviewer` returns `PASS`.
6. Mark the task complete.
7. Create a separate commit containing only that completed task.

Reviewers report findings and do not modify code. `worker` must not review its own work. Use `explorer` for targeted, read-only investigation when useful.

If implementation requires a material deviation from the approved design:

1. Pause the affected task.
2. Explain the proposed deviation and consequences.
3. Request explicit user approval.
4. Update the design specification and implementation plan where appropriate.
5. Resume only after approval.

Do not silently expand scope, alter approved behavior, or bypass a review gate.

## 5. Completion

After all tasks pass the code review gate:

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
