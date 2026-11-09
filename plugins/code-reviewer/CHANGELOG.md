# Changelog

## 1.3.0 - 2026-11-09

Fixes from the re-run (`evaluations/desktop-app/README.md`, section 7.5).

- Severity levels refined: BLOCKER is a security issue, data loss, a crash on normal input, a broken contract or a failing check; wrong behaviour in edge cases is MAJOR.
- The generator checks that `gh` or `glab` is installed and falls back to plain git; reads commit hooks, linter ignore lists and `.gitignore`; and leaves out options and bans that can't apply to the repository.
- Base-branch detection treats a missing `origin/HEAD` as silent, and counts CI as disagreeing only when it names a different merge or pull request target.
- Real code in angle brackets (JSX, generics) no longer counts as a placeholder, and CLI flags of an installed runner count as real commands.
- Related-test commands turn off coverage and report files, so the reviewer writes nothing.
- New focus area: Desktop and IPC security (Electron `nodeIntegration` and `contextIsolation`, IPC as a trust boundary, shell commands, renderer-supplied paths), and an optional security-model sentence in the role paragraph.

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
