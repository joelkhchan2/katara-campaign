---
name: handout-pdf
description: Build an in-world document PDF (a recovered letter, a burned note, a diary page, a printed list or poster) for the campaign using the shared reportlab helper. Use whenever a new prop handout is needed as a real printable PDF. Companion to `handout-prop-split` (what belongs in it).
---

# Building a handout PDF

`props/generators/handout_pdf_helpers.py` is the shared page-building module (wrap, header, section, paragraph, footer). Use it instead of writing canvas calls from scratch.

## Setup
`pip install reportlab` if `python3 -c "import reportlab"` fails.

## Using the helper
```python
import sys
sys.path.insert(0, "props/generators")
from handout_pdf_helpers import Page, THEMES

page = Page("props/out.pdf", THEMES["parchment"])    # or THEMES["festive"]
y = page.header("A Formal Multi-Page Title")          # centered, for lists and posters
# or: y = page.note_header("A Short Note's Title", header_note="Found: in the grate.")
y = page.section(y, "A Sub-Heading")
y = page.para(y, "Body text that wraps automatically.")
page.footer("A small italic closing line.")
page.show_page()                                       # next page
page.save()
```

Two themes: `parchment` (aged single border, for notes, letters and diary pages) and `festive` (double gold border, for printed lists and posters). Add a theme by extending `THEMES` with the keys `paper`, `ink`, `faint`, `accent`, `border`.

For layouts beyond the helpers (tables, columns), use `page.c` (the reportlab canvas) and `page.theme` directly.

## Where output goes
- **Session-specific props:** `props/session-N/`.
- **Standing props** reused across sessions: `props/standing/`.
Generator scripts live in `props/generators/`.

## Known pitfall: em-dashes
Never write the long dash character, or its Unicode escape, in a generator script or in prop text. The `dnd-writing` rule applies to props. Use a colon, parentheses or a full stop. After building a handout, grep the source script and the rendered text for the long dash.

## Verification
Render every page to PNG and look at it before calling a handout done:
```bash
python3 your_script.py
pdftoppm -png -r 100 "props/out.pdf" "$SCRATCHPAD_DIR/preview"
```
Then Read the PNG(s). Check every page on a first build, and at least the changed pages on a revision. Print with background graphics off: parchment textures eat toner.
