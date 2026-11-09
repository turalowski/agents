---
name: create
description: Generates a code-reviewer subagent tailored to this repository by reading the codebase and interviewing the user. Use when the user asks to set up, create, generate or customise a code review agent or code reviewer.
---

# Create code-reviewer

Generate one Claude Code subagent at `.claude/agents/<name>.md` that reviews code changes in this repository to the user's standards. Work through the five phases in order. Do not skip the interview, and do not write the file before the user confirms the summary.

## Phase 1: Discover

Read what exists so your questions come with good defaults. Read only; never modify anything in this phase.

- `CLAUDE.md`, `AGENTS.md`, `CONTRIBUTING.md`, `README.md`, any `docs/` on conventions or architecture, ADRs.
- The manifest: `package.json`, `pyproject.toml`, `go.mod`, `Cargo.toml` or similar. Note the language, framework and version, and the scripts for lint, typecheck, test and build.
- Monorepo markers: `pnpm-workspace.yaml`, `workspaces` in `package.json`, `turbo.json`, `nx.json`, `go.work`. If present, note each package's own check commands.
- Lint, format and type configs: `eslint.config.*`, `.eslintrc*`, `.prettierrc*`, `biome.json`, `tsconfig.json`, `ruff.toml`, `.golangci.yml`.
- Test setup: `jest.config.*`, `vitest.config.*`, `playwright.config.*`, `cypress.config.*`, `pytest.ini`, plus two or three existing test files to learn the style.
- CI: `.github/workflows/*`, `.gitlab-ci.yml`. CI shows which checks really gate a merge.
- The host: `git remote get-url origin`. GitHub uses pull requests and `gh`; GitLab uses merge requests and `glab`; any other host gets plain git commands. Use the host's word ("pull request" or "merge request") everywhere in the agent.
- The base branch, in this order: `git symbolic-ref refs/remotes/origin/HEAD`; the branch the docs name (`CLAUDE.md`, `CONTRIBUTING.md`, `README.md`); the branch CI runs merge or pull request pipelines against; the most common target in `git log --merges --oneline -50`. Each of these counts as detected; note which source settled it. The base branch is unclear only when these disagree or are all silent. If there is no remote, note it; the agent then falls back to `git diff HEAD`.
- Existing agents in `.claude/agents/`. Avoid name clashes and duplicated responsibilities.
- `git log --oneline -30` for commit conventions, and any `CHANGELOG*` file.

Write down for yourself:

- Stack and framework versions.
- Base branch and remote.
- Exact check commands, split into fast (lint, typecheck, unit tests) and slow (end-to-end, integration against a database).
- Conventions already documented.
- What linters, formatters and the type checker already enforce. The reviewer must not repeat those.
- Generated, vendored or legacy paths.
- Known issues the docs already list (for example a "Known bugs" section), so the agent does not re-report them.
- Whether the test command runs the whole suite, and the runner's related-tests mode if it has one (`jest --findRelatedTests`, `vitest related --run`).

## Phase 2: Interview

Rules:

- At most 4 questions per round and at most 5 rounds in total.
- Use AskUserQuestion when it is available. Each question has 2–4 options, and your detected default comes first, marked "(Recommended)".
- Ask open-ended questions in plain text, numbered, with defaults in brackets, so the user can reply "defaults" or answer by number.
- Skip a question the repository already answers, and state what you detected instead ("Base branch: dev, detected from origin").
- When the code does not settle something clearly (two candidate base branches, two test runners, no clear lint script), ask instead of guessing. Offer your best guess as the recommended option and say what points each way. Never present a guess as detected.
- Mark every value you inferred rather than read from the repository. The Phase 3 summary shows `(inferred)` after it.

### Round 1: Identity and trigger

1. **Name.** Default `code-reviewer`. If that name is taken in `.claude/agents/`, propose `<area>-code-reviewer` (for example `api-code-reviewer`).
2. **When it runs.**
   - Proactively after any non-trivial code change (default)
   - Only when asked
   - Before opening a pull request (a merge request on GitLab)
3. **What it reviews by default.** Files, or a pull or merge request named at call time, are always supported as well.
   - Branch diff against the base branch: `git diff origin/<base>...HEAD` (default)
   - Staged changes: `git diff --staged`
   - Uncommitted changes: `git diff HEAD`
4. **Model.** `inherit` (default), `sonnet` (faster and cheaper), `opus` (deepest review).

The agent is always written to the project (`.claude/agents/`), because its content is specific to this repository.

### Round 2: Focus and strictness

Correctness and edge cases are always checked. Ask the rest as three multi-select questions, pre-selecting the areas that fit the detected stack:

1. **Core:** Tests (missing, weak, or asserting mocks) · Security (auth, input validation, secrets, injection, XSS, SSRF) · Type safety (TypeScript: `any`, unsafe casts; plain JavaScript: JSDoc types that don't match runtime values; both: unchecked external data) · Performance (re-renders, N+1 queries, bundle size, blocking work)
2. **Frontend:** Accessibility (semantics, labels, focus, keyboard, contrast) · Framework rules (for example server vs client components, hooks rules, caching) · Design system (existing components and tokens) · Readability and naming (only what linters cannot catch)
3. **Backend and other:** Architecture (boundaries, coupling, wrong layer) · API contracts (breaking changes, error format, versioning) · AI and LLM code (prompt injection, output sanitising, tool-call validation, cost)

Skip question 2 for repositories without a frontend. Pre-select at most six areas: with more, each checklist gets thinner (see Phase 4).

4. **Strictness.**
   - Balanced: BLOCKER, MAJOR, MINOR and at most 3 NITs (default)
   - Strict: every severity
   - Light: BLOCKER only

### Round 3: Project rules (plain text)

1. **House rules.** Show the conventions found in Phase 1 as a numbered list and ask: "Which of these should the reviewer enforce? Anything to add?" Examples of what users add: "validate all external input with Zod", "no direct DB access from the web app", "every API change needs an integration test", "use our Button, never a raw `<button>`".
2. **Banned patterns.** Anything that must always be flagged? For example `console.log`, `@ts-ignore`, `useEffect` for data fetching, default exports, raw SQL strings. Before proposing a pattern, search the code for it. If the project relies on it somewhere (for example `console.log` routed to a logger in one folder), drop it or propose it with that exception.
3. **Out of scope.** Paths or topics to ignore. Propose the generated, vendored and lockfile paths from Phase 1 as the default.

### Round 4: Output and permissions

1. **Output format.**
   - Findings grouped by severity, each with `file:line`, quoted code, problem, why it matters and fix, then a verdict (default)
   - The same, plus a short "What's good" section
   - Compact checklist only
2. **Permissions.**
   - Read-only, can run fast checks through Bash (default)
   - Strictly read-only, no Bash
   - May also fix nits directly (not recommended: a reviewer that edits is reviewing its own work)
3. **AI-written code scrutiny.** Look harder for patterns common in agent-written code: invented APIs, tests that assert mocks, over-abstraction, swallowed errors, changes outside the task. Default: yes.

Every review ends with a verdict (APPROVE, APPROVE WITH NITS, REQUEST CHANGES); this is not a question.

### Round 5: Follow-ups (only if needed)

Ask only about answers that were unclear or that conflict, for example "strictly read-only" combined with a house rule that requires running tests.

## Phase 3: Confirm

Show this summary before writing anything:

```text
Agent:       <name>  ->  .claude/agents/<name>.md
Runs:        <trigger>
Reviews:     <diff command>, base branch <base>
Checks:      <fast check commands, or "none">
Focus:       <areas>
Strictness:  <level>   AI-code scrutiny: <yes|no>
House rules: <count> rules (listed below)
Banned:      <patterns>
Ignores:     <paths/patterns>
Known issues: <doc and section, or "none">
Output:      <format>, verdict always
Tools:       <tools>   Model: <model>
```

Put `(inferred)` after every value you inferred rather than detected. Then list the house rules in full and ask: "Generate the agent with these settings?" Apply any corrections, show the changed lines again, and continue only after a yes.

## Phase 4: Generate

1. Read [references/agent-template.md](references/agent-template.md). It defines the frontmatter, the section order and the fixed rules (severity, strictness, verdict, boundaries) of the generated agent.
2. Read [references/examples/nextjs-supabase.md](references/examples/nextjs-supabase.md) to calibrate the level of detail. Use it for calibration only; do not copy its rules into an unrelated stack.
3. If `.claude/agents/<name>.md` already exists, show the user what would change and ask before overwriting it.
4. Write the agent to `.claude/agents/<name>.md` following the template. Include only the focus areas the user selected, each with a checklist written for this stack. Write "Server Components by default; flag `'use client'` on components without state, effects or event handlers", not "check React best practices".
5. Budget the body. The fixed sections take about 70 lines, so the checklist, house rules and banned patterns share about 80. Give each focus area the 3–5 rules most likely to catch real bugs in this repository, and never a rule a linter already enforces. If they still don't fit, merge related areas rather than exceed 150 lines.
6. Write documented path patterns with braces, for example `files/{projectId}/{uuid}.docx`. Angle brackets are only for the fill-in slots of the Output template.
7. Never invent rules, commands or paths. Anything not found in the repository or given by the user is left out; if it matters, go back and ask.

## Phase 5: Validate and hand off

1. Frontmatter: valid YAML; `name` matches the filename; fields in the order `name`, `description`, `tools`, `model`; `tools` is one of the presets in the template.
2. Body:
   - Every selected focus area has a stack-specific checklist.
   - Every command named exists in the project (check it against the scripts or the Makefile).
   - No `<...>` placeholder or `TODO` remains outside the `## Output` template. Path patterns use `{...}`.
   - The Boundaries include the uncertainty rule, and the Output has an Open questions section.
   - The body is 80–150 lines.
   - It contains no AI or tool attribution: no "generated by" lines and no model or tool named as an author. The "AI-written code" heading and file names such as `CLAUDE.md` are not attribution.
3. Offer a dry run: "Want me to run it on the current changes to see how it reviews?" If yes, delegate to the new agent by name. If the session does not see it yet, run a general-purpose agent with the file's body as its instructions. Show the report, and if it contains false positives or noise, propose specific edits to the agent file.
4. Hand off in three lines or fewer: where the file is, how to call it ("review my changes", or `@<name>`), and how to change it later (edit the file, or run `/code-reviewer:create` again). Suggest committing the file so the team shares the agent.
