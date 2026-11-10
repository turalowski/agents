# Changelog

## 1.0.0 - Unreleased

First release.

- The `create` skill generates a repository-specific UI design subagent at `.claude/agents/<name>.md` through five phases: discover, interview, confirm, generate, validate.
- Discovery reads `components.json`, the theme stylesheet (every token in light and dark mode, OKLCH or HSL, Tailwind v3 or v4), the installed shadcn/ui components, the fonts, the icon and motion libraries, and two or three existing screens. It counts token bypasses such as raw palette classes and hex colours, and detects whether the theme is still the shadcn default.
- The interview settles a design direction once per repository: product and audience, personality, references and anti-references, colour, typography, shape, density, motion and a signature device. Options are written for the product, from `references/design-direction.md`.
- The generated agent checks the brief and returns brief questions instead of guessing. It writes a short plan, builds with shadcn/ui and token classes only, covers every state, runs lint and typecheck, and checks WCAG 2.2 AA contrast for every new colour pair in both themes.
- A "Generic design" section lists the habits that make an interface look machine-made, and the agent checks its own output for them.
- Includes a reference template and a sample agent for Next.js + Supabase on Tailwind v4.
