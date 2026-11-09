# Changelog

## 1.2.0 - 2026-11-09

Fixes from the re-run (`evaluations/desktop-app/README.md`, section 7.5).

- Severity levels refined: BLOCKER is a security issue, data loss, a crash on normal input, a broken contract or a failing check; wrong behaviour in edge cases is MAJOR.
- The generator checks that `gh` or `glab` is installed and falls back to plain git; reads commit hooks, linter ignore lists and `.gitignore`; and leaves out options and bans that can't apply to the repository.
- Base-branch detection treats a missing `origin/HEAD` as silent, and counts CI as disagreeing only when it names a different merge or pull request target.
- Real code in angle brackets (JSX, generics) no longer counts as a placeholder, and CLI flags of an installed runner count as real commands.
- Every reported bug starts with `path:line`, taken from `grep -n`.
- Without slow suites, the "Do not run" boundary is removed.

## 1.1.0 - 2026-11-09

Fixes from the first real-repository evaluation (`evaluations/desktop-app/README.md`).

- Proof for new code: a test that fails on the base only because the code doesn't exist there now needs a mutation proof, or is reported as not applicable.
- Single-file and proof commands turn off forced coverage. Proof commands find dependencies through `git rev-parse --show-toplevel` instead of `$PWD`.
- Discovery flags when existing tests mock collaborators, and then recommends "Follow the existing tests".
- Naming and location rules can differ per area. An optional lint step for new tests when CI lints them.
- Bug severities are spelled out, so security issues are BLOCKERs. The agent never asserts documented known issues as expected behaviour.
- GitLab support, base-branch detection from docs, CI config and merge history, banned-pattern search before proposing, `(inferred)` markers, `{...}` path patterns, and a precise definition of attribution (shared with code-reviewer 1.2.0).

## 1.0.0 - 2026-11-09

- First release: the `create` skill generates a repository-specific test-writing subagent at `.claude/agents/<name>.md` through a five-phase flow (discover, interview, confirm, generate, validate).
- Generated agents edit test code only, prove each new test fails on the base commit in a temporary git worktree, and list anything they cannot confirm under "Open questions".
- Reference template for the generated agent and two samples: Next.js + Supabase, and FastAPI + PostgreSQL.
