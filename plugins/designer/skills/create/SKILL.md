---
name: create
description: Generates a designer subagent tailored to this repository by reading the codebase and interviewing the user. Use when the user asks to set up, create, generate or customise a design agent, UI designer or designer.
---

# Create designer

Generate one Claude Code subagent at `.claude/agents/<name>.md` that designs and builds user interface for this repository with shadcn/ui components and the project's design tokens only, in a design direction you settle with the user here. Most generic interfaces come from decisions nobody made, such as the audience, the personality, or a palette left at the library's default. This interview makes those decisions once, as concrete choices, so every screen the agent builds is consistent and recognisable. Work through the five phases in order. Do not skip the interview, and do not write the file before the user confirms the summary.

## Phase 1: Discover

Read what exists so your questions come with good defaults. Read only; never modify anything in this phase.

- `CLAUDE.md`, `AGENTS.md`, `CONTRIBUTING.md`, `README.md`, and any `docs/` on design, brand or UI conventions. Note what the product is and who it serves.
- The manifest (`package.json`): the framework and version, React version, Tailwind version, the lint and typecheck scripts, and UI dependencies (`@radix-ui/*`, `radix-ui`, `lucide-react`, `@tabler/icons-react`, `motion`, `framer-motion`, `next-themes`, `class-variance-authority`, `tailwind-merge`). Note any second UI library (MUI, Chakra, Mantine, Ant Design, daisyUI) and where it is used.
- Monorepo markers: `pnpm-workspace.yaml`, `workspaces`, `turbo.json`, `nx.json`. In a monorepo, find which package holds the UI and whether shadcn lives in a shared package such as `packages/ui`.
- `components.json`. Record `style`, `tailwind.css`, `tailwind.baseColor`, `tailwind.cssVariables`, `iconLibrary`, `aliases` and `registries`. If there is no `components.json`, stop discovery and tell the user that the designer needs shadcn/ui. Offer to run `npx shadcn@latest init`, and run it only after a yes.
- The theme stylesheet named by `tailwind.css`. Record every token defined in `:root` and `.dark` (`--background`, `--foreground`, `--card`, `--popover`, `--primary`, `--secondary`, `--muted`, `--accent`, `--destructive`, `--border`, `--input`, `--ring`, `--chart-1` to `--chart-5`, `--sidebar-*`, `--radius`, and any custom ones). Record the colour format (OKLCH or HSL), and how tokens reach Tailwind: `@theme inline` in Tailwind v4, or `theme.extend.colors` in `tailwind.config.*` for v3.
- Whether the theme is customised. Compare the values with the shadcn defaults for the recorded base colour. If they still match, the theme is the default: colour and type become open questions in Round 2, not detected values.
- Installed components in the `ui` alias folder. For each, check with `git log --oneline -- <file>` whether it was edited after being added, and note edits that change behaviour or styling.
- Fonts and how they load (`next/font`, `@fontsource/*`, `<link>` tags, `@font-face`), and the font tokens (`--font-sans`, `--font-mono`, any display font).
- Dark mode: how it is switched (`next-themes`, a `.dark` class, `prefers-color-scheme` only), and whether `.dark` defines every token that `:root` defines.
- Two or three existing screens, including the most polished one. Note the page structure, the container widths, the spacing rhythm, how headings are sized, and how forms, tables and empty states are built.
- Token bypasses. Search the UI code for raw palette classes (`grep -rEn "(bg|text|border|ring|fill|stroke|from|to|via)-(slate|gray|zinc|neutral|stone|red|orange|amber|yellow|lime|green|emerald|teal|cyan|sky|blue|indigo|violet|purple|fuchsia|pink|rose)-[0-9]{2,3}"`), hex or `rgb()` colours in class names and `style` props, and arbitrary values such as `p-[13px]`. Count them per folder.
- The shadcn MCP server in `.mcp.json`. If it is configured, the agent can use it to search and view components.
- Visual checks the project already has: Storybook, Playwright screenshot tests, Chromatic. Note their commands, and whether they are fast.
- Commit hooks: `.husky/`, `lefthook.yml`, `.pre-commit-config.yaml` and the lint-staged config.
- Ignore candidates: the linter ignore lists and `.gitignore`, plus generated and vendored paths.
- The host: `git remote get-url origin`. GitHub uses pull requests; GitLab uses merge requests. Use the host's word everywhere in the agent.
- Existing agents in `.claude/agents/`. Avoid name clashes, and note whether a `code-reviewer` or `tester` exists so the hand-off can mention it.

Write down for yourself:

- The exact lint and typecheck commands, and the command for adding a shadcn component with the project's package runner (`npx`, `pnpm dlx`, `bunx`, `yarn dlx`).
- The token list with its format, the theme file, and how a new token is mapped for Tailwind.
- The installed components, and any that were edited.
- The fonts, the icon library and the motion library, if any.
- The bypass counts, and the paths where the project relies on a bypass on purpose (for example brand colours in an email template).
- Generated, vendored and legacy paths.

## Phase 2: Interview

Rules:

- At most 4 questions per round and at most 5 rounds in total.
- Use AskUserQuestion when it is available. Each question has 2–4 options, and your detected default comes first, marked "(Recommended)".
- Ask open-ended questions in plain text, numbered, with defaults in brackets, so the user can reply "defaults" or answer by number.
- Skip a question the repository already answers, and state what you detected instead ("Fonts: Fraunces and Inter Tight, from `app/layout.tsx`").
- When the code does not settle something clearly, ask instead of guessing. Offer your best guess as the recommended option and say what points each way. Never present a guess as detected.
- Write every option for this product. "Editorial and calm: generous whitespace, a serif display face, one ink-blue accent" is an option; "Modern and clean" is not.
- Mark every value you inferred rather than read from the repository. The Phase 3 summary shows `(inferred)` after it.

Read [references/design-direction.md](references/design-direction.md) before Round 2. It has the personality directions, font pairings, palette method and signature ideas to draw options from.

### Round 1: Identity and product

1. **Name.** Default `designer`. If that name is taken in `.claude/agents/`, propose `<area>-designer` (for example `web-designer`).
2. **When it runs.**
   - Only when asked (default)
   - Proactively when a change adds or restyles UI
3. **Model.** `inherit` (default), `sonnet` (faster and cheaper), `opus` (strongest design judgement).
4. **Product and audience** (plain text). Propose a two-line summary from the README: what the product does, who uses it, and in what setting (at a desk all day, on a phone between tasks, once a month). Ask the user to correct it.

### Round 2: Direction

1. **Personality.** Four contrasting directions from the reference, each written for this product with its consequences for type, colour, density and shape. If the repository already has a clear direction, describe it as the first option.
2. **References and anti-references** (plain text). "Name two or three products whose feel you admire, and anything you never want to see." Propose anti-references from the generic habits in the reference.
3. **Colour.** If the palette is customised: keep it (default), or refine it. If it is the shadcn default: build a palette from a brand hue (ask for it, or propose one that fits the personality), with warm, cool or neutral greys, and the accent used only for the primary action and the signature.
4. **Typography.** If the fonts are customised: keep them (default). Otherwise: two pairings from the reference that fit the personality, or the user's own. Ask whether the product needs a mono face for data.

### Round 3: Form

1. **Shape.** The radius: keep `--radius` as detected (default), sharp (`0.25rem` or less), or soft (`0.75rem` or more).
2. **Density and depth.**
   - Comfortable spacing, depth from borders and tonal surfaces (default for most products)
   - Compact spacing for data-heavy screens, depth from borders only
   - Comfortable spacing, depth from soft shadows
3. **Motion.** Subtle (150–200 ms state changes, no decorative motion; default) · none · expressive (staggered entrances and shared transitions, only with an installed motion library). All options respect `prefers-reduced-motion`.
4. **Signature** (plain text). Propose two or three recognisable devices that fit the direction, from the reference, and ask the user to choose, combine or write their own. It must be something the agent can apply on every screen.

### Round 4: Rules (plain text and one choice)

1. **Missing components.**
   - Add them with the shadcn CLI (default)
   - Use only the installed ones, and list what is missing in the report
2. **House rules.** Show the UI conventions found in Phase 1 as a numbered list and ask: "Which of these should the designer follow? Anything to add?" Examples: "every page starts with `PageHeader`", "tables use `DataTable` from `components/data-table/`", "no modals for forms longer than three fields".
3. **Banned patterns.** Propose the defaults from [references/agent-template.md](references/agent-template.md), plus anything the project already forbids. Before proposing a default, check the bypass counts from Phase 1. If the project relies on a pattern in one place, propose the ban with that exception.
4. **Out of scope.** Paths to ignore. Propose the generated, vendored and legacy paths, and any area that keeps its own look on purpose (marketing site, emails).

### Round 5: Follow-ups (only if needed)

Ask only about answers that were unclear or that conflict, for example a "bold and graphic" direction with a compact, border-only density.

## Phase 3: Confirm

Show this summary before writing anything:

```text
Agent:       <name>  ->  .claude/agents/<name>.md
Runs:        <trigger>
Product:     <one line>
Direction:   <personality, one line of consequences>
References:  <admired> | avoid: <anti-references>
Colour:      <palette decision, format, accent rule>
Type:        <display / text / mono>
Shape:       radius <value> · <density> · depth from <borders|shadows|surfaces>
Motion:      <level>
Signature:   <device>
Components:  <n> installed · missing ones: <add with CLI | report only>
Theme file:  <path> (<OKLCH|HSL>, Tailwind <v3|v4>)
Checks:      <lint command> · <typecheck command>
House rules: <count> rules (listed below)
Banned:      <patterns>
Ignores:     <paths/patterns>
Tools:       Read, Grep, Glob, Bash, Edit, Write   Model: <model>
```

Put `(inferred)` after every value you inferred rather than detected. Then list the house rules in full and ask: "Generate the agent with these settings?" Apply any corrections, show the changed lines again, and continue only after a yes.

## Phase 4: Generate

1. Read [references/agent-template.md](references/agent-template.md). It defines the frontmatter, the section order and the fixed rules of the generated agent.
2. Read [references/examples/nextjs-supabase.md](references/examples/nextjs-supabase.md) to calibrate how concrete the Design direction and Tokens sections must be. Never copy its direction, palette, fonts or signature.
3. If the user chose a new palette, fonts or radius, write the agent's Tokens section as the target values, and make "apply the design direction to the theme" the first task in the hand-off. The generator itself does not edit the theme file.
4. If `.claude/agents/<name>.md` already exists, show the user what would change and ask before overwriting it.
5. Write the agent to `.claude/agents/<name>.md` following the template. Every token, component, path and command must come from the repository or the interview: write "Page titles use `PageHeader` from `components/page-header.tsx`", not "use a consistent header".
6. Write documented path patterns with braces, for example `app/(app)/{workspace}/page.tsx`. Angle brackets are only for the fill-in slots of the Output template.
7. Never invent tokens, components, rules or commands. Anything not found in the repository or given by the user is left out; if it matters, go back and ask.

## Phase 5: Validate and hand off

1. Frontmatter: valid YAML; `name` matches the filename; fields in the order `name`, `description`, `tools`, `model`; `tools` is `Read, Grep, Glob, Bash, Edit, Write`.
2. Body:
   - Every token named exists in the theme file, or is listed as a target value the agent will add.
   - Every component named is installed, and the add command uses the project's package runner.
   - The lint and typecheck commands exist in the scripts.
   - The Design direction names concrete choices (a font, a hue, a radius, a device), not only adjectives.
   - No `<...>` placeholder or `TODO` remains outside the `## Output` template. Angle brackets in real code, such as JSX, are not placeholders.
   - The Boundaries include the uncertainty rule, and the Output has Brief questions and Open questions sections.
   - The body is 80–150 lines. It contains no AI or tool attribution. The "Generic design" heading is not attribution.
3. Offer a dry run, and say that it writes UI files into the working tree: "Want me to run it on one screen? It will edit files you can review with `git diff`." If the user chose new tokens, propose "apply the design direction to the theme" as the dry run. If yes, delegate to the new agent by name. If the session does not see it yet, run a general-purpose agent with the file's body as its instructions. If it returns brief questions, ask the user and run it again with the answers.
4. Hand off in three lines or fewer: where the file is, how to call it ("design the settings page", or `@<name>`), and how to change it later (edit the file, or run `/designer:create` again). Suggest committing the file so the team shares the agent. If a code reviewer exists, mention that it can review the designer's changes.
