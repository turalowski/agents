# Changelog

## 1.1.0 - 2026-11-09

- The generator asks instead of guessing when the repository does not clearly settle something, and never invents rules, commands or paths.
- Generated agents list anything they cannot confirm under a new "Open questions" output section instead of reporting it as a finding. Open questions do not change the verdict.

## 1.0.0 - 2026-11-08

- First release: the `create` skill generates a repository-specific code-review subagent at `.claude/agents/<name>.md` through a five-phase flow (discover, interview, confirm, generate, validate).
- Reference template for the generated agent and a Next.js + Supabase example.
