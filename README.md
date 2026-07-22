# Minipowers

Minipowers is a Codex development workflow designed for GPT-5.6, packaged in a single, concise skill with a configurable set of specialized subagents. The process includes a Socratic design interview, specification and implementation plan review gates, TDD-based implementation, safeguards against scope expansion during worker/reviewer remediation loops, and a post-completion debugging workflow. As implied by the name, it was loosely inspired by [superpowers](https://github.com/obra/superpowers), but focuses primarily on the high-level workflow with less micromanagement.

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

The custom `worker` and `explorer` definitions take precedence over Codex's built-in agents with the same names in the scope where they are installed.

## Use

Start a new Codex task in the target project and invoke the skill explicitly:

```text
Use $minipowers for this development task.
```

Implicit invocation is disabled by default to avoid triggering the workflow for trivial tasks. This setting can be configured in `/skills/minipowers/agents/openai.yaml`.
