# Landie Recipe — CLAUDE.md

## Project Overview

Single-file recipe web app for **香蕉燕麥馬芬 (Banana Oat Muffins)**. Everything lives in `index.html` — HTML, CSS, and JS are all in one file. No build system, no framework, no dependencies (Google Fonts is the only external resource).

## File Structure

```
Landie-Recipe/
├── index.html       ← entire app (HTML + CSS + JS)
├── logo.png         ← brand logo shown at top
├── README.md        ← project documentation
└── CLAUDE.md        ← this file
```

## Architecture Rules

- **Never split into multiple files.** The single-file constraint is intentional — the app must be openable by double-clicking `index.html` with no server.
- **No npm, no bundler, no framework.** Vanilla HTML/CSS/JS only.
- **No external JS libraries.** Web APIs only (Web Share API, Web Audio API, Canvas API, Anthropic fetch, localStorage).

## CSS Conventions

All colors are defined as CSS custom properties on `:root`. Always use tokens, never hard-coded hex values in new rules.

| Token | Value | Use |
|---|---|---|
| `--cream` | `#FAF7F2` | page background |
| `--warm-white` | `#FDF9F4` | overlay backgrounds |
| `--oat` | `#C4956A` | primary accent |
| `--oat-light` | `#E8D5BE` | borders, backgrounds |
| `--oat-dark` | `#9B6E47` | text, active states |
| `--sage` | `#A8B5A0` | secondary accent |
| `--sage-light` | `#D4DDCF` | subtle backgrounds |
| `--sage-dark` | `#718069` | secondary dark |
| `--brown` | `#5C4033` | headings |
| `--text` | `#3D2B1F` | body text |
| `--text-light` | `#7A6054` | secondary text |
| `--text-muted` | `#A89080` | hints, labels |
| `--card-bg` | `#FFFFFF` | card backgrounds |
| `--radius` | `20px` | card corners |
| `--radius-sm` | `12px` | inner elements |
| `--radius-xs` | `8px` | buttons, inputs |

## JS Data Structures

### `BASE` — ingredient amounts
Each key maps to `[cupLabel, grams, oz]` for the base recipe quantity.

### `ING_NAMES` — bilingual names
`{ zh: '...', en: '...' }` for all 14 ingredient keys. Always use `ingName(key)` to get the name — never read `ING_NAMES[key][lang]` directly in render code.

### `TYPES` — mold configurations
Keys: `rect`, `std`, `mini`. Each has `label`, `labelEn`, `unitLabel: {zh, en}`, `baseQty`, `min`, `max`, `summary(q, lang)`.

### `STEPS` — recipe steps
Each step: `{ num, title:{zh,en}, instruction:{zh,en}|Function, ings:[{key,emoji}], bake?:true }`.
The `instruction` field may be a function (e.g. step 6) that reads live state — check with `typeof` before rendering.

### `INGREDIENT_GROUPS` — ingredient display groups
Three groups (Wet, Dry, Mix-ins), each with `label`, `labelEn`, `items:[{key,emoji}]`. No `name` field — use `ingName(key)`.

### `NOTES` — tips
Array of `{ icon, zh, en }`.

### `TIMERS` — bake countdown state
`{ rect, std, mini }` — each: `{ state:'idle'|'running'|'done', remaining:number, intervalId }`.

### `galleryEntries` — creation gallery
Array of `{ id, base64, mediaType, timestamp, message, notes }`, persisted to `localStorage` key `landie-gallery`.

## State Variables

| Variable | Type | Description |
|---|---|---|
| `state` | object | `{ type, qty, unit }` — active mold, quantity, weight unit |
| `shopMode` | `null\|'select'\|'list'` | shopping list flow state |
| `haveSet` | `Set<string>` | ingredient keys marked as owned |
| `shoppingChecked` | `Set<string>` | shopping list checked items |
| `lang` | `'zh'\|'en'` | active language |
| `fontSize` | `0\|1\|2` | index into `FONT_SIZES = [0.9, 1, 1.12]` |

## Key Functions

| Function | What it does |
|---|---|
| `render()` | Full re-render: summary, ingredients, steps, notes, applyLang, timer ctrls |
| `animateUpdate()` | Shimmer animation then calls `render()` — use for quantity/unit/type changes |
| `renderIngredients()` | Rebuilds ingredient card grid; also renders shop entry/select UI |
| `setShopMode(mode)` | Drives shopping flow state machine (null → select → list) |
| `renderShopSheet()` | Rebuilds the shopping overlay bottom sheet content |
| `renderNotes()` | Fills `#notes-card` with bilingual tips |
| `applyLang()` | Updates all static DOM text for active language |
| `renderTimerCtrl(type)` | Updates a single bake timer card (idle/running/done) without full re-render |
| `renderGallery()` | Renders all saved gallery entries; wires up drag-and-drop |
| `renderGalleryEntry(entry, loading)` | Renders or replaces one gallery entry card |
| `ingName(key)` | Returns ingredient name in active language |
| `qtyUnit()` | Returns mold unit label in active language |
| `fmtMeasure(key)` | Returns `{ cup, weight }` strings scaled to current qty/unit |
| `toggleLang()` | Switches `lang`, calls `render()` |
| `changeFontSize(dir)` | Steps `fontSize` index, applies `body.style.zoom` |
| `showToast(msg)` | Shows bottom toast notification |
| `processPhoto(file)` | Uploads a photo, calls Claude API, saves gallery entry |
| `shareEntry(id)` | Creates a 1080×1080 share image and triggers Web Share / download |

## Language & Bilingual Pattern

- `lang` is global (`'zh'` default, `'en'` alternate)
- All dynamic content reads `lang` at render time — no caching
- Static elements updated in `applyLang()` using element IDs
- Dynamic elements (steps, ingredients, shopping) re-rendered by `render()`
- When adding new bilingual text: add both `zh` and `en` keys to the data object; read via `lang` variable; update `applyLang()` for any new static elements

## Adding New Features

- **New ingredient:** add to `BASE`, `ING_NAMES`, and the correct `INGREDIENT_GROUPS` group
- **New step:** add to `STEPS` with bilingual `title` and `instruction`
- **New UI section:** add HTML element with an `id`, add CSS, update `applyLang()` for bilingual labels
- **New static text:** give element an `id`, update both `zh` and `en` cases in `applyLang()`
- **New data in gallery entries:** add field to the entry object in `processPhoto()`, update `saveGallery()` and `renderGalleryEntry()`

## External API

The gallery AI analysis calls the Anthropic Messages API directly from the browser using `anthropic-dangerous-direct-browser-access: true`. The API key is stored in `localStorage` under `landie-apikey`. Model used: `claude-haiku-4-5-20251001`. Never hardcode an API key in the source.

## Design Principles

- Warm, editorial aesthetic — earth tones, serif headings, generous whitespace
- Card-based layout with `--shadow-soft` and `--shadow-card`
- Animations use `fadeUp`, `fadeDown`, `bounceIn` keyframes — keep them subtle
- Mobile-first: responsive breakpoints at 600px and 900px
- No blank states — every section should show meaningful content on load
