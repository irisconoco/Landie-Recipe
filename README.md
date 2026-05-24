# 香蕉燕麥馬芬 Banana Oat Muffins

A single-page interactive recipe app for Banana Oat Muffins, built with vanilla HTML, CSS, and JavaScript. No frameworks, no build tools — one file, fully self-contained.

---

## Features

### Recipe Controls
- **Mold selector** — switch between Rect Mold, Standard Muffin, and Mini Muffin
- **Quantity stepper** — scale ingredient amounts up or down in appropriate increments
- **Weight unit toggle** — display all measurements in grams (g) or ounces (oz)
- All ingredient quantities and step cards update dynamically as you adjust

### Shopping List
A 3-step flow to generate a personalized shopping list:
1. Tap **建立採購清單 / Create Shopping List** below the ingredient section
2. Tap each ingredient card you already have at home to mark it off
3. Tap **Generate List** — a bottom sheet slides up showing only what you need to buy
   - **Check off items** while shopping in-store
   - **Share** — native share sheet (Web Share API) or clipboard fallback
   - **Copy** — copies the list as formatted text to clipboard
   - **Print** — prints the shopping list only

### Language Toggle
- Switch between **Chinese (中文)** and **English** with one tap
- All content updates: ingredient names, group labels, step titles, instructions, bake times, notes, and UI text

### Font Size Control
- Three size levels: **90% / 100% / 112%**
- A− and A+ buttons in the utility bar; buttons dim when at the smallest or largest limit

### Notes / Tips
- 5 cooking tips rendered dynamically in the selected language
- Topics: banana ripeness, mixing technique, freezing, substitutions, doneness test

---

## Project Structure

```
Landie-Recipe/
├── index.html       # Entire app — HTML, CSS, and JS in one file
├── logo.png         # Brand logo displayed at the top of the page
└── README.md        # This file
```

---

## Tech Stack

| Layer | Details |
|---|---|
| HTML | Single-file, semantic markup |
| CSS | Custom properties (design tokens), CSS animations, `@media print` |
| JavaScript | Vanilla ES6+, no dependencies |
| Fonts | Google Fonts — Playfair Display, Noto Serif TC, Noto Sans TC |
| Sharing | Web Share API with clipboard fallback |
| Font scaling | `document.body.style.zoom` (3 levels) |

---

## Design System

Colors are defined as CSS custom properties on `:root`:

| Token | Value | Role |
|---|---|---|
| `--cream` | `#FAF7F2` | Page background |
| `--oat` | `#C4956A` | Primary accent |
| `--oat-light` | `#E8D5BE` | Borders, backgrounds |
| `--oat-dark` | `#9B6E47` | Text, active states |
| `--sage` | `#A8B5A0` | Secondary accent |
| `--brown` | `#5C4033` | Headings |
| `--text` | `#3D2B1F` | Body text |

Border radii: `--radius` 20px · `--radius-sm` 12px · `--radius-xs` 8px

---

## Key Data Structures

### Ingredient base amounts (`BASE`)
Each ingredient stores a tuple of `[cup label, grams, oz]` for the base recipe quantity.

### Mold types (`TYPES`)
Three mold types (`rect`, `std`, `mini`), each with a base quantity, min/max range, unit label, and a bilingual summary function.

### Steps (`STEPS`)
Seven steps, each with bilingual `title` and `instruction` objects (`{ zh, en }`), a list of ingredient keys used in that step, and an optional `bake` flag for the bake-time badge row.

### Ingredient groups (`INGREDIENT_GROUPS`)
Ingredients organized into three groups — Wet, Dry, Mix-ins — used for both the ingredient display and the shopping list grouping.

### Ingredient names (`ING_NAMES`)
Bilingual name lookup for all 14 ingredients: `{ zh: '...', en: '...' }`.

### Notes (`NOTES`)
Five tips, each with an icon and bilingual text: `{ icon, zh, en }`.

---

## State

| Variable | Type | Description |
|---|---|---|
| `state.type` | `'rect' \| 'std' \| 'mini'` | Active mold type |
| `state.qty` | `number` | Current quantity |
| `state.unit` | `'g' \| 'oz'` | Weight unit |
| `shopMode` | `null \| 'select' \| 'list'` | Shopping flow state |
| `haveSet` | `Set<string>` | Ingredient keys marked as owned |
| `shoppingChecked` | `Set<string>` | Items checked off while shopping |
| `lang` | `'zh' \| 'en'` | Active language |
| `fontSize` | `0 \| 1 \| 2` | Index into `[0.9, 1, 1.12]` zoom levels |

---

## Recipe at a Glance

- **Prep time:** 10 minutes
- **Bake time:** 18–26 minutes (depending on mold)
- **No refined sugar**
- **Freezer-friendly** up to 3 months

### Bake times by mold

| Mold | Time |
|---|---|
| Rect Mold | ~26 min at 350°F / 180°C |
| Standard Muffin | ~26 min at 350°F / 180°C |
| Mini Muffin | ~18 min at 350°F / 180°C |
