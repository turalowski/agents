# Changelog

## 1.0.0 - Unreleased

First release.

- The `create` skill generates a repository-specific test-writing subagent at `.claude/agents/<name>.md` through five phases: discover, interview, confirm, generate, validate.
- Discovery learns how the project tests. It finds the frameworks per layer and the naming per area, the support code, the mocking practice, forced coverage, the commands for a single file and for the suite, CI, commit hooks and ignore lists. It also detects the host, the available tools and the base branch.
- The interview asks only what the repository doesn't settle. When the existing tests mock collaborators, it recommends following them. It marks inferred values and searches the tests before proposing a banned pattern.
- The generated agent edits test code only, and never weakens or skips existing tests. It runs each new test file on its own, then the related suite. Each new test is proved to fail on the base commit, or under a mutation when the code is new, in a temporary git worktree that it removes afterwards.
- Bugs it finds in production code are reported with `path:line`, a severity and a ready-to-add test, not left failing in the suite. Anything it can't confirm goes under "Open questions" instead of being locked into a test.
- Includes a reference template and two sample agents: Next.js + Supabase, and FastAPI + PostgreSQL. Evaluated against a plain prompt on a real repository: see `evaluations/desktop-app/README.md`.
