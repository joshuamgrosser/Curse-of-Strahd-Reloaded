# Foundry VTT Style Guide — Curse of Strahd: Reloaded

This document describes how *Curse of Strahd: Reloaded* markdown should be rendered when imported into **Foundry Virtual Tabletop** journals. It mirrors the visual language of the published guide at [strahdreloaded.com](https://www.strahdreloaded.com/) and the site styles in [`publish.css`](../publish.css).

Use this guide when converting Reloaded articles into Foundry journal HTML so formatting stays consistent across arcs, chapters, and appendices.

---

## Goals

1. Preserve Reloaded’s structure (scenes, callouts, read-aloud boxes, combat tables).
2. Use Foundry-native HTML (journals support HTML natively).
3. Prefer **inline styles** on elements so imports work without a custom Foundry CSS module.
4. Link original *Curse of Strahd* citations to imported CoS journal pages when available.

---

## Color palette (dark theme)

Matched to the Obsidian Publish dark theme in `publish.css`:

| Token | Hex / value | Use |
|-------|-------------|-----|
| Primary | `#FFC107` | Headings, strong lead-ins, description/sidebar borders |
| Secondary | `#d697e9` | Citations, artist credits, content-link accent |
| Info | `#d8d8d8` | `[!info]` callouts |
| Warning | `#ff8725` | `[!warning]` callouts |
| Lore | `#5fdfff` | `[!lore]` callouts |
| Abstract / Narrative | `#68ff4a` | `[!abstract]` callouts |
| Profile / Tip | `#d79eff` | `[!profile]` / `[!tip]` callouts |
| Item | `#ff83ea` | `[!item]` callouts |
| Design / Combat | `#ff6262` | `[!design]` / `[!combat]` callouts |

Callout backgrounds use translucent fills (e.g. warning `rgba(100,45,0,0.35)`, lore `rgba(0,53,66,0.40)`, primary boxes `rgba(54,42,0,0.35)`).

---

## Journal page structure

### Arc articles (Acts I–IV)

Example: `Arc A - Escape From Death House`

| Markdown | Foundry |
|----------|---------|
| `#` (H1) | **New journal page** |
| `##` (H2) | **New journal page** |
| `###` / `####` | Inline headings inside the current page |

This keeps scene groups (e.g. `A2a. The Arrival`) as navigable pages while room names (e.g. `Entrance`, `Main Hall`) stay nested in the page body.

### Reference articles (Introduction, Chapters 1–3, Appendices)

Example: `Lore of Barovia`

| Markdown | Foundry |
|----------|---------|
| `#` (H1) | **New journal page** |
| `##` / `###` | Inline headings inside that page |

So `Religions` is one page, and `The Church of the Morninglord` is an `<h2>` under it—not a separate top-level page.

---

## Element mapping

### Headings

```html
<h3 style="color:#FFC107;margin:0.9em 0 0.35em;">Entrance</h3>
```

- Inline H2–H4 use primary gold.
- Page titles use Foundry’s journal page name (sidebar); do not duplicate the H1/H2 in the page body when that heading created the page.

### Body text

- Paragraphs → `<p>…</p>`
- Emphasis `*text*` → `<em>…</em>`
- Bold `**text**` → `<strong style="color:#FFC107;">…</strong>`
- Lead-ins `***The Bed.***` → same gold strong treatment
- Unordered / ordered lists → `<ul>` / `<ol>` with `<li>`
- Blockquotes (non-callout) → left gold border:

```html
<blockquote style="border-left:3px solid #FFC107;margin:0.5em 0;padding:0.25em 0 0.25em 1em;">
  <p>…</p>
</blockquote>
```

### Obsidian callouts → styled `<div>` boxes

**Do not use `<details>` / `<summary>`.** Foundry’s ProseMirror journal editor rewrites those tags: `<summary>` is stripped, and the client shows a generic “Details” disclosure that breaks the Reloaded look.

Source:

```markdown
> [!warning]+ **A Second-Level Adventure**
> Remember that…
```

Foundry:

```html
<div class="reloaded-callout callout-warning"
  style="border:3px solid #ff8725;border-radius:12px;padding:12px 18px;background:rgba(100,45,0,0.35);margin:0.85em 0;">
  <p style="color:#ff8725;font-weight:700;font-size:1.05em;margin:0 0 0.65em 0;">
    <span style="margin-right:0.4em;">⚠</span>A Second-Level Adventure
  </p>
  <div class="callout-body">
    <p>Remember that…</p>
  </div>
</div>
```

Callouts are always expanded in Foundry (the Obsidian `+` / `-` fold markers are ignored for collapse state). Combat meta fields (`Combat Level`, `Expected Character Level`, `Expected HP Consumption`) should each be their own `<p>` so labels do not run together.

**Callout type → class / color / icon**

| Type | Class suffix | Border color | Icon |
|------|--------------|--------------|------|
| `info` | `callout-info` | `#d8d8d8` | ℹ |
| `warning` | `callout-warning` | `#ff8725` | ⚠ |
| `lore` | `callout-lore` | `#5fdfff` | 📖 |
| `abstract` | `callout-abstract` | `#68ff4a` | ✦ |
| `profile` / `tip` | `callout-profile` | `#d79eff` | 👤 |
| `item` | `callout-item` | `#ff83ea` | ◆ |
| `design` | `callout-design` | `#ff6262` | 🔧 |
| `combat` | `callout-combat` | `#ff6262` | ⚔ |

Callout bodies may contain paragraphs, lists, headings, and **tables**. Convert nested markdown (especially tables) before wrapping in the callout `<div>`.

### Read-aloud / letter boxes

Source: `<div class="description">`, `<div class="sidebar">`, etc.

```html
<div class="description"
  style="border:3px solid #FFC107;border-radius:12px;padding:16px 20px;background:rgba(54,42,0,0.35);margin:0.85em 0;">
  <p>A grand manor stands before you…</p>
</div>
```

Apply the same box chrome to: `description`, `sidebar`, `handout`, `item`, `statblock`, `details`.

### Tables

Markdown pipe tables become **ProseMirror-native HTML tables** — the same shape Foundry generates when you insert a table from the editor toolbar.

**Why not styled `<td>`s or CSS-grid `<div>`s?** Both look fine after API import, then break on the first ProseMirror edit:

| Approach | After import | After edit → save |
|----------|--------------|-------------------|
| `<th>` / fancy `<td style=…>` | OK | Header cells merge (`3 Players4 Players…`) |
| CSS-grid `<div class="reloaded-cell">` | OK | Header cells flatten to `<p><span style=…>` and the grid collapses |
| Native `<td><p>…</p></td>` | Plain but correct | Columns survive |

```html
<table class="reloaded-table" border="1" style="width:100%;margin:0.75em 0;border-collapse:collapse;">
  <tbody>
    <tr>
      <td><p>&nbsp;</p></td>
      <td><p><strong>3 Players</strong></p></td>
      <td><p><strong>4 Players</strong></p></td>
      <td><p><strong>5 Players</strong></p></td>
      <td><p><strong>6 Players</strong></p></td>
    </tr>
    <tr>
      <td><p>Shadow</p></td>
      <td><p>3</p></td>
      <td><p>4</p></td>
      <td><p>5</p></td>
      <td><p>6</p></td>
    </tr>
  </tbody>
</table>
```

Notes:

- **Every cell must wrap content in `<p>`** (or a real block like `<ul>`). Omitting `<p>` is what triggers merge bugs on edit.
- No `<thead>` / `<th>` — ProseMirror’s table plugin does not round-trip them cleanly.
- Do **not** put border/background/padding styles on individual cells; ProseMirror moves or drops them. Use `border="1"` on the `<table>` plus optional world CSS (below).
- Header labels use `<strong>` inside the cell paragraph.
- Preserve empty leading cells as `&nbsp;` inside `<p>`.
- Allow raw list HTML in cells — do not wrap `<ul>` in an extra `<p>`.
- Never leave markdown pipes as plain text in the journal body.

#### Optional world CSS (visual polish)

Paste into a Custom CSS module (or equivalent) if you want Reloaded-colored headers without fighting ProseMirror:

```css
.journal-entry-page table.reloaded-table {
  width: 100%;
  border-collapse: collapse;
  margin: 0.75em 0;
}
.journal-entry-page table.reloaded-table td {
  border: 1px solid rgba(255, 255, 255, 0.3);
  padding: 8px 12px;
  vertical-align: middle;
}
.journal-entry-page table.reloaded-table tr:first-child td {
  background: #2c2c2c;
  color: #fff;
  font-weight: 700;
  text-align: center;
}
```

### Citations

Source:

```html
<span class="citation">N1. St. Andral's Church (p. 97)</span>
<span class="citation"><em>This scene takes place in Chapter 5: Area N2.</em></span>
```

Foundry:

1. Strip the `<span class="citation">` wrapper.
2. If the citation matches an imported CoS location page, emit a Foundry content link:

```html
<a class="content-link" draggable="true" data-link
   data-uuid="JournalEntry.… .JournalEntryPage.…"
   data-id="…" data-type="JournalEntryPage"
   style="color:#d697e9;font-weight:700;">N1. St. Andral's Church (p. 97)</a>
```

3. Otherwise (e.g. `Appendix B: Area 1`, external books), use styled emphasis:

```html
<em style="color:#d697e9;font-weight:700;">Appendix B: Area 1</em>
```

Patterns to resolve when possible:

- `CODE. Name (p. N)` → location page
- `Chapter N: Area CODE` → location page
- `Chapter N: Title (p. N)` → chapter journal
- Bare `Name (p. N)` when it uniquely matches a location page name

**Do not** leave raw `@UUID[…]{…}` text in the journal—Foundry may show it literally if enrichers do not run. Prefer `content-link` anchors when writing via the API.

### Artist credits

```html
<p class="credit"
   style="display:block;text-align:center;font-style:italic;font-size:0.85em;color:#d697e9;font-weight:700;margin:0.35em 0 1em;">
  "Rose &amp; Thorn" by Caleb Cleveland. Support him on <a href="…">Patreon!</a>
</p>
```

### Wiki links & images

| Source | Foundry |
|--------|---------|
| `[[Path#Heading\|Alias]]` | Italic display text (`Alias`, or last path segment) |
| `![[image.png]]` | Placeholder caption until assets are uploaded: centered italic `[Image: image.png]` |

---

## Foundry ProseMirror caveats

Journal HTML is normalized when opened **and again when saved from the editor**. Observed breakage:

| Input | What Foundry does | Mitigation |
|-------|-------------------|------------|
| `<details>` + `<summary>` | Strips summary; shows native “Details” label | Use styled `<div>` callouts |
| `<th>` / styled `<td>` without `<p>` | Merges header cell text on edit | Native `<td><p>…</p></td>` only |
| CSS-grid cell `<div>`s | Flattens to `<p><span style=…>` on edit | Don’t use grids for tabular data |
| Inline cell chrome (border/background) | Moved onto spans or dropped | Style via world CSS + `class="reloaded-table"` |
| Cell / div text | Wrapped in `<p>` | Pre-wrap plain text in `<p>` |

**Important:** Markup can look correct after API import and still break the first time you edit that page. Always edit-test a combat page before treating the markup as final.

## Conversion pipeline (recommended)

To avoid escaping bugs (the common failure mode for tables/callouts):

1. Convert Obsidian callouts → finished `<div class="reloaded-callout">` HTML (including nested tables).
2. Convert remaining markdown → HTML.
3. **Pass through** existing HTML blocks (`<div>`, `<table>`, …) without running them through HTML escaping.
4. Style known Reloaded div classes / credits.
5. Linkify citations last, without rewriting text inside existing `content-link` anchors.

**Never** wrap already-generated HTML in `escape_html` / `textContent`-style escaping. That produces visible tags like `&lt;div…&gt;` or broken pipe-table text in Foundry.

---

## Folder layout in Foundry (suggested)

```
Journals/
  Curse of Strahd: Reloaded/
    Introduction/
    Chapter 1 - Beginning the Campaign/
    Chapter 2 - The Land of Barovia/
    Chapter 3 - Running the Game/
    Act I - Into the Mists/
    Act II - The Shadowed Town/
    Act III - The Broken Land/
    Act IV - Secrets of the Ancient/
    Appendices/
```

Keep original CoS chapter journals (location map pages) in a separate folder so Reloaded citations can deep-link to them.

---

## QA checklist

When reviewing a converted article in Foundry:

- [ ] Callouts render as colored boxes with icon + title (not a native “Details” disclosure, not raw `[!lore]`)
- [ ] Description / sidebar boxes show gold borders
- [ ] Tables show **separate columns** (native `<table>`, headers not concatenated)
- [ ] Combat enemy counts align under the correct player-count headers
- [ ] **Edit-test:** open the page in ProseMirror, save, and confirm columns still match
- [ ] HTML list cells inside balancing tables render as lists
- [ ] CoS citations are clickable content links where a target exists
- [ ] No literal `@UUID[…]` or `<span class="citation">` text visible
- [ ] Page splitting matches the article type (arc vs reference)

---

## Reference

- Live guide styling: [Arc A on strahdreloaded.com](https://www.strahdreloaded.com/Act+I+-+Into+the+Mists/Arc+A+-+Escape+From+Death+House)
- Source CSS: [`publish.css`](../publish.css)
- Combat callout template: [`_other/templates/combat.md`](templates/combat.md)
