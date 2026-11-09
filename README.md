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
| [tester](plugins/tester/README.md) | A test-writing subagent that adds tests in your project's style and proves each one fails without the change | `/tester:create` |

## Does it make a difference?

We tested both kits on a real Electron + React repository, comparing a plain prompt with the generated agent on the same uncommitted change. The change was a small feature with 11 planted flaws: SQL injection, a swallowed error that reports success, boundary bugs, missing tests, and two deliberately ambiguous behaviours that the agent should ask about, not guess. The full report is in [evaluations/desktop-app/README.md](evaluations/desktop-app/README.md).

### Code review

Plain prompt: "Review the uncommitted changes".

| | Plain prompt | code-reviewer 1.1.0 | code-reviewer 1.2.0 |
|---|---|---|---|
| Planted flaws found | 11 / 11 | 9 / 11 | **11 / 11** |
| `file:line` on every finding | partly | no | **yes, all correct** |
| Ran the project's lint and tests | no | yes | yes |
| Ambiguities listed as open questions, not asserted | no | yes | yes |
| Fixed severity levels and a verdict | no | yes | yes |
| Tokens | 54.5k | 54.0k | 54.4k |

On a well-documented repository, a plain prompt already finds a lot. The first agent version found less than the plain prompt, because its checklist narrowed its attention. Version 1.2.0 adds a second read without the checklist, and now finds just as much at the same token cost. On top of that, it runs your checks, uses stable severities, gives a verdict, and keeps open questions apart from findings.

### Tests

Plain prompt: "Generate tests for uncommitted changes".

| | Plain prompt | tester 1.0.0 | tester 1.1.0 |
|---|---|---|---|
| Suite after the run | **red: 9 failing tests left behind** | green | green |
| Guesses about ambiguous behaviour written into tests | **2** | 0 | 0 |
| Bugs found | 5 | 6 | 4, plus 3 open questions |
| Bug-exposing tests | left failing in the suite | in the report, ready to add | in the report, ready to add |
| Proof that each test catches the change | none | weak (code missing on base) | **every test fails under a real mutation** |
| Tokens | 57.7k | 66.1k | 67.0k |

Here the agent makes a clear difference. The plain prompt wrote more tests, but it left the suite red, and it wrote two guesses about ambiguous behaviour into assertions. A later fix would break those tests. The agent left the suite green, put the bug-exposing tests in its report for you to add after the fixes, asked about the ambiguous behaviour instead of guessing, and proved that each test catches a real change in behaviour.

### Caveats

- One repository and one sample per run, so some differences are noise.
- Each run in the tables used the kit version shown. Later versions add fixes from the [re-run](evaluations/desktop-app/README.md#75-remaining-fixes) that have not been re-evaluated yet.
- Generating an agent costs about 95k tokens, once per repository. After that, each run costs about the same as a plain prompt.

## Contributing

Every kit follows [REQUIREMENTS.md](REQUIREMENTS.md).
