# tester

Generates a test-writing subagent tailored to your repository. The generator studies how your project already tests: frameworks, file layout, naming, fixtures, factories, mocking tools, and the commands for one file and for the whole suite. It then asks you a few rounds of questions and writes `.claude/agents/tester.md` into your project. The agent writes tests for your changes that look like the tests your team already writes.

## What the generated agent does

- Covers the branch diff (or staged or uncommitted changes, or files, functions and PRs you name).
- Lists the behaviours your change adds or alters, and writes tests for the main path, edge cases and error paths. For bug fixes, it writes a reproduction test.
- Runs each new test file on its own, then the related suite.
- Proves each new test fails without your change, by running it against the base commit in a temporary git worktree. Your working tree is never touched by this check.
- Edits test code only. It never changes production code, configuration or dependencies, and never weakens or skips existing tests to get a pass.
- Reports bugs it finds in production code with a severity, instead of fixing them.
- Lists anything it cannot confirm from the code under "Open questions" instead of guessing, because a test that locks in a guess protects the wrong behaviour.

## Install

```text
/plugin marketplace add turalowski/agents
/plugin install tester@turalowski-agents
```

To install it automatically for everyone who opens your project, commit this to `.claude/settings.json`:

```json
{
  "extraKnownMarketplaces": {
    "turalowski-agents": {
      "source": { "source": "github", "repo": "turalowski/agents" }
    }
  },
  "enabledPlugins": { "tester@turalowski-agents": true }
}
```

## Generate your tester

In your project, run:

```text
/tester:create
```

Or ask in plain words: "set up a testing agent for this repo". Answer the questions, confirm the summary, then commit `.claude/agents/tester.md` so your team shares the agent.

To use it, say "write tests for my changes", or mention the agent with `@tester`. It works well after [code-reviewer](../code-reviewer/README.md): hand it the reviewer's "missing tests" findings.

## Interview options

| Option | Choices | Default |
|---|---|---|
| Name | any lowercase, hyphenated name | `tester` |
| When it runs | only when asked · proactively after logic changes · before a PR | only when asked |
| What it covers | branch diff · staged · uncommitted | branch diff against the detected base branch |
| Model | inherit · sonnet · opus | inherit |
| Test layers | unit, component, integration, end-to-end | the layers your repo has, without end-to-end |
| Mocking | system boundaries only · follow existing tests · real database | system boundaries only |
| Proof | fail-on-base check in a temporary worktree · off | on |
| Coverage | none · project threshold · a percentage for changed lines | none |
| Support code | helpers but no dependencies · neither · helpers and proposed dependencies | helpers but no dependencies |
| AI-written test scrutiny | yes · no | yes |
| House rules, banned patterns, ignored paths | free text | proposed from your tests and docs |

## Update

- **The plugin:** `/plugin marketplace update turalowski-agents`. See [CHANGELOG.md](CHANGELOG.md) for what changed.
- **Your agent:** edit `.claude/agents/tester.md` directly, or run `/tester:create` again. The generator asks before overwriting an existing agent. Updating the plugin never changes your agent.
