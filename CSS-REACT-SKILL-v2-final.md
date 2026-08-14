---
name: qmas-design-system-css-react
description: >-
  QMAS design system for Ant Design v6 — governs every piece of QMAS UI built as
  HTML / CSS / React (Claude design, artifacts, or code). TRIGGER WHENEVER: (1) the
  user says "QMAS" or "Ant Design v6"; (2) asked to build/design/code any UI, page,
  component, button, form, table, modal, card, or dashboard for the QMAS product;
  (3) any design token, color, type scale, spacing, radius, or shadow is mentioned.
  Output must use the real QMAS tokens (colors, type scale, shadows, spacing) as CSS
  variables + Ant Design v6 components — never raw hex, raw HTML, or arbitrary values.
  This is the CSS/React edition; it does NOT cover pushing UI into Figma.
---

# QMAS Design System for Claude Design (Ant Design v6) — CSS / React

> Source: `qmas-design-system` Figma skill **v14.1** (synced 2026-07-02), translated for HTML/CSS/React. Figma-plugin-only material (component keys, node IDs) intentionally omitted.

You are building UI for **QMAS**, on **Ant Design v6** with Tailwind. Font is Roboto (Ant v6 default stack — no custom load). Follow every rule below on every output. When a real value in the QMAS Figma file conflicts with this doc, the Figma file wins.

---

## 0. Drop-in CSS tokens (use these — never hard-code hex)

```css
:root {
  /* Brand / primary */
  --color-primary:         #1677FF;   /* main CTA, active nav, links */
  --color-primary-hover:   #4096FF;
  --color-primary-active:  #0958D9;
  --color-primary-bg:      #E6F4FF;

  --color-success:  #52C41A;
  --color-warning:  #FAAD14;
  --color-error:    #FF4D4F;

  /* Text — alpha over black, NEVER pure #000 */
  --color-text:            rgba(0,0,0,0.88);
  --color-text-secondary:  rgba(0,0,0,0.65);
  --color-text-tertiary:   rgba(0,0,0,0.45);
  --color-text-quaternary: rgba(0,0,0,0.25);

  /* Surface & border (3-layer) */
  --color-bg-layout:        #F5F5F5;  /* page canvas — NEVER white */
  --color-bg-container:     #FFFFFF;  /* card / modal / table surface */
  --color-border:           #D9D9D9;
  --color-border-secondary: #F0F0F0;  /* card border, table dividers */
  --color-geekblue-1:       #F0F5FF;  /* optional content-canvas tint */

  /* Table fills */
  --color-fill-tertiary:    rgba(0,0,0,0.04);  /* table HEADER band bg */
  --color-fill-quaternary:  rgba(0,0,0,0.02);

  /* Radius (2-tier) */
  --radius-interactive: 6px;  /* Button, Input, Select, Dropdown */
  --radius-container:   8px;  /* Card, Modal, Panel, Drawer */
  --radius-dense:       4px;
  --radius-tag:         2px;

  /* Spacing (8px grid) */
  --space-xxs: 4px;   --space-xs: 8px;   --space: 16px;   /* 16 = DEFAULT */
  --space-lg:  24px;  --space-xl: 32px;

  /* Shadows — table/filter cards use the LIGHT one (see §5) */
  --shadow-card:    0 1px 2px 0 rgba(0,0,0,0.03),
                    0 1px 6px -1px rgba(0,0,0,0.02),
                    0 2px 4px 0 rgba(0,0,0,0.02);
  --shadow-table:   0 2px 0 rgba(0,0,0,0.02);           /* light — table/filter/toolbar */
  --shadow-overlay: 0 6px 16px 0 rgba(0,0,0,0.08),
                    0 3px 6px -4px rgba(0,0,0,0.12),
                    0 9px 28px 8px rgba(0,0,0,0.05);      /* Modal, Drawer, Dropdown */

  /* Brand layer — App Shell ONLY (separate from tokens above) */
  --brand-navy-400: #1950AE;  /* left-nav bg */
  --brand-navy-100: #E5ECF6;  /* avatar bg */
  --brand-text-600: #232B3A;  /* header user name */
  --brand-text-300: #8991A0;  /* header email caption */
}
```

> Shadow values are best-effort Ant v6 defaults; the QMAS Figma effect styles are the real source. What matters most: **table & filter cards get the LIGHT shadow, not the card shadow** (§5).

---

## 1. Typography — type scale (weight caps at 600; avoid 700)

| Style | size | line-height | weight | Use |
|---|---|---|---|---|
| Small/Regular | 12 | 20 | 400 | caption, helper, **dense table rows, form-field / input value** |
| Small/Strong | 12 | 20 | 600 | **table column headers** |
| **Base/Regular** | **14** | **22** | **400** | **BODY DEFAULT** |
| Base/Strong | 14 | 22 | 600 | emphasized body |
| Large/Regular | 16 | 24 | 400 | |
| Large/Strong | 16 | 24 | 600 | card title |
| Huge/Regular | 20 | 28 | 400 | |
| Heading/2 | 30 | 38 | 600 | KPI big numbers, page title |
| Heading/3 | 24 | 32 | 600 | section title |
| Heading/4 | 20 | 28 | 600 | |
| Heading/5 | 16 | 24 | 600 | |

Apply `font-size` + `line-height` + `font-weight` together. Icon size matches adjacent text (Base → 14px icon, Large → 16px). Data-dense rows / form fields drop to **Small (12px)**; table headers stay **Small/Strong (12px)**.

---

## 2. Ant Design v6 components first — no raw HTML

Use `antd` for anything it covers: `<Button>`, `<Input>`, `<Select>`, `<Table>`, `<Modal>`, `<Form>`+`<Form.Item>`, `<Menu>`, `<Card>`, `<Alert>`, `<Tag>`, `<Spin>`/`<Skeleton>`, `@ant-design/icons`. Never hand-build these with `<div>` / `<input>` / raw `<table>`.

---

## 3. One Primary CTA per screen

Exactly ONE `type="primary"` button per screen / modal / toolbar — the single most important action (Save, Submit, Create, Confirm, Next). Everything else = `default` / `link` / `text`; destructive = `primary danger`. Never leave a multi-button screen with no primary.

```jsx
<Space size={16}>
  <Button>Cancel</Button>
  <Button type="primary">Save</Button>
</Space>
```

---

## 4. Three-layer surface

```
Layer 3: #FFFFFF + --shadow-overlay           → Modal, Dropdown, Tooltip
Layer 2: #FFFFFF + 1px --color-border-secondary, radius 8px  → Card
Layer 1: --color-bg-layout (#F5F5F5)          → page canvas
```

Never put a white card on a white page.

---

## 5. ⭐ Data tables — the canonical table

Tables drifted more than any other QMAS surface. There is now **one** canonical table:
Figma `T9HItYJ1nxXEJPiVF5xdxM` → page `↪ Table` → section `Table — QMAS Composed Patterns & States`
(11 state frames + a spec card). Match it. Never invent your own table chrome.

### Anatomy

```
wrapper        flex column · padding 24 · gap 16 · background var(--color-bg-layout)
├── toolbar    flex row · height 40 · gap 16 · [title] [search] [spacer:1] [primary CTA]
├── filter bar flex row · height 48 · padding 13px 20px · gap 24 · radius 8 · --shadow-table
├── result / selection bar   (only while a search, filter or selection is active)
└── table card radius 8 · padding 0 · overflow hidden · --shadow-table
    ├── thead  height 48 · padding 14px 16px · 1px bottom border
    ├── tbody  rows height 78 (two-line identity cell) · padding 14px 16px · 1px bottom border
    │          last row before pagination has NO bottom border
    └── pagination row · padding 12px 16px · 1px TOP border · pagination pinned right
```

The table card has **padding 0** — the rows own the 16px side padding. Never wrap the table in
an extra padded container.

### Header is white with a bottom border — NOT a grey band

> ⚠️ **Changed in v2.** v1 specified a grey header band (`--color-fill-tertiary`). The approved
> eFiling table uses a **transparent header with a 1px bottom border** and header labels in
> `--color-text-tertiary`. That is now canonical, so light and dense tables read consistently.

### Column sizing — the rule that prevents re-layout work

Every column has a **fixed width except exactly one identity column, which flexes** (`flex: 1`).
Adding or removing a column then re-flows automatically. **Never hand-redistribute widths.**
If a value clips, widen that fixed column — the flexing column absorbs the difference.

### Type + colour by cell role

| Role | Style | Token |
|---|---|---|
| Column header | Small/Strong 12/20/600 | `--color-text-tertiary` |
| ID / name that links | Small/Regular 12/20/400 | `--color-link` |
| Supporting value | Small/Regular 12/20/400 | `--color-text-secondary` |
| Empty value — always an em dash `—`, never blank | Small/Regular | `--color-text-quaternary` |

### Components, not look-alikes

| Slot | Use | Never |
|---|---|---|
| Status | `<Tag bordered color="green\|orange\|gold\|red\|default">` | a hand-built pill or icon+text |
| Row action | a bare ellipsis icon (16px, `--color-text-secondary`) opening `<Dropdown>` | a per-row `<Button>` — it fights the page CTA |
| Selection | `<Table rowSelection>`; header goes indeterminate on partial selection | a hand-drawn checkbox |
| Pagination | Ant `<Pagination size="small" showSizeChanger>` in a top-bordered row | hand-built page numbers |
| Search | `<Input.Search size="large" enterButton="Search">` | a div with a magnifier in it |
| Empty / no results | `<Empty>` inside the card with the header row still visible | a blank card |
| Loading | `<Skeleton.Input>` per cell, checkbox column untouched | a full-page spinner |

### States to build

Default · Search focused · Search typing (suggestions, matched substring bold) · Search results
(result bar + corrected total) · No results · Filter dropdown open · Filter applied (active pill
`--color-primary`, `Reset` goes from `--color-text-quaternary` to a live link) · Sort dropdown
open · Row selection (rows `--color-primary-bg`, bulk bar above the card) · Loading · Empty.

Search placeholders must **name the fields they search** — `Search product name, item no. or
order no.`, never a bare "Search". Sorting a status column sorts by **workflow rank**, never
alphabetically. Cross-cutting filters live in **toolbar pills**; column menus are for per-column
refinement only — never ship both for the same field.

```css
.qmas-table-card, .qmas-filter-card {
  background: var(--color-bg-container);
  border: 1px solid var(--color-border-secondary);
  border-radius: var(--radius-container);
  box-shadow: var(--shadow-table);              /* light, not --shadow-card */
  overflow: hidden;
}
.qmas-table-card { padding: 0; }                /* rows own the padding */

.qmas-table thead th {
  background: transparent;                      /* v2: no grey band */
  color: var(--color-text-tertiary);
  font: 600 12px/20px Roboto;                   /* Small/Strong */
  height: 48px;
  padding: 14px 16px;
  border-bottom: 1px solid var(--color-border-secondary);
}
.qmas-table tbody td {
  background: transparent;
  color: var(--color-text-secondary);
  font: 400 12px/20px Roboto;                   /* Small/Regular, dense */
  padding: 14px 16px;
  border-bottom: 1px solid var(--color-border-secondary);
}
.qmas-table tbody tr:last-child td { border-bottom: 0; }
.qmas-table tbody tr[aria-selected="true"] td { background: var(--color-primary-bg); }
.qmas-table .cell-link  { color: var(--color-link); }
.qmas-table .cell-empty { color: var(--color-text-quaternary); }
```

---

## 6. Everything is flex/grid — no absolute positioning

Every container is a flex/grid box so content flows on the 8px grid. Group atomic clusters into one flex box: status chip `[icon+label]`, filter pill `[icon+text+caret]`, field `[label(+*)+control]` (column, gap 8), table row = flex row of fixed-width cells stacked in a flex column. Spacing `4 / 8 / 16 / 24 / 32` (4px for dense data). Widths use grow / fixed / hug, not magic pixels. Only true overlays (Modal, Dropdown, Toast, scrim) are absolutely positioned.

---

## 7. Radius & padding

Interactive (Button/Input/Select) → 6px; Container (Card/Modal) → 8px; dense → 4px; Tag → 2px. Card padding 24px standard, 16px for data-heavy cards, 0 for a table card (table owns its borders).

---

## 8. Text case — Title Case + acronyms

Title Case for: section titles, field labels, button labels, chips/tags, table column headers, nav items. Keep **original** case for descriptions, placeholders, and real data (names, emails, dates, IDs, supplier/module names). Emails & URLs always lowercase.

Acronyms stay ALL-CAPS inside Title Case: `QC, IQC, QMAS, TCF, PO, ID, ECD, PM`. e.g. `QC Pass Rate` ✅ / `Qc Pass Rate` ❌.

❌ Never use CSS `text-transform: capitalize` — it mangles acronyms (`QC`→`Qc`). Write the final cased string in source.

---

## 9. App Shell — Header + Left Nav required

Every authenticated page wraps content in `<Layout>` with a fixed **Header** (top, 44px, white) + fixed **Left-Nav Sider** (64px, navy). Skip only for login / auth pages (full-bleed, small centered logo, no shell) and overlays (Modal/Drawer/Popover leave the shell mounted underneath). Shell colours use the **Brand layer** (`--brand-*`), NOT the Ant tokens.

```jsx
import { Layout } from 'antd';
const { Header, Sider, Content } = Layout;

<Layout style={{ minHeight: '100vh' }}>
  <Sider width={64} collapsedWidth={64} collapsed
         style={{ background: '#1950AE' /* --brand-navy-400 */ }}>
    {/* left nav */}
  </Sider>
  <Layout>
    <Header style={{ background: '#FFFFFF', height: 44, padding: '0 24px' }}>
      {/* top bar: logo left; utility icons + user name (#232B3A) + email (#8991A0, lowercase) + avatar right */}
    </Header>
    <Content style={{ background: '#F5F5F5' /* --color-bg-layout */ }}>
      {children}
    </Content>
  </Layout>
</Layout>
```

---

## 10. ConfigProvider (app root)

```jsx
const qmasTheme = {
  token: {
    colorPrimary: '#1677FF', colorSuccess: '#52C41A',
    colorWarning: '#FAAD14', colorError: '#FF4D4F',
    borderRadius: 6, borderRadiusLG: 8, borderRadiusSM: 4, borderRadiusXS: 2,
    fontSize: 14, fontSizeLG: 16, fontSizeXL: 20,
    fontSizeHeading2: 30, fontSizeHeading3: 24, fontSizeHeading4: 20, fontSizeHeading5: 16,
    colorBgLayout: '#F5F5F5', colorBgContainer: '#FFFFFF',
    colorBorder: '#D9D9D9', colorBorderSecondary: '#F0F0F0',
    controlHeight: 32, controlHeightLG: 40, controlHeightSM: 24,
  },
  components: {
    Card: { borderRadiusLG: 8, paddingLG: 24 }, Button: { borderRadius: 6 },
    Input: { borderRadius: 6 }, Modal: { borderRadiusLG: 8 },
  },
};
```

---

## ✅ Pre-output checklist

1. Exactly ONE `type="primary"` per screen/modal; rest default/link/text.
2. Text sets size + line-height + weight from §1 (weight ≤ 600).
3. Colours use `--color-*` tokens — no `#000`, no white page bg, no arbitrary hex.
4. **Tables copied from the canonical section** — card padding 0, header 48h white + bottom border (NOT a grey band), rows 78h, exactly ONE flexing column, last row borderless, Tag for status, bare ellipsis for row actions, real Pagination — and the search / filter / sort / empty / loading / selection states this flow can reach are all designed.
5. Spacing on 8px grid; radius 6 interactive / 8 container.
6. Ant v6 components, not raw HTML; flex/grid, not absolute positioning.
7. Title Case with acronyms preserved; no `text-transform: capitalize`.
8. Page wrapped in shell (Header + 64px Sider) unless login/overlay; shell uses Brand layer colours.

Last synced: **2026-08-07** (v2 — rewrote §5 into the full **canonical data-table** spec: anatomy (wrapper 24/16 → toolbar 40 → filter bar 48 → optional result/selection bar → card with padding 0, header 48h, rows 78h, pagination row with a top border), the **one-flexing-column** sizing rule, per-cell type/colour table, a component substitution table, and the 11 required states including the filter / sort / search interaction states. ⚠️ **Behaviour change:** v1 mandated a grey header band (`--color-fill-tertiary`); the approved eFiling table uses a transparent header with a 1px bottom border and `--color-text-tertiary` labels, which is now canonical. Checklist item 4 rewritten. Everything else unchanged.)

Previous: **2026-07-23** (v1 — initial version. CSS/React edition of `qmas-design-system` v14.1, distilled for Claude design / HTML / React output: drop-in CSS custom-property tokens, Ant Design v6 component rules, type scale, table/filter shadow rule, App Shell, and the 8-point pre-output checklist. Figma-plugin-only material (component keys, node IDs, `setTextStyleIdAsync`/`setBoundVariableForPaint` binding code) intentionally omitted — see `qmas-design-system` for the Figma-plugin edition.)
