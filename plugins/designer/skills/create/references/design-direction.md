# Design direction

Material for the Round 2 and Round 3 options. Use it to write options for the user's product. Never offer it as a menu as written, and never put a direction into the agent that the user did not choose.

## Personality directions

Offer four that contrast with each other and could all fit the product. Each option names its consequences, so the user picks a set of decisions, not a mood.

| Direction           | Type                                                   | Colour                                                            | Density and shape                                       | Fits                                              |
| ------------------- | ------------------------------------------------------ | ----------------------------------------------------------------- | ------------------------------------------------------- | ------------------------------------------------- |
| Editorial and calm  | serif display, quiet sans text, large type contrast    | warm off-white paper, ink-dark text, one deep accent              | generous whitespace, small radius, hairline rules       | reading, writing, planning, professional services |
| Technical and dense | grotesk or mono-forward, tabular figures               | cool neutrals, one signal colour, status colours carry meaning    | compact, sharp corners, borders only                    | developer tools, finance, operations, analytics   |
| Warm and tactile    | rounded humanist sans, friendly display weight         | warm neutrals, a saturated earthy accent, tinted surfaces         | comfortable, soft radius, tonal layers                  | consumer, health, education, community            |
| Bold and graphic    | heavy display grotesk at large sizes, tight tracking   | near-black and near-white with one loud flat accent, no gradients | strong grid, big blocks of colour, sharp or pill shapes | launches, creative tools, brands with confidence  |
| Quiet utility       | one neutral sans in two weights                        | almost monochrome, colour only for state and the primary action   | comfortable, medium radius, borders                     | admin panels, internal tools                      |
| Playful and precise | geometric sans, an expressive display cut for headings | bright accent on calm neutrals, illustrations in token colours    | comfortable, mixed radius used on purpose               | onboarding-heavy consumer apps                    |

## Font pairings

All of these are free on Google Fonts and load with `next/font/google` or `@fontsource`. Propose pairings that fit the chosen direction; prefer one family in two or three weights over many fonts.

- Editorial: Fraunces + Inter Tight · Newsreader + Geist · Instrument Serif + Instrument Sans · Source Serif 4 + Source Sans 3
- Technical: Geist + Geist Mono · IBM Plex Sans + IBM Plex Mono · JetBrains Mono for data with Inter Tight
- Warm: Bricolage Grotesque + Figtree · Nunito Sans + Lora · Outfit + DM Serif Display
- Bold: Space Grotesk + Inter Tight · Archivo (expanded widths) + Archivo · Syne + Manrope
- Quiet utility: Geist alone · Inter Tight alone · Public Sans alone

A pairing is a decision only with a scale: name the display size, the body size and the ratio (1.2 for dense products, 1.25–1.333 for most, 1.5 for editorial). Use tabular figures (`tabular-nums`) for any column of numbers.

## Palette method

Build tokens, not colours. In OKLCH, with Tailwind v4 (convert to HSL for v3 projects):

1. Pick the brand hue `h` and its role: the primary action, the signature, focus. It is not a background colour for large areas unless the direction is "bold and graphic".
2. Neutrals carry the same hue at very low chroma (`0.005`–`0.02`), so greys feel warm or cool on purpose instead of dead grey. Light mode: `--background` around `L 0.98–0.99`, `--foreground` around `L 0.15–0.22`. Dark mode: not an inversion; `--background` around `L 0.14–0.18` with the same hue, surfaces stepped up by `0.03`–`0.05` in lightness.
3. `--primary` at a lightness that gives `--primary-foreground` 4.5:1. `--ring` usually equals `--primary`. `--muted-foreground` must still reach 4.5:1 on `--background` and `--muted`.
4. `--destructive` and any status colours are tuned to the same lightness and chroma family, so they belong to the palette.
5. `--chart-1` to `--chart-5` step around the hue wheel at similar lightness, starting from the brand hue.
6. Check every foreground and background pair in both themes: 4.5:1 for text, 3:1 for large text, icons, borders of inputs and focus rings (WCAG 2.2 AA).

## Signature devices

A signature is one device the product repeats so screens are recognisable without a logo. It must be applicable on every screen, built from tokens and shadcn components, and cheap to maintain. Propose two or three that fit the direction:

- Type: oversized tabular numerals for the key figure on each screen · a serif italic for one word in each page title · small caps labels in the mono face
- Line and grid: hairline rules that run to the edge of the content column · a visible baseline or column grid on empty states · a ruled left margin on lists
- Colour: the accent used only as a 2 px underline on the active item and on the primary action · tinted surfaces that change hue per section of the product
- Shape: one corner treatment, such as sharp cards with a pill primary button · a notch or tab on the active card
- Content: a dateline or breadcrumb set as a running header · status written as words in a fixed position instead of badges everywhere

## Generic habits

These make an interface look machine-made. Propose them as anti-references and list them in the agent's Generic design section, written for the stack.

- Purple-to-blue or multi-stop gradients on backgrounds, buttons or text (`bg-gradient-to-*`, `bg-clip-text text-transparent`)
- A centred hero with a headline, a subline, two buttons, then three icon cards in a row
- An icon in a tinted circle or rounded square above every feature, stat or list item
- Glass panels (`backdrop-blur` with translucent white) and decorative blurred blobs
- Emoji used as icons or bullet points
- `shadow-lg` or larger on every card, `rounded-2xl` on everything, borders and shadows and tinted backgrounds on the same card
- Inter at default tracking for every text style, with no type contrast between heading and body
- Every section the same height and spacing, so nothing leads
- Filler copy ("Unlock the power of", "Seamless", "Supercharge"), lorem ipsum, invented metrics, logos and testimonials
- A badge, a tooltip and an icon on every element
- Gratuitous motion: fade-up on every block, hover scale on every card
- Colour picked per component instead of from tokens
