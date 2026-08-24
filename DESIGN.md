# DESIGN.md — Project Design System & Guidelines

Single source of truth for design context in **my-coffee-store**. Read this before designing or coding any UI. Where this file says `TBD`, that decision has not been made — do not invent a value to fill the gap.

## 0. Project reality check (read this first)

This is not a document reverse-engineered from an existing UI. It is written against the actual state of the repository as of 2026-08-02:

- **There is no frontend code.** No `package.json`, no `.css`/`.scss`, no `.tsx`/`.jsx`/`.vue`/`.svelte` file exists anywhere in the repo. Verified by full-tree search.
- **There is no logo, favicon, icon set, or any visual asset.**
- **No theme, color variable, font, spacing scale, or component has ever been written.**
- **[ADR-03](docs/02-design/02-technical/20260824-04-adr-revise-backend-go.md)** (`ยอมรับแล้ว`, 2026-08-24; supersedes the archived [ADR-01](docs/00-archived/20260802-01-adr-platform-stack.md)) has decided the platform: React + Next.js (TypeScript) frontend, a separate Go backend service (Gin, gorilla/websocket, sqlc/pgx), PostgreSQL, a Thailand-based VPS. It does **not** decide a component library — no component library has been chosen. The CSS/styling framework itself **is now decided — Tailwind CSS, see §2** — this paragraph is left as-is otherwise since ADR-03 itself doesn't cover styling.
- **The requirement specs under `docs/01-requirements/01-spec/`** (three documents, all still `status: ร่าง`) do describe real product behavior, screens, user roles, and several UX rules with the force of business rules. Those are cited below with their source path — they are the only real design-relevant facts that exist yet.

Consequently, many visual tokens in this document are still `TBD`. That is the correct state, not a defect of this document. **Filling in a token with an invented value would be a bigger problem than leaving it TBD** — it would get built into the first components and become expensive to unwind. As of 2026-08-24 the CSS approach (Tailwind CSS, §2), palette (§1), and type scale (§2) are decided; Shadows, Layout, and Responsive Breakpoints (§2) remain open and should be settled alongside or before the first Next.js scaffold.

---

## 1. Brand Identity & CI

### Brand Overview

| Field | Value | Source |
| --- | --- | --- |
| Product name | my-coffee-store (repo name; no product/trade name decided) | [README.md](README.md) |
| Product purpose | A single coffee shop's table self-ordering system: customers scan a per-table QR code, order and pay from their own phone, orders reach a barista screen | [20260802-01](docs/01-requirements/01-spec/20260802-01-table-self-ordering.md) §1–2 |
| Target users | ลูกค้าที่นั่งในร้าน (dine-in customer, via phone), บาริสต้า (barista screen), พนักงานหน้าร้าน (staff, menu/order admin), เจ้าของร้าน (owner, menu/pricing/staff accounts) | same, §3 |
| Brand personality | `TBD` — no brand voice, tone, or personality has been defined anywhere in the repo |
| Design direction | `TBD` — no visual direction has been chosen or discussed |

**UI language: `TBD` and flagged as a gap.** All *documentation* in this repo is written in Thai by repo convention ([CLAUDE.md](CLAUDE.md)), but no spec states what language the customer-facing app itself must be in. Since the customer is a walk-in at a Thai coffee shop, Thai is the likely answer — but it is not decided, and English-only or bilingual are both still open. **Do not default the app UI to Thai based on the docs convention alone; get this confirmed before building the first screen.**

### Logo

`TBD` — no logo file, mark, or usage guideline exists in the repository.

### Brand Colors — decided 2026-08-19 (first pass; see note)

> **This is a working decision, not a brand-owner-approved identity.** No one
> claiming ownership of "my-coffee-store" as a brand has weighed in — there is
> still no logo, no company beyond the repo name. This palette exists so that
> UI work can proceed without inventing colors ad hoc per screen (the failure
> mode Rule 1 exists to prevent); treat it the way ADR-01 was treated before
> acceptance — usable now, but open to being overridden by whoever actually
> owns the shop's branding.

Grounded in the one real constraint already on record (§ Visual Direction
below): legible under glare, fast to read one-handed, calm rather than
decorative. A warm, coffee-derived palette rather than a generic SaaS blue —
this is a food-service counter tool, not enterprise software.

| Token | Hex | Use |
| --- | --- | --- |
| `color.primary` | `#6F4E37` | Primary actions — confirm order, save menu edit |
| `color.secondary` | `#C8A27A` | Secondary emphasis, selected states |
| `color.accent` | `#E08D3C` | Sparingly — highlights, active tab indicator |
| `color.background` | `#FAF6F0` | Page background — warm off-white, not stark white |
| `color.surface` | `#FFFFFF` | Cards, table rows, modals sitting on background |
| `color.text.primary` | `#2B211B` | Body text — warm near-black, not pure `#000` |
| `color.text.secondary` | `#6B5D54` | Secondary/muted text, captions |
| `color.border` | `#E4DDD3` | Dividers, table borders, input outlines |
| `color.success` | `#2E7D32` | Payment succeeded, order confirmed |
| `color.warning` | `#B45309` | Sold-out-mid-selection, non-blocking caution |
| `color.error` | `#C62828` | Payment failed, permission denied (Alert, §3) |
| `color.info` | `#1D6FA5` | Neutral informational messages |

Deliberately kept `warning` and `accent` visually distinct (amber-brown vs.
orange) — accent is decorative, warning is a signal; conflating them would
make a caution message look like a stylistic choice.

### Iconography

`TBD` — no icon library, style, or sizing rule has been chosen. Colors above
don't imply an icon set; leave this open rather than guessing one to match.

### Visual Direction

Confirmed direction, same reasoning as before now with color applied: the
ordering flow must be fast and legible for a customer standing at a table on
their own phone (§1 of `20260802-01`), and the barista screen must be
scannable at a glance under time pressure (US-5, `20260802-01`). **Legible,
low-friction, high-contrast-under-glare, calm rather than decorative** — still
worth validating with whoever ends up owning the brand, but no longer purely
an inference with nothing built on it.

---

## 2. Design Tokens / Design System

No token in this section has a real value yet. The table below exists so that **when values are decided, they land in one place** rather than being invented ad hoc per component.

### CSS / Styling Framework — decided 2026-08-24

**Tailwind CSS**, configured via `tailwind.config.ts` `theme.extend` — the
color/spacing/radius/typography tokens already decided below become the
theme's source values directly (e.g. `theme.extend.colors.primary =
"#6F4E37"`), not a separate hand-rolled variable layer to keep in sync.

| Option | Verdict | Why |
| --- | --- | --- |
| **Tailwind CSS** | **Chosen** | Ships as the default styling option in `create-next-app` and has first-class App Router/Server Component support with no client-boundary tax — a plain compiled stylesheet, not a runtime. Utility classes suit this project's shape well: many small, structurally similar components (Badge, chip-style Select, Button variants) that each need a handful of the same tokens applied slightly differently, not large bespoke page layouts. Matches the same "boring, widest docs, small-team-friendly" reasoning [ADR-03](docs/02-design/02-technical/20260824-04-adr-revise-backend-go.md) already used to pick Gin and gorilla/websocket on the backend |
| CSS Modules | Rejected | Built into Next.js with zero extra dependency, but gives no utility layer — every component would hand-write its own class rules even though most of them just apply the same handful of tokens (a border, a radius, a padding pair). More boilerplate for this project's actual shape, not less |
| styled-components / Emotion (CSS-in-JS, runtime) | Rejected | Runtime CSS-in-JS needs explicit client-boundary opt-outs to work with the App Router's React Server Components, adding friction ADR-03's Next.js choice didn't sign up for; also a runtime cost Tailwind's compiled-CSS approach avoids entirely |
| vanilla-extract / Panda CSS (zero-runtime CSS-in-JS) | Rejected | Technically reasonable (compiled, type-safe), but far smaller community/docs than Tailwind — this repo has consistently favored the option with the widest available documentation for a small team over the more novel one (same call as gorilla/websocket over coder/websocket in ADR-03) |

No component library (e.g. shadcn/ui, Radix, MUI) is chosen alongside this —
that's a separate, still-open decision (§1 notes it explicitly). Tailwind by
itself only styles; it doesn't supply component primitives.

### Colors — decided 2026-08-19, see §1 Brand Colors for the full reasoning

```text
color.primary          #6F4E37
color.secondary        #C8A27A
color.accent           #E08D3C
color.background       #FAF6F0
color.surface          #FFFFFF
color.text.primary     #2B211B
color.text.secondary   #6B5D54
color.border           #E4DDD3
color.success          #2E7D32
color.warning          #B45309
color.error            #C62828
color.info             #1D6FA5
```

No `tailwind.config.ts` exists yet — there is still no frontend code in the
repo (§0). When the Next.js app is scaffolded, wire these into
`theme.extend.colors` there first — this table is the source of truth for the
*values*, not a substitute for wiring them into the actual config.

### Typography — decided 2026-08-19

**UI language is still not formally confirmed** (§1 flags this as an open
gap) — but every spec and every screen designed so far is Thai, and the
customer is a walk-in at a Thai shop, so the typeface must render Thai well
regardless of how that question resolves. Chosen with that in mind, not as a
decision that Thai has been locked in.

| Token | Value |
| --- | --- |
| Font family | `Sarabun` |
| Fallback | `"Noto Sans Thai", "Segoe UI", sans-serif` |
| Source | Google Fonts (`fonts.googleapis.com`) — widely used for Thai UI, full Thai + Latin coverage, legible at small sizes |

| Style | Size | Weight | Line height | Letter spacing |
| --- | --- | --- | --- | --- |
| Heading (h1) | 28px | 600 | 1.3 | normal |
| Heading (h2) | 22px | 600 | 1.35 | normal |
| Body | 16px | 400 | 1.5 | normal |
| Label | 14px | 500 | 1.4 | normal |
| Caption | 12px | 400 | 1.4 | 0.01em |
| Button | 16px | 600 | 1 | 0.01em |
| Table | 15px | 400 | 1.4 | normal |
| Form (input text) | 16px | 400 | 1.4 | normal |

16px is the floor everywhere text carries meaning (body, button, form,
barista queue) — the functional constraint already on record (mobile,
variable lighting, one-handed use, glance-under-pressure) argues against
anything smaller for content a customer or barista actually reads to act on.
Caption is the one deliberate exception, for secondary/de-emphasized text only.

### Spacing — decided 2026-08-19 (minimum needed to give Badge/Button/Alert a real treatment)

```text
space-1    4px
space-2    8px
space-3    12px
space-4    16px
space-6    24px
space-8    32px
space-12   48px
space-16   64px
```

Standard 4px base, as this document originally proposed. Button padding uses
`space-3 space-4` (12px/16px vertical/horizontal); a compact variant uses
`space-2 space-3`.

### Border Radius — decided 2026-08-19

```text
radius-sm     6px    inputs
radius-md     8px    buttons, alerts, cards
radius-lg     14px   sheets/overlays (e.g. the cookie consent panel)
radius-full   999px  pills — badges, switches
```

### Shadows

`TBD` — still not needed to give Badge/Button/Alert a real treatment (all three
read fine flat, per §3 below); left open rather than decided just to fill the
section. Decide when a component genuinely needs elevation (a modal, a
dropdown) — don't invent a shadow scale ahead of that need.

### Layout

`TBD` for all values (container width, grid, sidebar, header, content spacing). Note one structural fact from the specs: this system has at least **two distinct layout contexts with different constraints** — the customer-facing order flow (phone, portrait, one-handed) and the barista/staff screens (likely tablet or desktop, viewed at a distance, `20260802-01` US-5). Whatever layout tokens are chosen should be defined per context rather than assuming one layout serves both.

### Responsive Breakpoints

`TBD` — no breakpoint values exist. What is implied but not decided:

| Context | Likely device | Source |
| --- | --- | --- |
| Customer ordering (BL-002, BL-003) | Phone, portrait — QR scan opens on the customer's own phone | `20260802-01` §2, Q1 (ตัดสินใจแล้ว) |
| Barista screen (BL-005) | Larger screen, viewed at a glance while working | `20260802-01` US-5 |
| Staff/owner menu & account admin (BL-001, BL-006) | Not specified | — |

"Likely device" is not a breakpoint decision — treat it as a hint for whoever designs the first screens, not as settled.

---

## 3. UI Components & Patterns

**No component exists in code anywhere in this repository** — verified by a full-tree search for `.tsx`/`.jsx`/`.vue`/`.svelte`/`.css`/`.scss`, none found. Nothing below can be "the existing Button," because there is no such file. Every component in this section is therefore documented in one of two states:

- **Required** — the specs name a real need for this pattern; source cited; Purpose/Usage are known, everything visual is `TBD`.
- **Not yet required** — no spec calls for this pattern yet; listed only so the catalogue is complete and nobody invents it prematurely for a screen that doesn't need it.

For every component below — required or not — **Variants, States (beyond what's cited), Responsive behavior, and Accessibility are `TBD`** until it is actually designed. Do not pre-fill these to look complete.

### Navigation

- **Header** — Not yet required. No screens designed yet.
- **Sidebar** — Not yet required. Likely relevant to staff/owner admin (BL-001, BL-006) once that area is designed, not to the customer phone flow.
- **Breadcrumb** — Not yet required. The customer flow (QR → menu → cart → pay) is linear per `20260802-01`, which argues against needing breadcrumbs there; staff admin is undesigned.
- **Tabs** — Not yet required.
- **Pagination** — Not yet required yet, but foreseeable: a full menu or an order-history list could need it. No spec currently calls for it.

### Actions

- **Button** — **Required.** Every confirm/pay/toggle action in `20260802-01` needs one (US-2 through US-7). One binding constraint already fixed by requirement, not by visual design: in the cookie banner, the reject button must be *at least as* prominent as the accept button (`20260802-03` BR-4) — this is a UI requirement, not a suggestion.

  **Decided 2026-08-19:**

  | Property | Value |
  | --- | --- |
  | Variants | **Primary** — `color.primary` background, `color.primary-ink` (white) text. **Ghost** — transparent background, `color.text.primary` text, `color.border` outline. No third variant yet — don't add one without a screen that needs it |
  | Size (default) | `space-3 space-4` padding (12px/16px), 16px/600 weight text (Button token, §2 Typography) |
  | Size (compact) | `space-2 space-3` padding (8px/12px), for dense contexts like a table row action |
  | Radius | `radius-md` (8px) |
  | Hover | Primary: background brightened slightly. Ghost: `color.background`-tinted fill |
  | Focus | 2px outline in `color.info`, 2px offset — visible, not just a color change, for keyboard use |
  | Disabled | 50% opacity, no pointer cursor, no hover/focus effect |
  | Loading | Not decided — no spinner/icon system exists yet; don't invent one to fill this row |

  The reject-vs-accept prominence rule (BR-4) is satisfied by **never pairing
  Primary with Ghost for a binary accept/reject choice** — both sides of a
  reject/accept pair use the same variant (both Primary, or both Ghost),
  differing only in label. A Primary "accept" next to a Ghost "reject" would
  violate BR-4 by construction, regardless of size.
- **Icon Button** — Not yet required.
- **Dropdown** — Not yet required.
- **Menu (contextual)** — Not yet required.

### Forms

- **Input** — **Required** for menu item fields (name, price) in staff admin (BL-001), for account fields (BL-006), and for the customer's order note to the barista (`20260802-01` §2, in scope — free text, not a fixed choice like §3's Select). Price-specific validation is already decided (§ above); general validation style still `TBD`.

  **Decided 2026-08-19:**

  | Property | Value |
  | --- | --- |
  | Container | Bordered box, `radius-sm` (6px), `color.border` outline, `color.surface` background — not underline-only, so the field reads as a container on both the cream customer background and the white staff screens |
  | Label | Always visible above the field, never placeholder-as-label — a placeholder that disappears on typing isn't a label, and this repo already has an open accessibility gap (§4) that a disappearing label would make worse, not better |
  | Text | 16px (Form token, §2 Typography) — the same floor as everywhere else text is acted on |
  | Focus | 2px outline in `color.info`, 2px offset — same treatment as Button, so focus reads consistently across every interactive element |
  | Error | Border turns `color.error`; message in Caption size, `color.error`, directly below the field — matches how the menu price rule (§ above) already said errors should appear |
- **Select** — **Required** for drink options (size, hot/cold, sweetness, toppings) in the customer order flow (US-2).

  **Decided 2026-08-19: button-group / chip selector, not a native `<select>`
  dropdown.** Every option set here is small (2–5 choices: size, hot/cold/
  blended, most topping lists) and this is the customer's mobile,
  one-handed, fast-ordering context — the constraint already on record since
  §1. A native dropdown costs an extra tap-then-scroll-then-tap for a choice
  that a single row of visible buttons shows and resolves in one tap. Chips
  use `radius-full` (matching Badge's pill shape), selected state uses
  `color.primary` fill, unselected uses `color.surface` with `color.border`
  outline.

  **Left open:** "ระดับความหวาน" (sweetness) — the spec doesn't say whether
  this is a small discrete set (มาก/กลาง/น้อย) or a percentage scale (0–100%,
  as some real coffee menus use). A discrete set fits the chip pattern above;
  a percentage scale would need a slider instead, which is a different
  control this decision doesn't cover. Confirm which before building this
  specific option.
- **Checkbox** — **Required** for the cookie consent banner's per-category toggles (`20260802-03` BR-2, BR-3). Binding rule: "วิเคราะห์" and "การตลาด" must default **unchecked** — this is fixed by BR-3, not a visual choice. **Confirmed as Checkbox, not Switch — decided 2026-08-19**, see the rule below.
- **Radio** — Not yet required by any current spec.
- **Switch** — **Required** for the menu item "sold out" toggle (BL-001, US-6). **Decided 2026-08-19: Switch, not Checkbox** — see the rule below.

**Switch vs Checkbox — decision (2026-08-19):** the spec text itself mixes
metaphors (BR-2 says the necessary cookie category "เปิดตลอด...ปิดไม่ได้" —
on/off language; BR-3 says the other categories must not be "ติ๊กมาให้ล่วงหน้า"
— tick language) so wording isn't a reliable signal either way. Decided by
interaction shape instead:

| Use **Switch** when... | Use **Checkbox** when... |
| --- | --- |
| The control takes effect immediately, alone, with no separate submit step | The control is one of several choices gathered together and confirmed by an explicit action |
| Example: **menu sold-out toggle (BL-001)** — journey confirms it changes on-screen the instant it's tapped, no confirmation | Example: **cookie category consent (BL-014)** — categories sit together and are confirmed via "ยอมรับทั้งหมด" / "ปฏิเสธทั้งหมด" / a save action, matching how consent forms are read elsewhere |

Same shape as the Table-vs-Card rule: not a single blanket pick, because the
two use cases genuinely behave differently — one is a live state flip, the
other is a batched choice. The earlier UI mockup used a switch-styled control
for the cookie banner, which this decision makes visually incorrect there —
it should render as checkboxes; noted as a fix rather than silently left.
- **Date Picker** — Not yet required by any current spec.
- **Search** — Not yet required by any current spec.
- **Validation** — `TBD` in general (style, message pattern, on-blur vs. on-submit timing — no spec addresses any of this). **One concrete instance decided 2026-08-19: menu price**, see below. Treat this as the first real answer to the general question, not a template stamped onto every future field — the next field validated should still be reasoned through, not copy-pasted from this one.

  **Menu price rule (first-pass business policy, not spec-derived — revisable
  like the Brand Colors decision):** nothing in `20260802-01` sets a price
  format or bound; BR-4/AC-9 only govern *when* a price change takes effect,
  not what values are valid. Decided:

  | Rule | Value | Reasoning |
  | --- | --- | --- |
  | Currency | Thai Baht (฿), whole numbers only | Every price in every spec, mockup, and prior decision uses whole Baht (฿45, ฿55...); satang subdivision is essentially unused in Thai food retail |
  | Minimum | Must be **greater than 0** | Not a style choice — a ฿0 item has nothing for BL-004's payment step to charge. This is a technical necessity the flow depends on, not a preference |
  | Maximum | ฿2,000 (soft ceiling) | Generous enough for any real coffee-shop item; exists to catch a fat-finger entry (typing 4500 instead of 45), not to constrain legitimate pricing |
  | Required | Name and price both required to save | A menu row with a blank price or name isn't a valid row to persist |
  | Message timing | On submit (when "บันทึก" is pressed), not on every keystroke | Matches the rest of this screen's interaction — the sold-out Switch already acts without a submit step (§3), so the one field that *does* need a submit step should say so clearly rather than validating live like the Switch does |

### Data Display

- **Table** — **Required** for staff/owner menu management (BL-001) and any future admin-style record list (order-status review, spec §3). ~~and the barista order queue (US-5, BL-005)~~ — **decided 2026-08-19: not for the barista queue**, see the Table-vs-Card rule below.

  **Decided 2026-08-19:**

  | Property | Value |
  | --- | --- |
  | Header | 12px, weight 600, uppercase, `0.02em` letter-spacing, `color.text.secondary`, `color.border` bottom rule separating it from the body |
  | Row | `color.border` bottom rule between rows — **no zebra striping**, consistent with how Card also uses a border rather than an added surface tint (§ above) |
  | Cell text | Table token, §2 Typography (15px/400) |
  | Numeric columns (price, counts) | **Right-aligned**, `tabular-nums` — so a column of prices lines up on the decimal/units digit instead of ragging left |
  | Action column | Right-most column, one action per row (e.g. "แก้ไข") — a row needing more than one action is a sign it should show a menu, not more inline buttons, but no spec has hit that case yet |
  | Row hover | `color.background` tint (the page background color, reused — not a new token) — a plain visual cue for which row the pointer is over, independent of whether that row's action is visible |

  **Sort: not implemented.** No spec asks for it, and the only table that
  exists (menu management) is a small, hand-curated list — sorting a
  10–30-row list a person built by hand doesn't solve a real problem yet.
  Decide this when a table's row count or turnover actually makes unsorted
  scanning slow, not ahead of that.

  **Filter/Search: not implemented, same reasoning.** The most likely future
  table to need it is "order-status review" (spec `20260802-01` §3, not yet
  built) once it spans enough days/orders that browsing beats searching — but
  that screen doesn't exist yet, so deciding its filter behavior now would be
  designing for a screen nobody has specified.
- **Card** — **Required** for the customer-facing menu item list (US-2) **and the barista order queue (US-5, BL-005) — decided 2026-08-19**, see the Table-vs-Card rule above for which screens use it.

  **Container decided 2026-08-19:** `color.surface` background, `color.border`
  1px outline, `radius-md` (8px), `space-4` (16px) padding. Flat — no shadow.
  Deliberately **no second "tinted surface" tone** added just to separate a
  card from a white screen background — the border alone is the separation
  mechanism, matching how Table already separates rows (border lines, not
  zebra striping) and Alert/Badge (flat, tinted only for semantic meaning,
  not for layering). If a border genuinely proves too subtle once real
  screens exist, that's a call to make from an actual screenshot, not a
  token to pre-invent now.

  **Internal layout stays per-use** (this is *why* Table-vs-Card chose Card
  for these two screens in the first place — variable content, not uniform
  rows): the menu item card (image, name, price, options summary) and the
  barista order card (table number, item list, action button) keep their
  own internal layouts, already specified in each screen's prototype
  document — this entry only fixes the shared container.

**Table vs Card — decision (2026-08-19):** the ambiguity wasn't "which one does this repo use" — it was two genuinely different data shapes wearing the same name. Resolved as a rule, not a single blanket pick:

| Use **Table** when... | Use **Card** when... |
| --- | --- |
| Rows are uniform — same fields, one line each (e.g. menu item: name, price, status, action) | Each item has variable-length or multi-part content (e.g. an order: table number, several line items, options, notes) |
| The screen is admin/record-management — benefits from scanning a column, sorting, comparing rows | The screen must be read at a glance under time pressure — DESIGN.md §1 already inferred the barista screen needs exactly this |
| Example: **menu management (BL-001)** | Example: **barista order queue (BL-005)** |

This isn't "Table for staff, Card for customers" — both examples above are staff-facing. The distinguishing factor is the shape and urgency of the content, not who's looking at it. Apply this rule to the next list-shaped screen rather than re-opening the question; if a screen doesn't clearly fit either row in the table above, that's worth raising again, not silently defaulting to one.
- **Badge** — **Required** for order status (e.g., "รับแล้ว" / "เสร็จแล้ว", `20260802-01` §2) and for "sold out" marking on a menu item.

  **Decided 2026-08-19:** pill shape (`radius-full`), `space-1` (4px) vertical
  padding / roughly `space-2`–`space-3` horizontal, 12px text at 500 weight
  (Caption token, §2 Typography). Semantic meaning drives color — a badge is
  **never** decorative, it always signals a real state:

  | Meaning | Background | Text |
  | --- | --- | --- |
  | Success (e.g. "เสร็จแล้ว") | `color.success` tinted ~12% over `color.surface` | `color.success` |
  | Warning (e.g. "ของหมด", "รับแล้ว" while still pending) | `color.warning` tinted ~12% over `color.surface` | `color.warning` |
  | Neutral/informational count (e.g. "3 ใบรอทำ") | `color.border` | `color.text.secondary` |

  A tinted background rather than a solid semantic fill — a solid `color.error`
  badge sitting next to body text would compete with real error Alerts for
  visual weight; the tint keeps a badge legible without it reading as urgent
  as an Alert.
- **Tag** — Not yet required.
- **Avatar** — Not yet required — no user-facing profile concept exists in any spec.
- **Tooltip** — Not yet required.
- **Statistics / KPI** — Not yet required — `20260802-01` explicitly puts sales reporting/analytics **out of scope** for this phase.

### Feedback

- **Alert** — **Required** for the sold-out-mid-selection warning (`20260802-01` §7), the offline/no-signal message, and the Permission State layer-2 backstop (§ above).

  **Decided 2026-08-19: inline, not toast or banner.** Every current use is
  attached to a specific moment on a specific screen — a cart item, a
  permission attempt, a connectivity check — not a global system-wide
  announcement. A toast implies transient and dismissible, wrong for
  something the user must read and act on (e.g. remove a sold-out item
  before paying); a top-of-page banner implies persistent and general, wrong
  for a one-off contextual warning. Inline, next to what triggered it, matches
  all three cases without inventing a fourth mechanism.

  | Property | Value |
  | --- | --- |
  | Radius | `radius-md` (8px) |
  | Padding | `space-3` (12px) |
  | Background/text | Semantic pair matching severity — `color.error`/error-tint for permission denial and payment failure, `color.warning`/warning-tint for sold-out-mid-selection, `color.info`/neutral-tint for offline/no-signal (informational, not the user's fault) |
  | Dismissal | **No auto-dismiss** when the Alert gates a required decision (remove a sold-out item, retry a denied action) — it stays until the underlying state resolves. Only a purely informational Alert with no required follow-up may be dismissed by the user |
- **Toast / Snackbar** — Not yet decided as the mechanism for any of the above — could be Toast, could be Alert, could be inline. This choice is explicitly open (see §4, Notifications).
- **Modal** — **Required**, likely for payment confirmation (US-3) and for the consent preference center (`20260802-03` BL-016) — whether these use Modal vs. Dialog vs. a full-screen step is `TBD`.
- **Dialog / Confirmation Dialog** — **Required** for any destructive or hard-to-undo staff action (e.g., cancelling an order, removing a menu item) per general UX good practice — no spec currently enumerates which actions need confirmation; this is a real gap, see §4.

### System States

- **Loading** — **Required implicitly** (payment processing, order submission per US-3) but no visual pattern designed. `TBD`.
- **Skeleton** — Not yet decided as the loading pattern; plain spinner vs. skeleton is an open choice.
- **Empty State** — **Required implicitly** (e.g., barista queue with no pending orders; menu list before staff adds items) but no pattern designed. `TBD`.
- **Error State** — **Required**, and partially specified at the behavior level (not visual) in `20260802-01` §7 — see the table in §4 below. The visual treatment of each is `TBD`.
- **Success State** — **Required implicitly** (payment succeeded, order sent) but no pattern designed.
- **Permission State — decided 2026-08-19, two layers, not a dedicated screen:**

  1. **Preventive (default).** Hide or disable the control entirely for a role
     that can't use it — don't render an "แก้ไข" button for non-owner staff at
     all. This is the normal path for every permission boundary in this repo
     (BL-006 owner vs. staff; PDPA BR-8 least-privilege access). It avoids a
     confusing dead end where a user taps something only to be told no, and it
     doesn't weaken AC-8/AC-9's "ระบบต้องปฏิเสธ" — the backend still rejects
     the action regardless of what the UI shows, so hiding the control is a
     UX improvement on top of the enforcement, not a replacement for it.
  2. **Reactive (backstop).** If a denied action is attempted anyway — stale
     UI state, direct navigation, a race between role change and page load —
     the backend rejects it and the UI surfaces a lightweight inline **Alert**
     at the point of the attempt (reuse the existing Alert component; do not
     invent a new "Permission Denied" screen or Dialog for this). Message
     states that the action needs a higher permission level; it does not
     expose implementation or security detail.
  3. **Always, regardless of which layer caught it:** log the attempt
     server-side per PDPA BR-9/AC-9 (spec `20260802-02`) — this is a backend
     requirement independent of what the UI displays, and applies whether the
     control was hidden and someone reached the endpoint directly, or the
     Alert fired.

  A full-screen or modal "access denied" experience was considered and
  rejected: it breaks flow for what should be a rare, recoverable event once
  layer 1 is in place, and nothing in the specs asks for that much ceremony.
- **Offline / Network Error** — **Required**, behavior fixed by `20260802-01` §7 ("มือถือลูกค้าไม่มีอินเทอร์เน็ต" → show a clear message, suggest ordering at the counter). Visual pattern `TBD`.

---

## 4. UX Guidelines & Rules

Unlike the sections above, this section is **not** mostly TBD — the requirement specs already fix a number of UX rules as binding business rules (`BR-*`) and acceptance criteria (`AC-*`), not as design suggestions. Treat every rule below as a constraint, not a starting point to redesign.

### Consent / cookie banner — fixed by `20260802-03`

- **Default state:** "วิเคราะห์" and "การตลาด" categories must default to **off / not consented**. Pre-ticking either is a compliance violation, not a design choice. (BR-3)
- **Reject must be as easy as accept:** the reject control must be at least as visible and at least as easy to activate as the accept control. Designing a small/greyed-out reject next to a prominent accept button is explicitly disallowed. (BR-4)
- **Rejecting cookies must never block ordering.** If a UI flow makes analytics/marketing consent a prerequisite to completing an order, that flow is wrong regardless of how it looks. (BR-5)
- **"จำเป็น" (necessary) cookies are always on, cannot be turned off**, and must be limited to what the system genuinely cannot function without — this category cannot be used to sneak analytics in under a friendlier label. (BR-2)

### System states already implied by the specs

These are documented as required *system behavior* in `20260802-01` §7 (กรณีข้อผิดพลาดและกรณีขอบ) — a UI that doesn't surface them fails the spec, independent of visual design:

| Trigger | Required behavior | What this implies for UI |
| --- | --- | --- |
| Double-tap confirm | One order only created, no double charge | Disable/debounce the confirm action after first tap |
| Payment succeeds but order-save fails | Must recover or alert staff — **never silent** | A visible error/recovery state is mandatory, not optional |
| Menu item goes sold-out mid-selection | Warn at confirm, let customer remove item before paying | An explicit "item unavailable" state in cart/checkout |
| Customer's phone loses signal | Show a clear message, suggest ordering at the counter | An offline/network-error state, with a fallback instruction |
| Barista screen disconnects and reconnects | Must backfill all pending orders, none dropped | Reconnect/resync must be visible or automatic, not silent |

Loading, empty, and permission states are **not** specified anywhere yet — those remain genuinely open design work, distinct from the states above which are already fixed by requirements.

### Forms, Tables, CRUD, Notifications, Accessibility, general responsive rules

`TBD` in full. Nothing in the current specs constrains form validation style, table interaction patterns, notification placement (toast vs. modal vs. inline), or general responsive behavior beyond the two device contexts noted in §2. **Accessibility in particular has no requirement anywhere in this repo** — no WCAG target, no keyboard-navigation rule, no screen-reader consideration was found in any spec or design document (verified by search). This is a real gap, not an oversight of this document, and is called out again in the final report below.

---

## 5. AI Design Rules

These rules apply regardless of how empty §1–3 are today — in fact they matter *more* while the system is empty, because the first component built sets the pattern everything after it copies.

1. **Reuse existing component before creating a new one.** Right now almost nothing exists to reuse (§3) — which makes the *next* rule matter more, not less.
2. **Reuse existing token before creating a new one.** No token exists yet (§2) — a token gap is a decision to make, not a default to invent.
3. **Do not invent colors.** Every color in §2 is `TBD`. If a screen needs a color and none is defined, that is a stop sign, not permission to pick one.
4. **Do not invent typography.** Same as above — no font, size, or weight has been decided.
5. **Do not invent spacing.** No spacing scale exists (§2). Don't hardcode a pixel value "because it looks right."
6. **Do not create one-off styling without justification.** There is no system yet to accidentally deviate from — there is only the system being created right now. The first arbitrary padding value or one-off color *is* the precedent everything after it copies.
7. **Follow existing UI patterns.** Before adding a page or feature: check `docs/01-requirements/01-spec/` for the governing requirement, check this file for any token/pattern that already exists, check `docs/02-design/02-technical/` for the accepted architecture (currently: ADR-01 only), then build. Do not design against a guess at what "a coffee ordering app" should look like — build against the specs cited in §3–4.
8. **Every interactive component must consider relevant states.** Default / Hover / Focus / Active / Disabled / Loading / Error / Success / Empty, as applicable. Cross-reference §4's table of specification-mandated states — several (double-submit, payment-fail, sold-out-mid-order, offline, reconnect) are not optional polish; they are acceptance criteria in `20260802-01`.
9. **Every new page must consider responsive behavior** for both known device contexts — customer phone flow and staff/barista larger-screen flow (§2) — even though neither has fixed breakpoints yet. "Desktop-only for now" is not an acceptable default per this repo's UX rules (§4).
10. **Update `DESIGN.md` when a Design System decision changes.** Add the decision to §7 in the same change, and correct any `TBD` that the decision resolves — don't let this file describe a state the code has moved past.

---

## 6. Design → Code Mapping

```text
Design Token
    ↓
CSS Variable / styling primitive
    ↓
Component
    ↓
Page
```

No mapping can be populated yet — there is no CSS variable, no component file, and no page in the repository. This section is a placeholder structure for when the first design tokens and first component exist; fill it in with real paths at that point, not with a hypothetical example.

---

## 7. Design Decisions

| Decision | Reason | Date |
| --- | --- | --- |
| ~~Platform: TypeScript + Next.js, PostgreSQL, long-running Node, Thailand VPS~~ — superseded, see next row | See archived [ADR-01](docs/00-archived/20260802-01-adr-platform-stack.md) for the original reasoning | 2026-08-02 |
| Platform (current): React + Next.js (TypeScript) frontend, separate Go backend (Gin, gorilla/websocket, sqlc/pgx), PostgreSQL, Thailand VPS | User specified Go for the backend; ADR-01 archived and replaced — see [ADR-03](docs/02-design/02-technical/20260824-04-adr-revise-backend-go.md) for full reasoning | 2026-08-24 |
| ~~No CSS/styling framework chosen yet~~ — decided, see next row | Deliberately deferred at the time — not part of ADR-01/ADR-03's scope | 2026-08-02 |
| CSS/styling framework: **Tailwind CSS**, tokens wired via `theme.extend` | Default Next.js App Router styling option with no client-boundary/runtime cost, and suits this project's many small structurally-similar components; same "widest docs, small-team-friendly" reasoning ADR-03 used for Gin/gorilla — see §2 for the full alternatives-considered table | 2026-08-24 |
| Cookie consent: 3-category banner, reject-as-easy-as-accept, off-by-default | Legal requirement (PDPA), not a design preference — see `20260802-03` BR-1–5 | 2026-08-02 |
| This document written mostly `TBD` rather than invented | No frontend code, brand, or visual asset exists to analyze; inventing values would misrepresent this as a settled system | 2026-08-02 |
| Table vs Card: rule by data shape, not a single blanket pick — Table for uniform/admin rows (menu management, BL-001), Card for variable/glance-under-pressure content (barista queue, BL-005) | Two independent prototype audits (BL-001, BL-005) hit the identical undecided component — confirmed it was a system-level gap, not a per-feature one. A single fixed choice would have fit one screen and forced the other, since the barista queue's per-order content (multiple line items, options) doesn't sit in one table row the way a menu item does | 2026-08-19 |
| Permission State: hide the control by default, Alert as backstop, no dedicated denial screen | AC-8/AC-9 require the system to reject an unauthorized attempt (and, for PDPA, log it) — they don't require a full denial UI. Hiding first avoids a confusing dead end; a screen or modal for a rare, recoverable event was considered and rejected as more ceremony than the requirement calls for | 2026-08-19 |
| Brand Colors: warm coffee-derived palette (see §1) — first-pass, not brand-owner-approved | Requested UI mockups need real values per Rule 1 (don't invent silently); no brand owner has weighed in yet, so this is usable-now-not-final, the same posture ADR-01 had before acceptance | 2026-08-19 |
| Typography: Sarabun + Noto Sans Thai fallback, 16px floor for anything read to act on | Every spec and screen so far is Thai though the UI-language question is still open; 16px floor follows directly from the mobile/glance-under-pressure constraints already on record, not a new assumption | 2026-08-19 |
| Switch vs Checkbox: rule by interaction shape — Switch for immediate no-submit state changes (menu sold-out), Checkbox for batched choices confirmed by an action (cookie consent categories) | Spec wording mixes on/off and tick language for the two cases, so text wasn't a reliable signal; the menu toggle changes instantly with no confirm step while cookie categories are gathered and confirmed together, matching each control's normal convention | 2026-08-19 |
| Menu price validation: whole Baht, >0, ฿2,000 soft ceiling, name+price required, validate on submit — first-pass business policy, not spec-derived | Nothing in `20260802-01` sets a price bound; BR-4/AC-9 only govern timing of a price change, not valid values. Minimum >0 is a technical necessity (BL-004 has nothing to charge otherwise); the rest is a reasoned starting policy, marked revisable the same way Brand Colors was | 2026-08-19 |
| Spacing (4px-based scale) and Border Radius (sm/md/lg/full) decided as a prerequisite | Badge/Button/Alert can't have a real treatment without them — same reasoning as deciding Colors/Typography before the first mockup | 2026-08-19 |
| Button: Primary + Ghost only, reject/accept pairs must use the same variant | Satisfies BR-4 (cookie reject ≥ accept prominence) by construction — pairing Primary-accept with Ghost-reject would violate it regardless of size, so the rule forbids that pairing outright rather than leaving it to per-screen judgment | 2026-08-19 |
| Badge: pill shape, tinted (not solid) semantic background, never decorative | A solid `color.error` badge next to body text would compete with real error Alerts for visual weight; tinting keeps a badge legible without borrowing Alert-level urgency | 2026-08-19 |
| Alert: inline only, no toast/banner mechanism; no auto-dismiss when it gates a required decision | Every current use (sold-out warning, permission backstop, offline message) is attached to one screen moment, not a global announcement; toast implies dismissible-and-transient, wrong for something the user must act on before proceeding | 2026-08-19 |
| Input: bordered container, always-visible label (never placeholder-as-label), same focus ring as Button | A placeholder that vanishes on typing isn't a label and would compound the accessibility gap this document already flags; consistent focus treatment matters more than per-component novelty | 2026-08-19 |
| Select: button-group/chip, not a native dropdown; sweetness level left open (discrete vs. percentage) | Every option set is small (2–5) in a mobile, one-handed, fast-ordering context already on record — a dropdown costs an extra tap-scroll-tap a visible chip row doesn't; sweetness wasn't decided because the spec doesn't say whether it's discrete or a slider-scale, and those need different controls | 2026-08-19 |
| Card: bordered container, no added "tinted surface" tier just for layering | Matches how Table already separates rows (borders, not zebra striping) and Alert/Badge (flat, tint only for meaning); inventing a second surface tone to solve a layering problem that hasn't been observed on a real screen yet would be exactly the kind of ungrounded token Rule 1 exists to prevent | 2026-08-19 |
| Table: header/row/numeric-alignment/hover styling decided; Sort and Filter deliberately left unimplemented | No spec asks for sort or filter, and the only table that exists (menu management) is a small hand-curated list where either would solve a problem nobody has yet; deciding them now would be designing ahead of a real screen, the same reasoning already applied to Shadows | 2026-08-19 |

Update this table whenever a real design decision is made — including "we decided not to decide X yet."

---

## 8. Design Quality Checklist

Before merging new UI:

- [ ] Used an existing component? (none exist yet — first components should be built expecting reuse, see Rule 2)
- [ ] Used an existing design token? (none exist yet — flag if a value had to be invented instead of decided)
- [ ] Color matches the brand system? (no brand system exists yet — flag this explicitly rather than skip it)
- [ ] Typography correct?
- [ ] Spacing correct?
- [ ] Responsive handled for both known device contexts (customer phone / staff-barista larger screen)?
- [ ] Loading state present?
- [ ] Empty state present?
- [ ] Error state present, including the specific error/edge cases fixed in `20260802-01` §7?
- [ ] Permission state present (staff vs. owner distinction per BL-006)?
- [ ] Accessibility considered? (no requirement exists yet — this is a known gap, do not skip silently)
- [ ] No one-off styling without a stated reason?
- [ ] Consistent with existing UI/specs?
- [ ] `DESIGN.md` updated if a new design decision was made?

---

## Final Validation

- No conflicting design tokens exist — because no design tokens exist yet.
- No duplicate-meaning colors exist — same reason.
- No conflicting typography definitions exist — same reason.
- Component rules in §3 are drawn directly from cited spec requirements, not invented.
- UX rules in §4 are implementable as stated — they are already-approved business rules (`BR`/`AC`) from the specs, not aspirational guidance.
- This document is safe to use as AI/developer context precisely because it is honest about what is and isn't decided — a shorter document with invented tokens would be more dangerous, not more useful.
- No information in this document was fabricated without source-code or source-document evidence — every non-`TBD` claim cites a spec path.
