# Changelog

## 1.0.0 - Unreleased

First release.

- The `create` skill generates a repository-specific code-review subagent at `.claude/agents/<name>.md` through five phases: discover, interview, confirm, generate, validate.
- Discovery reads the docs, manifest, lint, type and test configs, CI, commit hooks and ignore lists. It detects the host (GitHub or GitLab, and whether `gh` or `glab` is installed) and the base branch (`origin/HEAD`, then the docs, CI targets and recent merges).
- The interview asks only what the repository doesn't settle. It marks inferred values with `(inferred)`, searches the code before proposing a banned pattern, and leaves out options that can't apply.
- Focus areas include correctness, tests, security, type safety (TypeScript or plain JavaScript), performance, accessibility, framework rules, design system, architecture, API contracts, AI and LLM code, and desktop and IPC security.
- The generated agent runs the project's fast checks, using only related tests when the suite is large and writing no files. After its checklist, it reads the change once more without it. Every finding starts with `path:line`. It skips documented known issues, uses the shared severity levels, lists anything it can't confirm under "Open questions", and ends with a verdict.
- Includes a reference template and a Next.js + Supabase sample agent. Evaluated against a plain prompt on a real repository: see `evaluations/desktop-app/README.md`.
