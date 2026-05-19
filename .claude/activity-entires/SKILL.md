---
name: activity-entries
description: Generate JSON entries for the "Something To Do" randomizer site — each entry pairs an activity suggestion with a hand-picked font, font size, weight, letter-spacing, and background/foreground/accent color palette so each one feels visually distinct. Use this skill whenever the user asks to "add activities", "generate more entries", "make more things to do", "add to the JSON", "give me more boredom-buster ideas", or any variation. Also trigger when the user pastes the existing JSON and asks for additions, or asks to brainstorm new entries in the same format. The output is always raw JSON objects ready to drop into the existing `activity-data` script block.
---

# Activity Entries

Generate JSON entries for the "Something To Do" site (the randomizer page that displays one activity at a time with bold typography). Each entry is one JSON object pairing an activity with a deliberate visual treatment.

## The JSON shape

Every entry has these fields. The names match exactly — the HTML reads them directly:

```json
{
  "text": "Main activity text shown big and centered.",
  "subtitle": "Optional smaller line of context below the main text.",
  "fontFamily": "'Cinzel', serif",
  "fontSize": "clamp(1.8rem, 4.5vw, 4rem)",
  "fontWeight": "700",
  "letterSpacing": "0.04em",
  "bg": "#1c1410",
  "fg": "#e8c87c",
  "accent": "#b8853a"
}
```

`subtitle` is optional — omit the field entirely if not needed (don't include it as `""` or `null`).

All numeric values (`fontWeight`, `bg`, `fg`, `accent`) are strings.

## Field rules

**text** — the activity itself. One concrete, specific thing a person could actually do right now or today. Avoid vague suggestions like "be creative" or "go outside". Good examples: "Memorize the names of all eight planets in reverse order", "Cook one recipe from a country you've never visited", "Build a tiny paper boat and float it in a sink". Vary the verb forms — some sentences, some imperatives, some all-caps for visual punch, some lowercase for intimacy.

**subtitle** — use only when `text` is short/poetic and needs grounding ("neon dreams" → subtitle explaining what to actually do). If `text` is already a full sentence, skip the subtitle.

**fontFamily** — pick from the fonts already loaded in the HTML. Don't introduce new ones (they'd 404). The available roster, with their typical use:

| Family | Style | Best for |
|---|---|---|
| `'Cinzel', serif` | Roman capitals, formal | History, ancient, monumental |
| `'Shrikhand', cursive` | Bold display, playful | Food, fun, casual |
| `'VT323', monospace` | Pixel terminal | Tech, time, instructions |
| `'Caveat', cursive` | Handwritten script | Personal, intimate, soft |
| `'Abril Fatface', serif` | Heavy display serif | Editorial, classic, statement |
| `'Major Mono Display', monospace` | Geometric mono | Science, observation, data |
| `'Fraunces', serif` | Soft display serif | Literary, contemporary |
| `'Bungee Shade', cursive` | 3D block letters | Loud, fun, attention-grabbing |
| `'Playfair Display', serif` | High-contrast italic serif | Elegant, contemplative |
| `'Tourney', sans-serif` | Variable techno | Action, sport, modern |
| `'Monoton', cursive` | Striped retro display | Dreamy, neon, atmospheric |
| `'Eczar', serif` | Bookish serif | Learning, lists |
| `'UnifrakturMaguntia', cursive` | Blackletter | Medieval, mysterious |
| `'Silkscreen', cursive` | Tiny pixel font | Retro tech, glitch |
| `'Rubik Glitch', cursive` | Distorted display | Chaos, glitch, error |
| `'Rampart One', cursive` | Stencil-like 3D | Bold, dramatic |

Match font mood to activity mood. "Listen to an entire album with eyes closed" → Playfair italic, not Bungee Shade. "GLITCH IN THE SYSTEM" → Rubik Glitch, not Caveat.

**fontSize** — always a `clamp(min, ideal, max)` value so the text scales gracefully. Pick the size band by text length and visual weight:

- Short punchy text (1–3 words): `clamp(3rem, 8vw, 7rem)` — really big
- Medium text (a short sentence): `clamp(2rem, 5vw, 4.6rem)`
- Long text (full sentence with subordinate clause): `clamp(1.6rem, 4vw, 3.6rem)`
- Decorative fonts with thick strokes (Bungee Shade, Monoton, Cinzel) need smaller sizes so they don't dominate
- Mono fonts (VT323, Silkscreen, Major Mono) need slightly larger sizes since their characters are visually thinner

**fontWeight** — must be a string. Most fonts have one weight available so use the natural one (`"400"` for display fonts, `"700"` for Cinzel/Caveat/Eczar, `"900"` for Fraunces/Playfair Display).

**letterSpacing** — small adjustments only:

- Tight (`-0.02em` to `-0.01em`) for heavy display serifs (Fraunces, Playfair, Abril Fatface)
- Neutral (`0`) for scripts and most defaults
- Wide (`0.02em` to `0.05em`) for ALL CAPS, monospace, and small-cap roman (Cinzel, Tourney)
- Very wide (`0.08em` to `0.1em`) only for tiny pixel/techno fonts (Silkscreen) to space them out

**bg / fg / accent** — a hand-picked palette where:

- `fg` must have strong contrast against `bg` (text needs to be readable at large sizes)
- `accent` is for the pulsing header dot — should pop against `bg` but doesn't need to read as text
- Each palette should feel intentional, like it was chosen for *this* activity (medieval = browns and parchment, neon = magenta/cyan/black, sunset = orange/purple)

Avoid grey/neutral palettes — every entry should have personality. Avoid repeating the exact same `bg` across entries.

## Variety across the set

When generating a batch, deliberately vary:

- **Font** — don't reuse the same font twice in a batch unless the user asks for many entries (then aim for at most 2–3 entries per font across 10+ items)
- **Background lightness** — alternate dark and light backgrounds; a stretch of all-dark entries gets monotonous
- **Text length** — mix short punchy entries with longer descriptive ones
- **Mood** — mix contemplative, playful, physical, intellectual, and silly activities

## Output format

When asked to generate entries, output ONLY a JSON array of objects (no prose explanation around it, unless the user explicitly asks for one). Format each entry on a single line so it's easy to paste into the existing data block:

```json
[
  { "text": "...", "fontFamily": "...", "fontSize": "...", "fontWeight": "...", "letterSpacing": "...", "bg": "...", "fg": "...", "accent": "..." },
  { "text": "...", "subtitle": "...", "fontFamily": "...", "fontSize": "...", "fontWeight": "...", "letterSpacing": "...", "bg": "...", "fg": "...", "accent": "..." }
]
```

If the user pastes existing entries and asks for additions, only return the NEW entries — not the existing ones repeated.

## If the user wants a different theme

The skill works for any randomizer of the same shape — date ideas, writing prompts, workout moves, dinner ideas, gratitude prompts, etc. The fields stay the same; just swap the activity content. Match the visual treatment to the theme: workout moves might use heavier display fonts and energetic palettes; gratitude prompts might use softer handwritten fonts and pastel palettes.

## Examples

**Example 1** — user says "give me 3 more activities":

```json
[
  { "text": "Learn to fold a fitted sheet properly.", "fontFamily": "'Caveat', cursive", "fontSize": "clamp(2.4rem, 6vw, 5.6rem)", "fontWeight": "700", "letterSpacing": "0", "bg": "#fef9ef", "fg": "#3d405b", "accent": "#e07a5f" },
  { "text": "RECORD A VOICE MEMO TO YOUR FUTURE SELF", "fontFamily": "'VT323', monospace", "fontSize": "clamp(2rem, 5.2vw, 4.8rem)", "fontWeight": "400", "letterSpacing": "0.02em", "bg": "#1d3557", "fg": "#f1faee", "accent": "#e63946" },
  { "text": "the spice cabinet inventory", "subtitle": "Open every jar and smell each one. Throw out anything older than two years.", "fontFamily": "'Monoton', cursive", "fontSize": "clamp(2.6rem, 7vw, 6rem)", "fontWeight": "400", "letterSpacing": "0.04em", "bg": "#6a040f", "fg": "#ffba08", "accent": "#9d0208" }
]
```

Notice the variety: one handwritten + light, one mono + dark, one display + with subtitle. Three different fonts, three different palette moods.

**Example 2** — user pastes existing JSON and says "add 5 more about cooking":

Generate 5 entries themed around cooking, but still vary their visual treatment. Don't make them all use `'Shrikhand', cursive` just because it's food-themed — variety within the theme matters.

**Example 3** — user says "make a date-ideas version with 10 entries":

The same shape, but the `text` content is date ideas, and the palette/font choices lean romantic where appropriate (more Caveat, Playfair Display italic, warm palettes) — but not exclusively, since variety still matters.
