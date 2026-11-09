# Changelog

## 1.2.0 - 2026-11-09

Fixes from the first real-repository evaluation (`evaluations/desktop-app/README.md`).

- GitLab support: the generator detects the host, and the agent uses "merge request" and `glab mr diff` (or plain git) there.
- Base-branch detection checks `origin/HEAD`, then the docs, CI config and recent merge targets before asking.
- The generator searches the code before proposing a banned pattern, marks inferred values with `(inferred)` in the summary, and records documented known issues so the agent doesn't re-report them.
- The agent runs only the tests related to changed files when the test command runs the whole suite.
- New procedure step: a second read without the checklist, for unhandled input, duplicated behaviour and name collisions. Findings must start with `path:line` taken from the diff.
- Line budget guidance (3–5 rules per focus area, at most six areas pre-selected), a Type safety variant for plain JavaScript, path patterns written with `{...}`, and a precise definition of attribution.

## 1.1.0 - 2026-11-09

- The generator asks instead of guessing when the repository does not clearly settle something, and never invents rules, commands or paths.
- Generated agents list anything they cannot confirm under a new "Open questions" output section instead of reporting it as a finding. Open questions do not change the verdict.

## 1.0.0 - 2026-11-08

- First release: the `create` skill generates a repository-specific code-review subagent at `.claude/agents/<name>.md` through a five-phase flow (discover, interview, confirm, generate, validate).
- Reference template for the generated agent and a Next.js + Supabase example.
