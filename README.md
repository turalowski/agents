# agents

Claude Code agent kits. Each kit is a plugin with a generator skill: it reads your repository, interviews you, and writes a subagent tailored to your project into `.claude/agents/`.

## Install

Add the marketplace once:

```text
/plugin marketplace add turalowski/agents
```

Then install the kits you want:

```text
/plugin install <kit>@turalowski-agents
```

Run a kit's generator in your project with `/<kit>:create`, answer the questions, and commit the generated agent so your team shares it.

## Kits

| Kit | Generates | Run |
|---|---|---|
| [code-reviewer](plugins/code-reviewer/README.md) | A code-review subagent that checks changes against your stack, house rules and checks, and ends with a verdict | `/code-reviewer:create` |

## Contributing

Every kit follows [REQUIREMENTS.md](REQUIREMENTS.md).
