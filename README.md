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

| Kit                                              | Generates                                                                                                      | Run                     |
| ------------------------------------------------ | -------------------------------------------------------------------------------------------------------------- | ----------------------- |
| [code-reviewer](plugins/code-reviewer/README.md) | A code-review subagent that checks changes against your stack, house rules and checks, and ends with a verdict | `/code-reviewer:create` |
| [tester](plugins/tester/README.md)               | A test-writing subagent that adds tests in your project's style and proves each one fails without the change   | `/tester:create`        |

## Does it make a difference?

I tested both kits on a real Electron + React repository, comparing a plain prompt with the generated agent on the same uncommitted change. The change was a small feature with 11 planted flaws: SQL injection, a swallowed error that reports success, boundary bugs, missing tests, and two deliberately ambiguous behaviours that the agent should ask about, not guess. The full report is in [evaluations/desktop-app/README.md](evaluations/desktop-app/README.md).

### Code review

Plain prompt: "Review the uncommitted changes".

|                                                    | Plain prompt | code-reviewer agent  |
| -------------------------------------------------- | ------------ | -------------------- |
| Planted flaws found                                | 11 / 11      | 11 / 11              |
| `file:line` on every finding                       | partly       | **yes, all correct** |
| Ran the project's lint and tests                   | no           | **yes**              |
| Ambiguities listed as open questions, not asserted | no           | **yes**              |
| Fixed severity levels and a verdict                | no           | **yes**              |
| Tokens                                             | 54.5k        | 54.4k                |

On a well-documented repository, a plain prompt already finds a lot, and the agent finds just as much at the same token cost. The difference is consistency: it runs your checks, cites exact lines, uses stable severities, gives a verdict, and keeps open questions apart from findings.

### Tests

Plain prompt: "Generate tests for uncommitted changes".

|                                                      | Plain prompt                         | tester agent                               |
| ---------------------------------------------------- | ------------------------------------ | ------------------------------------------ |
| Suite after the run                                  | **red: 9 failing tests left behind** | green                                      |
| Guesses about ambiguous behaviour written into tests | **2**                                | 0                                          |
| Bugs found                                           | 5                                    | 4, plus 3 open questions                   |
| Bug-exposing tests                                   | left failing in the suite            | in the report, ready to add                |
| Proof that each test catches the change              | none                                 | **every test fails under a real mutation** |
| Tokens                                               | 57.7k                                | 67.0k                                      |

Here the agent makes a clear difference. The plain prompt wrote more tests, but it left the suite red, and it wrote two guesses about ambiguous behaviour into assertions. A later fix would break those tests. The agent left the suite green, put the bug-exposing tests in its report for you to add after the fixes, asked about the ambiguous behaviour instead of guessing, and proved that each test catches a real change in behaviour.

### Caveats

- One repository and one sample per run, so some differences are noise.
- The agent columns come from the latest evaluation run. A final round of small fixes from that run has not been re-tested.
- Generating an agent costs about 95k tokens, once per repository. After that, each run costs about the same as a plain prompt.

## Contributing

Every kit follows [REQUIREMENTS.md](REQUIREMENTS.md).

Markdown is formatted with Prettier by a pre-commit hook. Enable it once per clone (it needs Node.js):

```text
git config core.hooksPath .githooks
```
