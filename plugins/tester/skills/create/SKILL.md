---
name: create
description: Generates a tester subagent tailored to this repository by reading the codebase and interviewing the user. Use when the user asks to set up, create, generate or customise a testing agent, test writer or tester.
---

# Create tester

Generate one Claude Code subagent at `.claude/agents/<name>.md` that writes and updates tests for code changes in this repository's own test style, and proves each new test checks real behaviour. Work through the five phases in order. Do not skip the interview, and do not write the file before the user confirms the summary.

## Phase 1: Discover

Read what exists so your questions come with good defaults. Read only; never modify anything in this phase.

- `CLAUDE.md`, `AGENTS.md`, `CONTRIBUTING.md`, `README.md`, and any `docs/` on testing or conventions.
- The manifest: `package.json`, `pyproject.toml`, `go.mod`, `Cargo.toml` or similar. Note the language, framework and version, and every test-related script.
- Monorepo markers: `pnpm-workspace.yaml`, `workspaces` in `package.json`, `turbo.json`, `nx.json`, `go.work`. If present, note each package's own test commands.
- Test configuration for each layer: `vitest.config.*`, `jest.config.*`, `playwright.config.*`, `cypress.config.*`, `pytest.ini`, `[tool.pytest.ini_options]` in `pyproject.toml`, `conftest.py`, `go test` flags in the Makefile.
- Where test files live and how they are named: co-located `*.test.ts`, `__tests__/`, `tests/` mirroring the source tree, `test_*.py`, `*_test.go`.
- Three to five existing tests per layer (unit, component, integration, end-to-end). Note their structure, test naming, assertion style and setup.
- Shared support code: setup files, fixtures, factories, builders, custom matchers, render helpers, and mock layers such as MSW, `respx`, test containers or a test database.
- How tests handle time, randomness and network access (fake timers, `time-machine`, seeded random, blocked network).
- Coverage configuration and thresholds, flaky or quarantine markers, and how CI runs tests (`.github/workflows/*`, `.gitlab-ci.yml`).
- The base branch: `git symbolic-ref refs/remotes/origin/HEAD`, else `git branch -r`. If there is no remote, note it.
- Existing agents in `.claude/agents/`. Avoid name clashes, and note whether a `code-reviewer` exists so the hand-off can mention it.

Write down for yourself:

- The exact command to run one test file, and the command to run the whole fast suite. Both must be real.
- The test layers present, and which are slow or stateful (end-to-end, browser, real services).
- The conventions for location, naming, structure and support code.
- What the proof step needs to run tests in a separate worktree, for example a symlinked `node_modules` or the project's existing virtualenv.
- Generated, vendored and legacy paths.

## Phase 2: Interview

Rules:

- At most 4 questions per round and at most 5 rounds in total.
- Use AskUserQuestion when it is available. Each question has 2–4 options, and your detected default comes first, marked "(Recommended)".
- Ask open-ended questions in plain text, numbered, with defaults in brackets, so the user can reply "defaults" or answer by number.
- Skip a question the repository already answers, and state what you detected instead ("Single-file command: `npx vitest run <file>`, from package.json").
- When the code does not settle something clearly (two test runners, no obvious single-file command, tests in two different layouts), ask instead of guessing. Offer your best guess as the recommended option and say what points each way. Never present a guess as detected.

### Round 1: Identity and scope

1. **Name.** Default `tester`. If that name is taken in `.claude/agents/`, propose `<area>-tester` (for example `api-tester`).
2. **When it runs.**
   - Only when asked (default)
   - Proactively after changes that add or change logic
   - Before opening a pull request
3. **What it covers by default.** Files, functions or a PR named at call time are always supported as well.
   - Branch diff against the base branch: `git diff origin/<base>...HEAD` (default)
   - Staged changes: `git diff --staged`
   - Uncommitted changes: `git diff HEAD`
4. **Model.** `inherit` (default), `sonnet` (faster and cheaper), `opus` (deepest analysis).

### Round 2: How it tests

1. **Test layers it may write** (multi-select): unit · component · integration · end-to-end. Pre-select the layers the repository already has, except end-to-end, which is slow and needs a running app.
2. **Mocking policy.**
   - Mock only at system boundaries: network, time, randomness, third-party services (default)
   - Follow the existing tests exactly
   - No mocks for the database; use real instances through the project's test setup
3. **Proof that tests work.**
   - Check that each new test fails on the base commit, in a temporary git worktree (default)
   - Skip the check
4. **Coverage.**
   - No target (default)
   - Meet the project's configured threshold
   - A percentage for changed lines (ask for the number)

### Round 3: Limits

1. **Support code.**
   - May add test helpers and fixtures, never dependencies (default)
   - May add neither
   - May add helpers, and propose new test dependencies in the report without installing them
2. **AI-written test scrutiny.** Look harder at its own tests for assertions that always pass, asserting a mock's own return value, invented matchers or APIs, and duplicated tests. Default: yes.

### Round 4: Project rules (plain text)

1. **House rules.** Show the testing conventions found in Phase 1 as a numbered list and ask: "Which of these should the tester follow? Anything to add?" Examples: "use the factories in `tests/factories/`", "no snapshot tests for logic", "every API route has an integration test".
2. **Banned patterns.** Propose the defaults from [references/agent-template.md](references/agent-template.md), plus anything the project already forbids, and ask what to add or remove.
3. **Out of scope.** Paths to ignore. Propose the generated, vendored and legacy paths from Phase 1.

### Round 5: Follow-ups (only if needed)

Ask only about answers that were unclear or that conflict, for example "no mocks for the database" in a repository with no test database setup.

## Phase 3: Confirm

Show this summary before writing anything:

```text
Agent:       <name>  ->  .claude/agents/<name>.md
Runs:        <trigger>
Covers:      <diff command>, base branch <base>
Commands:    one file: <single-file command>   suite: <suite command>
Layers:      <layers>
Mocking:     <policy>
Proof:       <worktree check | off>   Coverage: <target>
Support:     <support code policy>
AI scrutiny: <yes|no>
House rules: <count> rules (listed below)
Banned:      <patterns>
Ignores:     <paths/patterns>
Tools:       Read, Grep, Glob, Bash, Edit, Write   Model: <model>
```

Then list the house rules in full and ask: "Generate the agent with these settings?" Apply any corrections, show the changed lines again, and continue only after a yes.

## Phase 4: Generate

1. Read [references/agent-template.md](references/agent-template.md). It defines the frontmatter, the section order and the fixed rules of the generated agent.
2. Read one sample agent to calibrate the level of detail. Use [references/examples/nextjs-supabase.md](references/examples/nextjs-supabase.md) for JavaScript or TypeScript projects, and [references/examples/fastapi-postgres.md](references/examples/fastapi-postgres.md) for Python. For any other stack, read both, and note what changes between them (framework, layout, mocking tools) and what stays the same. Never copy a sample's rules into an unrelated stack.
3. If `.claude/agents/<name>.md` already exists, show the user what would change and ask before overwriting it.
4. Write the agent to `.claude/agents/<name>.md` following the template. Every convention must come from the existing tests: write "Component tests use `renderWithProviders` from `tests/utils/render.tsx`", not "use a render helper".
5. Never invent rules, commands, paths or helpers. Anything not found in the repository or given by the user is left out; if it matters, go back and ask.

## Phase 5: Validate and hand off

1. Frontmatter: valid YAML; `name` matches the filename; fields in the order `name`, `description`, `tools`, `model`; `tools` is `Read, Grep, Glob, Bash, Edit, Write`.
2. Body:
   - Every command named exists in the project (check it against the scripts, Makefile or config).
   - Run the single-file command on one existing test file to confirm it works.
   - The proof commands match the project's setup (dependencies available in the worktree).
   - No `<...>` placeholder or `TODO` remains outside the `## Output` template.
   - The Boundaries include the uncertainty rule, and the Output has an Open questions section.
   - The body is 80–150 lines and contains no AI or tool attribution.
3. Offer a dry run, and say that it writes test files into the working tree: "Want me to run it on the current changes? It will add or update test files, which you can review with `git diff`." If yes, delegate to the new agent by name. If the session does not see it yet, run a general-purpose agent with the file's body as its instructions. Show the report, and if the tests or report contain noise, propose specific edits to the agent file.
4. Hand off in three lines or fewer: where the file is, how to call it ("write tests for my changes", or `@<name>`), and how to change it later (edit the file, or run `/tester:create` again). Suggest committing the file so the team shares the agent. If a code reviewer agent exists, mention that its "missing tests" findings can be handed to the tester.
