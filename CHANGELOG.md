# Changelog — Trust Strategy Builder

Project: **Trust Strategy Builder** (Fast Insights tool). Single `index.html`.
Repo `jessiesfaith/trust-strategy-builder` → Vercel → **app.fastinsights.io/trust-strategy-builder**.
Newest first. Each entry notes the commit and what changed.

## 2026-06-17 — Cleanup & handoff: dead-code removal + "Net to heirs" display fix

- **Fixed a display bug.** The Estate-tax card's **"Net to heirs after all taxes"** row — and the same
  line in the downloaded tax summary — was permanently blank ("—"). When the income-tax line was split
  into capital-gains vs. the rest, the `set('r_est_netheirs', m$(netHeirs))` call had been merged onto the
  end of a `//` comment and silently stopped executing. Restored it to its own statement; the row now
  shows the figure and reconciles **exactly** with the headline "Estate remaining for heirs" and the
  Distribution base (all = `netAfterAllTaxes().net`; verified at $30,877,603 on the sample scenario).
- **Removed verified-dead code** (each confirmed unreferenced before removal):
  - the orphaned **"Native mind-map tree" CSS family** (`.mindtree` + `.mt-*`, ~20 rules) left over from a
    pre-SVG renderer — the diagram now renders via `buildOwnershipSVG()` / `buildMindmapHTML()`;
  - the unused **`.pill-count`** and **`code.inline`** CSS rules;
  - the unused **`buildFlow()`** Mermaid-flowchart builder (~57 lines) — the Ownership Map renders as SVG
    and the Mermaid export uses `buildMindmap()`;
  - three dead **`ensureTax()` default seeds** (`qbi`, `rentalGross`, `inILIT`) — all superseded by
    derived values (QBI = net rental P&L; gross = Σ per-property rent) or the Strategy tab (`S.outcome.ilit`);
  - four never-read **DOM-id attributes** (`tax_estate`, `tax_exemption`, `tax_seComp`, `mmResetBtn`) — the
    fields/buttons stay; only the unused ids were dropped;
  - the dead `.mindtree` fallback inside the full-screen-zoom `querySelector`.
- Handoff docs (HANDOFF / CLAUDE / README) refreshed to match current behavior. No console errors; all
  four tabs render and reconcile.

## 2026-06-17 — Per-property rental P&L, QBI from net rental, executor salary, Strategy→Estimator flow, sticky nav

- **Gross rental income now sums per-property "Annual rent."** Added two per-property fields in Real
  Estate — "Annual rent / gross income ($)" (`annualRent`) and "Annual operating expenses — excl. property
  tax ($)" (`annualExpenses`). The Rental income card's gross is now Σ per-property `annualRent` (the manual
  "Gross rent" input and its fallback were removed). The property meta line shows "rent $X/yr".
- **Rental P&L = gross − executor salary − operating expenses − current rental property tax**, with a
  "Net rental P&L if reassessed to market" example line. `rentalPnl()` is the single source.
- **QBI is now the net rental P&L** (no separate "Qualified business income" input). The QBI card label
  reads "Qualified business income (= net rental P&L)"; deduction = 20% × max(0, net rental P&L), phased to
  $0 if SSTB above the §199A income cap.
- **Estate card "Less income tax" split into two lines** — "capital gains & dividends" (`r_est_lessinc_cg`)
  and "ordinary / rental / executor" (`r_est_lessinc_other`), capped so neither goes negative and both sum
  to the annual income-tax total.
- **New "Total tax (this section)" row** in the Rental income & executor card (`r_pnl_tottax` = your income
  tax on the P&L + executor income tax + executor payroll/SE).
- **Executor / management salary** is a fixed deductible rental expense (charged even with no profit) and is
  taxed as the recipient's own income — a standalone Single filer (income tax + payroll/SE), via
  `annualIncomeTax()`.
- **Strategy-tab structure toggles now drive the Tax Estimator headline "Estate tax with added structures"**
  (ILIT removes the death benefit; IDGT removes full rental value; FLP ≈ 30% discount; QPRT removes the
  residence). The life-insurance death-benefit input now lives on the Strategy tab (feeds the gross estate
  + ILIT comparison). Headline metrics are now: Total estate value · Grand total tax due · Estate tax with
  added structures · Estate remaining for heirs.
- **Sticky/pinned left nav** — `syncNavTop()` pins the side nav just under the sticky header (re-measured on
  resize; static below 760px).
- **Grandchildren milestone reserve count defaults to 1** (a blank/zero count no longer zeroes the reserve
  when the card is on).

## 2026-06-17 — Tax Estimator: card swap, QBI deductible total, editable retirement

- **Swapped the LLC-vs-S-corp and Life-insurance cards** (LLC now first/left, Life-insurance second/right).
- **QBI card:** the deductible amount is now the final, emphasized line — "Total deductible from income
  (20% of QBI)" — with "Est. federal tax saved" above it, so it's clear what's in the total.
- **Retirement contributions are now editable.** Replaced the read-only limit lines with four editable
  per-account contribution inputs (401(k) / IRA / SIMPLE / SEP-solo-401k), each labeled with the year's
  max for reference, defaulting blank. "Total pre-tax contributions" + "Income tax saved" compute from
  what's entered (progressive fed + CA delta); over-limit flags and a coordination/eligibility caveat in
  the note. (`S.tax.ret401/retIra/retSimple/retSep`; the old single "pre-tax contribution" field removed.)

## 2026-06-17 — Grandchildren milestone-funding reserve + negative-sign consistency

- **New "Grandchildren milestone funding" card** on the Distribution tab. Toggle on and enter: number of
  grandchildren, per-child life-insurance death benefit + **annual premium × premium-years**, college-grad
  gift, age-25 gift, first-home down payment, and IRA (annual × years). The estate reserve = **premiums +
  cash milestones + IRAs** (× children) — i.e. the cash the estate sets aside to *pay the premiums* and the
  disbursements; the death benefit is shown for reference (what the premiums buy). The reserve flows into
  "Less reserves" so it's deducted before distribution. Includes a "structure to discuss" note recommending
  an ILIT on the grantor's life (survivorship if married) vs grantor-owned (in estate) vs grandkid-owned.
  All inputs floored at 0; IRA earned-income caveat noted. State: `S.gk` + `updGk()`.
- **Estate tax card: deduction lines now all show a minus sign** — "Federal estate tax", "Less
  capital-gains tax", and "Less real-estate capital-gains tax" all render as "– $X" (consistent), $0 plain.

## 2026-06-17 — Headline: total tax at death + estate remaining for heirs

- **The headline "Estate tax now (at death)" became "Total tax at death"** and now adds capital-gains
  tax + real-estate capital-gains tax to the federal estate tax (estate tax + cap-gains + RE cap-gains).
- **Added a 4th headline metric, "Estate remaining for heirs"** = estate − total tax at death. It
  reconciles exactly with the Estate tax card's "Net to heirs after all taxes" and the Distribution
  tab's base (all from `netAfterAllTaxes()`). Headline grid is now 4 columns.

## 2026-06-17 — Show the math in notes (retirement, RE cap gains, annual income tax)

- **Retirement contributions note** now spells out each limit's composition (e.g. "401(k) $24,500 base
  + $8,000 age-50 catch-up = $32,500", IRA/SIMPLE likewise, SEP = 25% × comp capped) and clarifies these
  are 2026 *limits*, not amounts entered.
- **Real-estate capital-gains note** breaks the tax into federal long-term (~20%) + California (gains
  taxed as ordinary income, up to 13.3%), with the effective % — answering "isn't it 20%?" (federal is;
  CA state is the rest). Notes NIIT may also apply.
- **"Est. annual income tax" note** shows the components: ordinary-income tax + investment (cap-gains &
  dividends) + self-employment − QBI saving − retirement saving = total, and reminds that real-estate
  capital gains are a one-time at-sale figure (in the Real estate card), not part of the annual number.

## 2026-06-17 — Net-to-heirs after all taxes; Distribution starts from the true net

- **Estate tax card now shows the full math chain:** Estate → − federal estate tax = "Estate after
  federal estate tax", then **− capital-gains tax − real-estate capital-gains tax = "Net to heirs after
  all taxes"** (with a note spelling out each subtraction and the step-up caveat).
- **Distribution tab now starts from that "Net after all taxes"** (not just estate − estate tax), so
  reserves and beneficiary allocations come out of the true amount available to heirs. A breakdown note
  shows `estate − estate tax − cap-gains − real-estate cap-gains`. Single source of truth:
  `netAfterAllTaxes()`, used by both the Tax Estimator card and the Distribution tab, so the two always
  agree (verified: both = $22,549,127 for a $36.05M / $5M-LT-gain / $10M-RE-gain example). Removed the
  now-unused `estateAfterTax()` helper.

## 2026-06-17 — Collapsible amber disclaimer bars

- **The amber "⚠️" warning/disclaimer bars now start compact** ("Disclaimer — tap to read") and
  expand on click. Implemented generically: `.disclaimer:not(.open)` hides the body and shows a label
  via `::after`, and a single delegated click handler toggles `.open` — so it covers every disclaimer,
  including the ones rendered dynamically on the Tax Estimator / Distribution / Strategy tabs.
- **Collapsed state is now a subtle one-liner**, not a full amber bar: when collapsed the amber
  background/border are dropped and the line is dimmed/shrunk (~24px), so it reads as minimized; tapping
  restores the full amber bar. (Functionally they were already collapsing — this was a visual fix so it
  actually *looks* collapsed.)

## 2026-06-17 — Real-estate cap gain managed in one place; Strategy model fixes

- **Moved the real-estate capital gain out of the "Capital gains & dividends" card.** It was confusing
  to have real estate in both that card (as a "Real-estate gain"/"Total long-term gain" line) and the
  Real estate card. Now the Capital gains card taxes only the gains you enter; the **Real estate card**
  owns the real-estate gain and its cap-gains tax (value − basis, "$0 if held to death via step-up").
  Side benefit: the headline **"Est. annual income tax" no longer balloons** by folding in a one-time
  real-estate gain (it was ~$9.4M with the sample; now reflects recurring income only).
- **Strategy Outcome — model corrected** (adversarial-review findings). Made the estate/gains/property
  model internally consistent: ILIT now produces a *real* estate-tax benefit (the death benefit is in
  the taxable estate unless an ILIT holds it — previously it subtracted a phantom amount); IDGT removes
  the gifted asset's **value** from the estate while charging carryover capital-gains on its **gain**
  (the two sides now agree); combining IDGT with FLP/QPRT no longer double-removes the same assets; and
  property-tax reassessment is now decided **per asset** (only the structure that holds a given property
  avoids its reassessment). Estate value row labeled "incl. life insurance" for clarity.

## 2026-06-17 — Strategy Outcome tab; estate breakdown; removed Preview banner

- **Removed the "Preview Mode" top strip** (the "● Preview Mode" pill stays in the header).
- **Estate tax card** now spells out the breakdown: "Amount above exemption (taxed)" → "Federal estate
  tax (40%)" → "Estate after federal estate tax" (previously the above-exemption piece was only in the
  footnote).
- **New "Strategy" tab — Strategy Outcome.** Toggle which structures are in place (revocable trust,
  ILIT, FLP, QPRT, IDGT) and see a **No-planning vs Your-setup** comparison of estate value, federal
  estate tax, capital-gains tax (heirs), annual property tax after death, and **net to heirs**, with a
  one-line summary of the net effect. Captures the central tradeoff: gifting assets out cuts estate tax
  but forfeits the step-up (carryover basis → capital-gains tax) and an FLP/IDGT holding property avoids
  the death reassessment. Includes a "how the capital gain can be reduced" explainer and a **charitable
  break-even calculator** (CLAT / donate-to-your-own-NFP): given annual contribution, investment return,
  deduction rate, and horizon, it shows net cost of giving, foundation value, cumulative tax saved, and
  the **break-even year**. Figures flow from the Tax Estimator inputs (estate, exemption, death benefit,
  property values/basis/assessed, property-tax rate) so the tabs stay consistent.

## 2026-06-17 — Per-property current property tax in Planning Detail

- **Each property in the Planning Detail list now shows its current annual property tax** ("property
  tax ~$X/yr") on the row's meta line, computed as assessed value × the Tax Estimator's property-tax
  rate (assessed ← entered assessed value, else cost basis, else market value). `render()` now calls
  `ensureTax()` up front so the rate is initialized before the accordion list renders.

## 2026-06-17 — Sort properties in natural numeric order

- **Properties now sort by their leading number** (1, 2, 3 … 10 … 17 — not the lexicographic
  1, 10, 11, 2, …). `render()` sorts `S.properties` by name with `localeCompare(..., {numeric:true})`,
  so every property list (Asset Summary, Planning Detail, memo, mind map) shows them in order.
  Unnumbered names (e.g. a personal residence) fall in A–Z order after the numbered ones.

## 2026-06-17 — Collapse Strategy Snapshot & Asset Summary; collapsible Tax-Considerations subsections

- **Strategy Snapshot and Asset Summary are now collapsible, collapsed by default** (same idiom as the
  other sections). The Asset Summary's "+ Add" buttons use `event.stopPropagation()` so they no longer
  trigger the section toggle.
- **Tax Considerations subsections 1 / 2 / 3 are now individually collapsible.** Section 1 ("estate
  size vs. exemption") stays expanded by default with a collapse option; sections 2 ("step-up vs.
  gifting out") and 3 ("which structure pulls which lever") start collapsed. State persists across
  re-renders (`tcOpen` + `toggleTC()`), so editing an estate/exemption figure no longer snaps an
  opened subsection shut.

## 2026-06-17 — Property tax, reassessment increase, step-up benefit + negative-input guards

- **Real estate — property tax & step-up (new Tax Estimator card).** Added a per-property "Assessed
  value — property tax" field and a "Property tax rate (%)" input (default 1.1%). Computes: annual
  property tax (Σ assessed × rate; assessed ← entered assessed value, else cost basis), the tax if
  **reassessed to market value**, and the **potential reassessment increase / yr** (the jump that can
  occur on transfer/death — CA Prop 13/19, with a note on exclusions). Also computes the **step-up in
  basis** benefit: the federal + state capital-gains tax the step-up erases at death, derived from the
  real-estate unrealized gain (incremental LTCG on value − basis). Added to the downloadable tax summary.
- **Distribution Plan — negative-input guards (from adversarial verification).** Floored reserve
  inputs (execPct capped 0–100; propMgmt/taxDebt ≥ 0), beneficiary allocation %, and insurance
  pct/amount at 0 in computeDistribution(), so a typed negative can no longer make distributable
  exceed the estate, show negative reserves/shares, or corrupt the allocation total.

## 2026-06-17 — Distribution Plan tab + estate-tax clarity + cross-screen exemption consistency

- **New "Distribution" tab — Estate Distribution Plan.** A planning organizer that starts from the
  estate after federal estate tax (from the Tax Estimator) and flows: after-tax estate → reserves →
  distributable → per-beneficiary shares. Blocks: (1) a standalone **beneficiary list** with % allocation
  (live $ amounts, 100%-allocation check, "Import from People"); (2) **distribution timing** per
  beneficiary — outright, staggered by age (splits the share across ages), milestone-based, lifetime/HEMS
  trust, income-only; (3) **life-insurance distributions by milestone** to children/grandchildren (% of
  death benefit or fixed $, with over/under-allocation flag); (4) **reserves** — executor/trustee comp
  (% of estate), property-management fund, taxes/debts reserve — deducted before distribution. State:
  `S.beneficiaries` / `S.insDist` / `S.reserves`. New `renderDistribution()`/`computeDistribution()` +
  CRUD; wired into the tab bar, `switchTab`, and `render()`.
- **"Annual income tax" clarified.** Relabeled the headline metric "per year, during life — not a
  death-time tax" to answer the common "is this at death?" question (estate-tax columns already say "at death").
- **Estate value after tax.** Added "Estate after federal estate tax" to the Estate tax card (estate − estTax).
- **Collapse the Estate Map.** The live diagram section is now collapsible and collapsed by default.
- **Cross-screen consistency (follow-up to the estate-sync fix).** A verification pass found the
  exemption threshold could still differ between the Tax Estimator and Tax Considerations. Unified it:
  the exemption now auto-syncs to the current-year figure (`exemptionAuto`, with a manual-override +
  "↻ Use current-year" link, mirroring the estate field), the spousal doubling is driven by one source
  (marital status → `S.tax.dual`, still toggleable), Tax Considerations now honors a manual estate
  override in its below/above-exemption verdict (with an on-screen note), and estate/exemption edits
  refresh both panels. Fixes the stale-localStorage case (year shows 2026 but exemption frozen at 13.99M).

## 2026-06-17 — Fix estate figure mismatch + manual Recalculate button

- **Bug: Tax Estimator and Tax Considerations showed different estate totals.** `S.tax.estate` was set
  once in ensureTax() and then frozen/persisted, while Tax Considerations recomputed its "Rough net
  estate" live from assets — so the two drifted (e.g. ~45M vs ~35M) whenever assets changed after the
  estimator first initialized. Fixed with a single source of truth, `estateFromAssets()`, used by BOTH
  renderTax() and ensureTax(). The Tax Estimator's taxable-estate field now auto-syncs to the live
  asset total (`S.tax.estateAuto`, default true) so it always matches Tax Considerations. Editing the
  field switches it to a manual override (`updEstate` → estateAuto=false, with an "↻ Use asset total"
  link via `resyncEstate` to re-enable). Also aligned the Tax Considerations exemption figure to the
  current-year exemption (`curY().exemption`) instead of a hardcoded 13M, so both screens agree.
- **Manual Recalculate button.** Added "↻ Recalculate" to the Tax Estimator header (`recalcTax`) —
  the estimate already recomputes live on every field edit; this re-runs it on demand and re-syncs the
  estate from current entries.

## 2026-06-17 — Tax Estimator: retirement contribution, less double-entry, real-estate cap gains, layout

- **Editable retirement contribution.** The Retirement card only showed contribution *limits*; added a
  "Your pre-tax contribution this year ($)" field that shows the income tax saved (federal + state, via
  the actual progressive-tax delta of reducing ordinary income) and flags entries over the 401(k)
  elective max. The saving now reduces the headline annual income-tax total.
- **Less double-entry.** "Net business profit" now auto-fills "Qualified business income" and
  "Self-employed net comp" (the same number was previously typed in 3 cards). Each remains individually
  editable; re-entering net profit re-syncs them. (`updProfit()` updates the sibling inputs in place — no
  full re-render, caret preserved.)
- **Real-estate capital gains.** Added a "Cost basis / purchase price ($)" field to each property. Per
  property gain = max(0, value − basis); the list now shows "cap gain $X". The total across all
  properties with a basis feeds the Tax Estimator as "Real-estate gain (from properties)" and rolls into
  the long-term gain (the manual field is relabeled "Other long-term gain"). Full gain — no
  primary-residence exclusion (per choice). Added to the Markdown tax summary.
- **Layout.** Moved the Tax Considerations section above the Tax Estimator on the Tax Estimator tab.

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
