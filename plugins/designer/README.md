# designer

Generates a UI design subagent tailored to your repository. The generator reads your shadcn/ui setup (`components.json`, the theme stylesheet and its tokens, the installed components, the fonts) and two or three of your existing screens. It then interviews you for a design direction and writes `.claude/agents/designer.md` into your project. The agent designs and builds screens that look like your product, not like a template.

Generic interfaces usually come from decisions nobody made: who the product is for, what it should feel like, and a palette left at the library default. The interview makes those decisions once, as concrete choices (a font pairing, a brand hue, a radius, a signature device), and the agent applies them to every screen.

## What the generated agent does

- Builds with shadcn/ui components and your design tokens only. Missing components are added with the shadcn CLI, and no other UI library is installed.
- Checks the brief first. When the purpose, the primary action or the content of a screen is unclear, it returns brief questions instead of guessing, and Claude asks you.
- Writes a short plan before any code: the hierarchy, the one primary action, where your signature appears, and the layout on mobile and desktop.
- Covers every state: loading, empty, error, disabled, long text, narrow screens and dark mode.
- Follows WCAG 2.2 AA. It checks the contrast of every new colour pair in both themes, and checks focus, labels, target sizes, heading order and keyboard paths.
- Checks its own output for generic design habits: gradients, glass panels, an icon in a tinted circle on every item, heavy shadows, filler copy and invented numbers.
- Runs your lint and typecheck before reporting.

## Requirements

shadcn/ui must be set up in the project (`components.json`). If it is not, the generator offers to run `npx shadcn@latest init`, and runs it only if you agree. Tailwind CSS v3 and v4 are both supported.

## Install

```text
/plugin marketplace add turalowski/agents
/plugin install designer@turalowski-agents
```

To install it automatically for everyone who opens your project, commit this to `.claude/settings.json`:

```json
{
  "extraKnownMarketplaces": {
    "turalowski-agents": {
      "source": { "source": "github", "repo": "turalowski/agents" }
    }
  },
  "enabledPlugins": { "designer@turalowski-agents": true }
}
```

## Generate your designer

In your project, run:

```text
/designer:create
```

Or ask in plain words: "set up a design agent for this repo". Answer the questions, confirm the summary, then commit `.claude/agents/designer.md` so your team shares the agent.

If you chose a new palette, fonts or radius, start with "apply the design direction to the theme". After that, ask for screens: "design the settings page", or mention the agent with `@designer`.

## Interview options

| Option                                      | Choices                                                              | Default                                    |
| ------------------------------------------- | -------------------------------------------------------------------- | ------------------------------------------ |
| Name                                        | any lowercase, hyphenated name                                       | `designer`                                 |
| When it runs                                | only when asked · proactively when a change adds or restyles UI      | only when asked                            |
| Model                                       | inherit · sonnet · opus                                              | inherit                                    |
| Product and audience                        | free text                                                            | proposed from your README                  |
| Personality                                 | four contrasting directions written for your product, or your own    | your current direction, if it has one      |
| References and anti-references              | free text                                                            | anti-references from common generic habits |
| Colour                                      | keep · refine · build from a brand hue                               | keep, if your palette is customised        |
| Typography                                  | keep · a proposed pairing · your own, with an optional mono face     | keep, if your fonts are customised         |
| Shape, density and depth                    | radius · comfortable or compact · borders, shadows or tonal surfaces | detected radius, comfortable, borders      |
| Motion                                      | subtle · none · expressive                                           | subtle                                     |
| Signature                                   | one device repeated on every screen                                  | two or three proposals                     |
| Missing components                          | add with the shadcn CLI · report only                                | add with the CLI                           |
| House rules, banned patterns, ignored paths | free text                                                            | proposed from your code                    |

## Update

- **The plugin:** `/plugin marketplace update turalowski-agents`. See [CHANGELOG.md](CHANGELOG.md) for what changed.
- **Your agent:** edit `.claude/agents/designer.md` directly, or run `/designer:create` again. The generator asks before overwriting an existing agent. Updating the plugin never changes your agent.
