# Minipowers

Minipowers is a Codex development workflow designed for GPT-5.6, packaged in a single, concise skill with a configurable set of specialized subagents. The process includes a Socratic design interview, specification and plan review gates, test-driven development for production behavior changes, guardrails against scope expansion during implementer/reviewer remediation loops, and a post-completion debugging workflow.

As implied by the name, it was loosely inspired by [superpowers](https://github.com/obra/superpowers), but focuses on the high-level workflow with less micromanagement. Safeguards were also added to prevent GPT 5.6 (especially Sol) from going off the rails and iteratively sneaking in additional complexity that compounds until straightforward tasks become excessively overengineered.

## Automatic project-scoped installation

Copy and paste the following instruction in Codex:

```markdown
Please fetch the latest commit from [minipowers](https://github.com/evansumarosenberg/minipowers), then install the skill and subagent profiles into this project.
```

## Manual project-scoped installation

- Copy `skills/minipowers/` to `<project>/.agents/skills/minipowers/`.
- Copy the contents of `agents/` to `<project>/.codex/agents/`.

## Manual global installation

- Copy `skills/minipowers/` to `~/.agents/skills/minipowers/`.
- Copy the contents of `agents/` to `~/.codex/agents/`.

The specialized `implementer` and `investigator` names avoid overriding Codex's built-in `worker` and `explorer` subagents.

## Customizing subagents

Each file in `agents/` is a subagent profile. You can customize a profile in `.codex/agents/<agent>.toml` by changing its `model` and `model_reasoning_effort` values. For example:

```toml
model = "gpt-5.6-terra"
model_reasoning_effort = "high"

```

By default, design and planning agents use Sol, implementation agents use Terra, and the investigator uses Luna.

## Use

Start a new Codex task in the target project and invoke the skill explicitly:

```text
Use $minipowers for this development task.
```

Implicit invocation is disabled by default to avoid triggering the workflow for trivial tasks. This setting can be configured in `/skills/minipowers/agents/openai.yaml`.
