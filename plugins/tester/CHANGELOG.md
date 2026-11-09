# Changelog

## 1.0.0 - 2026-11-09

- First release: the `create` skill generates a repository-specific test-writing subagent at `.claude/agents/<name>.md` through a five-phase flow (discover, interview, confirm, generate, validate).
- Generated agents edit test code only, prove each new test fails on the base commit in a temporary git worktree, and list anything they cannot confirm under "Open questions".
- Reference template for the generated agent and two samples: Next.js + Supabase, and FastAPI + PostgreSQL.
