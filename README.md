# Minipowers

Minipowers is a concise, deliberately lean Codex development workflow designed for GPT-5.6 and packaged as a single skill with a configurable set of specialized subagents. It uses a Socratic design interview, implementation planning with specification and plan review gates, TDD-based implementation with code review gates, safeguards against scope expansion during worker/reviewer remediation loops, and a debugging workflow that can reopen affected tasks after completion.

## Install for one project

- Copy `skills/minipowers/` to `<project>/.agents/skills/minipowers/`.
- Copy the contents of `agents/` to `<project>/.codex/agents/`.

## Install globally

- Copy `skills/minipowers/` to `~/.agents/skills/minipowers/`.
- Copy the contents of `agents/` to `~/.codex/agents/`.

The custom `worker` and `explorer` definitions take precedence over Codex's built-in agents with the same names in the scope where they are installed.

## Use

Start a new Codex task in the target project and invoke the skill explicitly:

```text
Use $minipowers for this development task.
```

Implicit invocation is disabled by default to avoid triggering the workflow for trivial tasks. This setting can be configured in `/skills/minipowers/agents/openai.yaml`.
