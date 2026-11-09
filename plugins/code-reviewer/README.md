# code-reviewer

Generates a code-review subagent tailored to your repository. The generator reads your codebase (stack, scripts, lint and test setup, CI, conventions), asks you a few rounds of questions, and writes `.claude/agents/code-reviewer.md` into your project. The agent then reviews your changes against your own standards: real check commands, your base branch, your house rules.

## What the generated agent does

- Reviews the branch diff (or staged or uncommitted changes, or files and PRs you name).
- Reads the changed files in full, plus their callers and the code they call.
- Runs your fast checks (lint, typecheck, unit tests) and reports failures.
- Checks the focus areas you chose, with rules written for your stack.
- Reports findings by severity (BLOCKER, MAJOR, MINOR, NIT), each with `file:line`, quoted code and a fix, and ends with a verdict: APPROVE, APPROVE WITH NITS or REQUEST CHANGES.
- Lists anything it cannot confirm from the code under "Open questions" instead of guessing.
- It never edits files, unless you allow it to fix nits.

## Install

```text
/plugin marketplace add turalowski/agents
/plugin install code-reviewer@turalowski-agents
```

To install it automatically for everyone who opens your project, commit this to `.claude/settings.json`:

```json
{
  "extraKnownMarketplaces": {
    "turalowski-agents": {
      "source": { "source": "github", "repo": "turalowski/agents" }
    }
  },
  "enabledPlugins": { "code-reviewer@turalowski-agents": true }
}
```

## Generate your reviewer

In your project, run:

```text
/code-reviewer:create
```

Or ask in plain words: "set up a code reviewer for this repo". Answer the questions, confirm the summary, then commit `.claude/agents/code-reviewer.md` so your team shares the agent.

To use it, say "review my changes", or mention the agent with `@code-reviewer`. If you chose the proactive trigger, Claude also runs it after code changes on its own.

## Interview options

| Option | Choices | Default |
|---|---|---|
| Name | any lowercase, hyphenated name | `code-reviewer` |
| When it runs | proactively · before a PR · only when asked | proactively |
| What it reviews | branch diff · staged · uncommitted | branch diff against the detected base branch |
| Model | inherit · sonnet · opus | inherit |
| Focus areas | correctness (always), tests, security, type safety, performance, accessibility, framework rules, design system, readability, architecture, API contracts, AI/LLM code | chosen from your stack |
| Strictness | light (BLOCKER only) · balanced · strict (everything) | balanced |
| House rules, banned patterns, ignored paths | free text | proposed from your docs and configs |
| Output | grouped by severity · plus "What's good" · compact checklist | grouped by severity |
| Permissions | read-only with checks · strictly read-only · may fix nits | read-only with checks |
| AI-written code scrutiny | yes · no | yes |

## Update

- **The plugin:** `/plugin marketplace update turalowski-agents`. See [CHANGELOG.md](CHANGELOG.md) for what changed.
- **Your agent:** edit `.claude/agents/code-reviewer.md` directly, or run `/code-reviewer:create` again. The generator asks before overwriting an existing agent. Updating the plugin never changes your agent.
