# Changelog — Trust Strategy Builder

Project: **Trust Strategy Builder** (Fast Insights tool). Single `index.html`.
Repo `jessiesfaith/trust-strategy-builder` → Vercel → **app.fastinsights.io/trust-strategy-builder**.
Newest first. Each entry notes the commit and what changed.

## 2026-06-17 — Thousands-separator commas in number/currency input fields

- **Number and currency input fields now format with commas as you type.** The entity detail forms
  (Estimated value, Mortgage balance, Number of units, account Balances) and all Tax Estimator amount
  fields were plain `type="number"` inputs (which can't show thousands separators). Switched them to
  `type="text" inputmode="decimal"` with live comma grouping via `fmtNumField()` (caret-preserving),
  formatted on initial display via `groupNum()`. **Stored values stay comma-free** — `unfmtNum()`
  strips commas on save (entity forms) and before `updTax()` writes (Tax Estimator), so every
  downstream `Number()`/sum/`computeTax()` keeps working. Display helpers (`num`/`M`/`m$`) already
  added commas; this fixes the input side to match.

## 2026-06-17 — Trustee timeline dates + per-line rental list

- **Trustee Protection Plan: date-driven deadlines.** Added an optional "Date of death / incapacity"
  date field to the Executor / Trustee Protection Plan timeline. When set, each milestone title shows
  its computed deadline: "At death / incapacity — <date>", "First 30 days — by <date+30d>", "90 days
  — by <date+90d>", "6 months — by <date+6mo>", "12 months — by <date+12mo>". Stored in
  `S.protectionDate` (persists), recalculated live via `updProtectionDate()` → `renderAccordions()`.
  Dates are parsed locally (no timezone drift) and formatted "Mon D, YYYY". Labels stay plain when no
  date is entered. Carries a planning-only / "not legal deadlines" note.
- **Rental Properties summary card: one property per line.** The Asset Summary "Rental Properties"
  card was a single comma-joined run-on (hard to read, especially since addresses contain commas).
  Now each property renders on its own dashed-separated line (`.sub-line`), still inside the
  scrollable card (max-height raised 54px → 150px). Other cards unchanged.

## 2026-06-17 — Login required on every visit (no "remember me")

- **The client-side login gate now prompts on every page load.** Previously, a successful sign-in
  set `localStorage['tsb_auth']='1'` and an inline script auto-hid the gate on all future visits, so
  it only ever asked once per browser. Removed that persistence: deleted the auto-hide `<script>`,
  dropped the `setItem`/`removeItem('tsb_auth')` calls, and `login()` now just hides the overlay for
  the current page view. Reloading or returning re-shows the login. Stale `tsb_auth` flags from old
  sessions no longer bypass the gate. (Note: this is still a cosmetic, client-side gate — the
  password ships in the page, so it deters casual viewers only. Real access control would need
  Vercel deployment password protection.)

## 2026-06-17 — Collapsible Export Center + Mermaid card; all sections collapsed by default

- **Export Center and the Mermaid Mind Map Code card are now collapsible too**, and **all four
  collapsible sections now default to collapsed** (Planning Memo, Planning Detail Sections, Export
  Center, Mermaid card) for a cleaner initial view. Export Center collapses its whole body (download
  grid + Mermaid card); the Mermaid card nests inside and collapses independently. The "Copy code"
  button uses `event.stopPropagation()` so it never triggers the card's collapse, and keyboard
  toggles are guarded with `event.target===this` so activating the button doesn't toggle the card.
  Generalized the shared CSS/JS: `.collapsible` (any head), `.collapse-head` for card-style heads,
  `:scope >` selectors and direct-child combinators so nested collapsibles stay independent.

## 2026-06-17 — Collapsible report sections

- **Collapse/expand for the Attorney-Ready Planning Memo and Planning Detail Sections.**
  Both section headers are now clickable toggles (mouse + Enter/Space) that collapse and expand
  their bodies, using the existing accordion visual idiom (rotating `▶` chevron, `cursor:pointer`,
  hover). Both default to **expanded**, so first load is unchanged. Shared implementation:
  generic `toggleSection(secId)` + reusable `.section-head.collapsible` / `.collapse-chev` /
  `.block.collapsed > .collapse-body` CSS (replaces the earlier memo-specific `toggleMemo`).
  Accessible: `role="button"`, `tabindex="0"`, `aria-expanded`/`aria-controls`, and a hint that
  flips between "Click to collapse" / "Click to expand". Inner accordions still open independently
  when Planning Detail Sections is expanded.

## 2026-06-16 — UI/layout pass + new tabs (all LIVE)

Six changes shipped this day, all in `index.html`, each pushed to `main` (Vercel auto-deploy).
Verified live on both `trust-strategy-builder.vercel.app` and `app.fastinsights.io/trust-strategy-builder`.

- **Scrollable rental property list** — commit `e9985d4`.
  The Asset Summary "Rental Properties" card was rendering its whole list on one `white-space:nowrap`
  line, which (because grid columns were `1fr`) blew out the entire row's layout. Fixed: grid tracks →
  `minmax(0,1fr)`, card `min-width:0`, and the rental list now wraps and scrolls vertically inside the
  card (`.sub.scroll`, capped height + `overflow-y:auto`).

- **Irrevocable Living Trust tab + user-created custom trust tabs** — commit `e9985d4`.
  Added a built-in **Irrevocable Living Trust** tab (info, tax/cost impact, attorney/CPA decisions,
  statutory refs). Added a **"＋ New trust"** button so users can create, rename, and delete their own
  trust/entity tabs; custom trusts persist in saved state. (`allTrusts()` / `newTrust()` /
  `renameTrust()` / `deleteTrust()` wired into `renderTopTabs` + `renderTrustPanel`.)

- **Wider layout + bigger fonts across all tabs** — commit `0679df2`.
  Content column max-width 1200px → **1480px** (header 1180 → 1460). Every `font-size` bumped up one
  notch in both the CSS rules and the inline styles in the JS-rendered panels, so all tabs read larger
  (e.g. body copy 13 → 14.5px, headings 21 → 24px).

- **Tax Estimator moved to its own tab; Overview button added to the header** — commit `e9769b3`.
  The Tax Estimator section became its own **Tax Estimator** tab (listed right under Overview); it is
  hidden on Overview and shown only on its tab. Added a **🏠 Overview** button to the sticky header so
  it stays reachable while scrolling.

- **Tax Considerations moved to the bottom of the Tax Estimator tab** — commit `e674ac6`.
  Relocated the Tax Considerations section out of Overview to sit below the estimator on the Tax
  Estimator tab.

- **Header Overview button made prominent** — commit `415d8ed`.
  Moved the Overview button out of the right-side action cluster (where it blended in) to a primary
  button on the **left** of the frozen header, beside the brand; the brand/logo is now clickable too.

_Verification:_ each change confirmed in the local preview (DOM checks — widths, tab order, section
visibility, font sizes) and confirmed present in both live deployments via served-HTML markers.
The Mermaid-heavy Overview tab times out the preview screenshot tool, so visual checks were done at the
DOM level.
