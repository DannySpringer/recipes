# Recipes site

Static recipe pages served by GitHub Pages from the repository root of `main`. No build step, no framework, no dependencies: every page is a single hand-written HTML file with inline CSS and JS. `.nojekyll` turns off Jekyll so files are served as-is.

**This repository is public.** See "No personal information" below before committing anything.

## Layout

```
index.html                      list of all recipes (one tap card each)
recipes/<slug>.html             one page per recipe, fully self-contained
recipes/img/                    optional recipe photos (WebP)
games/<slug>/index.html         small browser games, one folder each (assets beside it)
.claude/skills/add-recipe/      how to add a new recipe (use this skill)
.nojekyll                       serve files as-is
```

- Slugs are lowercase, hyphenated, ASCII: `Black Beans & Greens` becomes `black-beans-and-greens`.
- Recipe pages link back with `<a class="back" href="../index.html">`. Use relative links everywhere; the site lives under `/recipes/` on GitHub Pages, so absolute `/` paths break.

## Design rules (phone first)

- One column, `max-width: 34rem`, 16px side padding. Must read well at 360 to 430px wide with no horizontal scroll.
- Tap targets at least 48px tall (checklist rows and step buttons are 56px).
- Fonts: Bricolage Grotesque (display) and Atkinson Hyperlegible (body) from Google Fonts. Body text 1.125rem.
- Colors are CSS custom properties on `:root`, with a dark set under `@media (prefers-color-scheme: dark)` and `:root[data-theme="dark"]`. Each recipe gets its own `--accent` / `--accent-soft` pair that suits the dish; reuse the rest of the token set.
- `viewport-fit=cover` plus `env(safe-area-inset-*)` padding so nothing hides under the notch or home bar.

## Recipe page template

Copy an existing page in `recipes/` (both follow the same template) and change the content. Keep this structure:

1. `<head>`: charset, viewport, `<meta name="description">`, `<title>` (the dish name only), the small reset `<style>`, Google Fonts links, then the page `<style>`. All of these belong in `<head>`, never `<body>`.
2. Sticky `<header class="top">`: back link row, then `.top-row` with `<h1>` and `<span class="meta" id="meta">` holding "time · yield" (e.g. `~4 hr · serves 6`), then the progress `.bar`.
3. `<main class="wrap">`:
   - `.tools` chips: "Keep screen on" (Wake Lock) and "Start over".
   - Section `0` "Get everything out first": `ul.checks` of ingredients (amounts in `<span class="amt">`), optional items marked `<span class="opt">(optional)</span>` and `data-optional` on the input so they don't count toward progress. A `.milestone#ms-prep` shows when prep is all checked.
   - Numbered sections `1..n`: `<h2><span class="n">N</span>Short imperative</h2>`, a sentence or two of plain instruction, then either a `ul.checks` (things going into the pot) or a single `.step-btn` to mark the step done.
   - Timers: `.timer` blocks with preset `.chip`s and Start/Cancel `.big` buttons. Long timers save their end time so they survive the page closing, and say so; suggest the phone's Clock app for long waits.
   - `.milestone#ms-done` on the last real step, then a `<footer>` with serving notes.
4. Inline `<script>`: checkbox and step state saved in `localStorage` under a key unique to the recipe (`<slug>-v1`), progress bar and `#meta` update, wake lock, timers, Start over. Every ID used for state must be unique on the page.

Writing style: short, friendly, second person, one action per step. Say what "done" looks like ("beans are soft all the way through"). Use real fractions (½, ⅓, ¼) and plain units (cup, tbsp, tsp, oz).

## Index cards

The index groups recipes into categories, each a `<section class="group" id="...">` with an `<h2>` and a `ul.list`, plus a matching link in the `nav.jump` row at the top:

- `#breakfast` **Breakfast**: breakfast dishes. First on the page.
- `#dinners` **Dinners**: main dishes.
- `#sides` **Sides & breads**: breads, dips, sauces, side dishes.
- `#sweets` **Sweets**: cakes, quick breads, desserts.
- `#games` **Games**: not recipes; links into `games/<slug>/`. Keep it last.

Pick the closest group; add a new one (section plus jump link) only when a recipe truly fits none. Each recipe is one `<li>` in its group's list, sorted A to Z by name within the group:

```html
<li><a class="card" href="recipes/<slug>.html" style="--c:<light accent>; --c-dark:<dark accent>">
  <span class="name">Dish Name</span>
  <span class="blurb">One short line about it.</span>
  <span class="facts"><span><time></span><span><yield></span></span>
</a></li>
```

`--c` / `--c-dark` are the recipe page's light and dark `--accent` values. Time and yield match the page's `#meta` text.

## Recipe photos (optional)

A recipe can have one photo of the food. Only the dish: no people, hands with rings or tattoos, names, or anything that shows where it was taken.

- Save two WebP files in `recipes/img/`, with metadata stripped (Pillow drops it by default):
  - `<slug>.webp`: the hero, 1024px wide at most, quality about 72. Aim for under 150 KB.
  - `<slug>-thumb.webp`: a 192×192 square crop centered on the dish, quality about 70.
- Recipe page: put `<img class="hero" src="img/<slug>.webp" width="…" height="…" alt="…">` as the first thing in `<main>`. Use the real pixel size in `width`/`height` and the `aspect-ratio`. Write `alt` to describe the food. Copy the `.hero` rule from `pumpkin-bread-with-maple-glaze.html`.
- Index card: add `has-thumb` to the card's class and put `<img class="thumb" src="recipes/img/<slug>-thumb.webp" width="72" height="72" alt="" loading="lazy">` as the card's first child. The empty `alt` is on purpose, because the card name already says what it is.
- Never commit the original upload. Commit only the compressed copies.

## No personal information

Everything here is public on the internet. Never commit:

- Names of family or friends, or who a recipe came from ("Mom's", "from Jen"). Describe the dish instead.
- Email addresses, phone numbers, street addresses, cities or neighborhoods, workplaces, schools, birthdays, kids' ages, or photos of people.
- Screenshots, text exports or metadata from messages, notes apps or email (headers, timestamps, contact names).
- Health details or allergies tied to a person.
- Personal notes in comments, commit messages, or file names.

Before committing, scan the diff for these. When a recipe arrives with personal context, keep the recipe and drop the context.
