---
name: add-recipe
description: Add a new recipe to this site as a phone-friendly page in recipes/ and a card on index.html, with a check for personal details before committing. Use when given a new recipe (pasted text, a photo, a link or an HTML file).
---

# Add a recipe

Read `CLAUDE.md` first; it holds the template, design rules and the no-personal-information rule this skill relies on.

## 1. Pull out the recipe

From whatever arrived, get: dish name, total time, yield (servings, or "one bowl" style), ingredients with amounts, tools, steps, any waits that need a timer, and serving suggestions.

Leave out anything personal: who sent it or where it came from, names, places, dates, chat text, and notes about specific people. If a step is ambiguous, pick the common-sense reading and mention it in the PR description rather than guessing silently. Don't invent ingredients or amounts.

## 2. Make the page

- Slug: lowercase, hyphenated ASCII from the dish name (`&` becomes `and`). Check `recipes/` so it is unique.
- Copy the closest existing page in `recipes/` to `recipes/<slug>.html` (one with a long timer for slow cooks, one with short steps for quick dishes) and rewrite the content. Keep the structure from CLAUDE.md: head tags in `<head>`, sticky header with back link, section 0 prep checklist, numbered steps, timers where there is a wait, milestones, footer.
- Choose a new `--accent` / `--accent-soft` (light and dark) that suits the dish and differs from other recipes. Keep text contrast readable (4.5:1 for body text on `--bg`).
- Update every recipe-specific value in the script: the `localStorage` key (`<slug>-v1`), the default `#meta` text, the prep IDs used for the `ms-prep` milestone, the step that shows `ms-done`, and timer defaults. Make sure every `id` is unique.

## 3. Add the index card

Pick the recipe's category (Dinners, Sides & breads, or Sweets; see CLAUDE.md) and add an `<li>` to that group's `ul.list` in `index.html` using the card markup in CLAUDE.md, keeping the group sorted A to Z by name. `--c`/`--c-dark` are the page's accent colors; the facts match the page's `#meta` time and yield.

## 3½. Add a photo, if one came with the recipe

Follow "Recipe photos" in CLAUDE.md. First look at the image: it should show only food. Then make the hero and thumb WebP files with Pillow, outside any interpreter run from the upload's folder, for example:

```python
from PIL import Image
im = Image.open(src).convert("RGB")
im.thumbnail((1024, 1024)); im.save(f"recipes/img/{slug}.webp", "WEBP", quality=72, method=6)
s = min(im.size); l = (im.width - s) // 2; t = (im.height - s) // 2
im.crop((l, t, l + s, t + s)).resize((192, 192), Image.LANCZOS).save(f"recipes/img/{slug}-thumb.webp", "WEBP", quality=70, method=6)
```

Adjust the crop if the dish isn't centered.

## 4. Check it

- Open `index.html` and the new page at phone width (about 390x844), in light and dark mode if you can. Nothing should scroll sideways, the header should stay pinned, and the back link and card should work.
- Tick a few boxes, reload, and confirm they persist; tap Start over and confirm everything clears.
- Personal-info scan, on the full diff: `git diff --cached` and read it for names, emails, phone numbers, addresses, places, dates tied to people, and "from X" or "X's" attributions. Also run:

  ```sh
  git diff --cached | grep -nEi '@[a-z0-9-]+\.|[0-9]{3}[-. )][0-9]{3}[-. ][0-9]{4}|\b(mom|dad|grandma|grandpa|nana|aunt|uncle|wife|husband)\b|\b(st|street|ave|avenue|rd|road)\b\.?' || true
  ```

  Fix anything it turns up. Fonts URLs from `fonts.googleapis.com` are expected.

## 5. Ship it

Commit as `Add <Dish name> recipe`, push, and open a PR. Once merged to `main`, GitHub Pages publishes it.
