# Changelog — Trust Strategy Builder

Project: **Trust Strategy Builder** (Fast Insights tool). Single `index.html`.
Repo `jessiesfaith/trust-strategy-builder` → Vercel → **app.fastinsights.io/trust-strategy-builder**.
Newest first. Each entry notes the commit and what changed.

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
