# Mulakhkhaṣ Interactive Table of Contents — Documentation

**File:** `Mulakhkhas_table_of_contents.html`
**Backup:** `Mulakhkhas_table_of_contents_BACKUP.html`

---

## Overview

A single self-contained HTML file: no external dependencies, no server required beyond basic static hosting. Open locally in a browser or host on any web server. A backup of the original file is preserved alongside it.

---

## Data Structure

All content lives in a JavaScript object called `mindmapData` (around line 600). Each node in the tree is a JavaScript object with the following fields:

| Field | Required | Description |
|---|---|---|
| `id` | yes | Unique identifier, hyphen-encoded (e.g. `maq1-1-8`). Never shown to the user. |
| `text` | yes | The primary label shown on the node — typically the section number (e.g. `'1.1.8'`). Named nodes (root, Manṭiq) use their name. The muqaddima node uses an empty string since it has no number. |
| `label` | no | Arabic hierarchical label — the name of the division type (e.g. `'muqaddima'`, `'Jumla'`, `'Mabḥath'`). Displayed in italics within parentheses **before** the English description. |
| `desc` | no | English description of the section's content. Displayed in plain text after the label. |
| `term` | no | Arabic technical term naming the philosophical topic of the section (e.g. `'tanāquḍ'`, `'lawāzim khārijiyya'`). Displayed in italics within parentheses **after** the English description. |
| `page` | no | Page number in the 2002 Qaramaleki/Asgharinezhad edition. Displayed as `p. X`. |
| `class` | yes | CSS class controlling visual appearance and hierarchy level. See below. |
| `children` | no | Array of child nodes. Absence of `children` means the node is a leaf. |

### Display order on each node

> **text** *(label)* desc *(term)* p. X

### Hierarchy classes

| Class | Level | Role |
|---|---|---|
| `root` | 0 | Mulakhkhaṣ (top node) |
| `level-1` | 1 | Manṭiq |
| `jumla` | 2 | Major part (Jumla) |
| `bab` | 3 | Chapter (Bāb) |
| `qism` | 4 | Section (Qism / Mabḥath) |
| `level-5` – `level-7` | 5–7 | Deeper subsections |
| `leaf` | — | Terminal node (no children) |

---

## Correcting a Node

Find the node by its `id` or `text` using the IDE search or the in-browser search box. Edit the relevant fields directly. The `id` should never need to change. If correcting a number, update `text` only — `id` is independent and used internally.

**Example — correcting a description:**
```js
// Before
text: '1.1.5',
desc: 'Subject abd predicate',   // typo

// After
text: '1.1.5',
desc: 'Subject and predicate',
```

---

## Adding a New Node

Insert a new object into the appropriate `children` array. Choose an `id` that fits the existing convention (e.g. `maq1-1-8b` if inserting between `maq1-1-8` and `maq1-1-9`). Set `text` to the display number. All other fields are optional.

**Example:**
```js
{
    id: 'maq1-1-8b',
    text: '1.1.8b',
    desc: 'Description of the new section',
    page: '56',
    class: 'leaf',
},
```

Note: numbering is **not** automatic. If you need clean sequential numbers after an insertion, update subsequent `text` fields manually.

---

## Adding a Translation Passage or Image Table to a Node

Passages and supplementary tables are stored in the `popupContent` object (search for `const popupContent` in the file). A ↗ button appears automatically on any node whose `id` has an entry there. Clicking it opens a modal panel.

### Text passage

```js
'maq1-1-8': {
    title: '1.1.8 — Investigations of external implicates',
    html: `
    <div class="translation-block">
        <p class="translation-label">Translation — p. 50</p>
        <blockquote class="translation-quote">
            "Your translated passage here."
        </blockquote>
        <p class="translation-note">Optional note on the passage, variant readings, etc.</p>
    </div>
    `
},
```

- The `translation-label` line holds the page reference (and any other short header).
- The `translation-note` line is optional — delete it if not needed.
- To attach **multiple passages** to the same node, stack additional `<div class="translation-block">` blocks inside the `html` string.
- To add a subtitle below the modal title, add `subtitle: 'your text'` to the entry.

### Image table (SVG or PNG embedded as base64)

```js
'node-id': {
    title: 'Title shown in modal header',
    subtitle: 'Supplementary tables and diagrams',
    html: `
    <section class="popup-section">
        <h3>Table caption</h3>
        <figure class="popup-figure">
            <img class="zoomable-figure"
                 data-caption="Caption shown when enlarged"
                 alt="Alt text"
                 src="data:image/svg+xml;base64,YOUR_BASE64_HERE">
        </figure>
    </section>
    `
},
```

To convert an SVG or PNG to base64 for embedding, run in Terminal:
```bash
base64 -i your_file.svg | tr -d '\n'
```
Then paste the result after `base64,`.

---

## Search

A search box is available in the controls panel (top right). It searches across `text`, `desc`, `label`, `term`, and the full text of any popup passage attached to a node. Matching nodes are highlighted in gold; non-matching nodes dim. Ancestor nodes of matches are automatically expanded. Press ✕ or Escape to clear.

---

## Controls

| Button | Action |
|---|---|
| Expand All / Collapse All | Show or hide entire tree |
| Level 1–7 | Expand tree to a given depth |
| Fit to Screen | Zoom to fit all visible nodes |
| Zoom In / Out / Reset | Adjust zoom level |
| Reset View | Re-render and scroll to top-left |
