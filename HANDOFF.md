# Trust Strategy Builder — Handoff

Single-page, client-side estate/trust/entity **planning & attorney-prep** organizer. Everything lives in
`index.html` (inlined CSS + JS, no build step). State is the global `S`, persisted to `localStorage` via
`save()`. **Planning/organization only — not legal, tax, or financial advice; creates no legal documents.**

## Run / deploy
- **Local:** `python -m http.server 8000` from the repo root → http://localhost:8000
- **Deploy:** `git push origin main` → Vercel auto-deploys (project `trust-strategy-builder`). Push via the
  `github-jessica` SSH alias (Jessica's repo `github.com/jessiesfaith/trust-strategy-builder`).
- **Live:** https://trust-strategy-builder.vercel.app  ·  pretty path `app.fastinsights.io/trust-strategy-builder`
  (rewrite in the `fast-insights-app` project).
- **No tests/lint/build.** Verification = open the page, exercise tabs, watch the console.

## Layout (tabs via `switchTab(key)`)
`.wrap > section.block` blocks are shown/hidden per tab. Tab order in `renderTopTabs()`:
**Overview · Tax Estimator · Distribution · Strategy · (per-trust tabs) · + New trust.**
The left nav (`#topTabs`) is pinned just below the sticky header via `syncNavTop()` (re-measured on
window resize; falls back to static, row-wrapped below 760px).

- **Overview** — `#essentials` (always shown) + collapsible sections: `#snapshot` Strategy Snapshot,
  `#assets` Asset Summary, `#mindmap` Estate Map (live diagram), `#details` Planning Detail (accordions),
  `#report` Attorney memo. Collapsible idiom: `toggleSection(id)` toggles `.collapsed`; CSS hides
  `.collapse-body` and rotates `.collapse-chev`. Most default collapsed.
- **Tax Estimator** (`#taxconsider` + `#taxest`, shown together on the `tax` tab) — `renderTax()` builds the
  Tax Considerations framework (numbered subsections collapsible via `toggleTC`/`tcOpen`); `renderTaxEstimator()`
  builds the estimator inputs; `computeTax()` fills all result spans (it does NOT call `ensureTax()` itself —
  `render()` and the tab switch run `ensureTax()` first). The Estate card splits the income-tax deduction
  into a capital-gains line (`r_est_lessinc_cg`) and an ordinary/rental/executor line (`r_est_lessinc_other`);
  the Rental income & executor card shows a "Total tax (this section)" (`r_pnl_tottax` = your P&L income tax +
  executor income tax + executor payroll/SE). The 4-metric headline is **Total estate value · Grand total tax
  due · Estate tax with added structures** (projected from the Strategy-tab toggles) **· Estate remaining
  after taxes**. The Rental card shows the P&L at **FMV-reassessed** property tax (`r_pnl_net`, "at FMV") with
  a current-assessed reference line (`r_pnl_net_assessed`); the "Taxes this generates" rows
  (`r_pnl_tax`/`r_exec_inc`/`r_exec_se`/`r_pnl_tottax`) render as negatives; the "ordinary / rental / executor"
  income-tax line carries a self-reconciling note (`r_est_lessinc_other_note`). The Real Estate "property tax
  & capital gains" card splits the reassessed total into `r_pt_business` (rental/business, = the rental P&L)
  and `r_pt_nonbusiness` (personal/land — Schedule A), summing to `r_pt_reassessed`.
- **Distribution** (`#dist`) — `renderDistribution()` / `computeDistribution()`. Beneficiary list (% allocation
  + timing) and reserves — executor %, property mgmt, taxes/debts, **grandchildren milestone funding**, and
  **milestone life-insurance distributions** (the allocated death benefit is earmarked, so it's reserved from
  the estate before the general split) — each itemized and summing to Total reserves. Distributable =
  `netAfterAllTaxes().net − reserves`. When the grandchildren card is on, the child count defaults to and is
  floored at 1 (a blank count no longer zeroes the reserve); the card shows per-child amounts and
  all-children (× count) totals.
- **Strategy** (`#outcome`) — `renderOutcome()` / `computeOutcome()`. The life-insurance death-benefit input
  lives here (feeds the gross estate + the ILIT comparison). Toggle structures (trust/ILIT/FLP/QPRT/IDGT) →
  No-planning vs Your-setup comparison (estate tax, cap gains, property tax, net to heirs) + a charitable
  CLAT/own-NFP break-even calculator (`computeCharity`). These toggles also drive the Tax Estimator headline
  "Estate tax with added structures" — ILIT removes the death benefit, IDGT removes full rental value, FLP ≈
  30% discount, QPRT removes the residence.

## Tax engine — key functions
- `ensureTax()` — defaults `S.tax`; **auto-syncs** the taxable estate to live assets (`estateAuto`) and the
  exemption to the current-year figure (`exemptionAuto`) unless the user overrode them; defaults the
  spousal-doubling `dual` from marital status.
- `estateFromAssets()` — single source for the asset-based estate (used by `renderTax` AND `ensureTax`, so the
  Tax Estimator and Tax Considerations never drift).
- `rentalPnl()` — single source for the rental P&L over **rental-business properties only** (type matches
  `/rental|commercial|vacation/i`; Land / Other parcels are excluded — their property tax is split out in the
  Real Estate card). gross = Σ per-property `annualRent`; subtracts the executor/management salary (a fixed
  deductible expense, charged even at a loss), Σ per-property `annualExpenses` (operating expenses, excl.
  property tax), and the rental property tax. The P&L is **modeled at the FMV-reassessed property tax** (the
  estate's go-forward basis after a Prop 19 reassessment at death): `taxable = max(0, reassessed)`. It also
  returns the current-assessed `actual` P&L and both property-tax figures (`propNow` / `propReassessed`) for
  the reference line. No manual "Gross rental income" input — gross is entirely per-property.
- `annualIncomeTax()` — single source for one year of during-life income tax (used by the headline, the
  Estate card's income-tax lines, and the Distribution base). Covers the grantor's 1040 (ordinary + net
  rental P&L + cap-gains/dividends − QBI − retirement), the corporate DRD, and the executor/management
  salary taxed as the recipient's **own** income (a standalone Single filer: income tax + payroll/SE).
  **QBI = net rental P&L** (no manual QBI input); 20% deduction, phased to $0 if SSTB above the §199A cap.
- `netAfterAllTaxes()` — **single source of truth** for net available to heirs = total estate value
  (assets + life-insurance death benefit) − federal estate tax − real-estate capital-gains tax − one year
  of all income-side tax (`annualIncomeTax().total`). It is the Distribution base; the Estate-tax card and
  the Tax Estimator headline (both labeled "Estate remaining after taxes") compute the same figure inline,
  so all three reconcile. These baselines are **UNPLANNED** — the Strategy-tab structures (ILIT/FLP/QPRT/IDGT)
  feed only the Strategy tab and the headline "Estate tax with added structures" projection, never the
  Estate-card/Distribution baselines (clarifying notes live in `r_annual_note` / `r_dist_taxnote`).
- `reCapInfo()` / `reCapTaxOn(g)` — capital-gains tax on a real-estate gain, stacked above ordinary + entered
  long-term gains (federal LTCG + CA state; NIIT not included — noted in the UI).
- Real-estate capital gains are managed **only** in the Real estate card (not the Capital gains card); they're
  excluded from the recurring "Est. annual income tax" headline.
- Helpers: `groupNum`/`unfmtNum`/`fmtNumField` (comma-formatted numeric inputs — stored values stay
  comma-free), `m$`/`num` (money/number display), `ltcgStack`/`progTax`/`stateTax`/`seTaxCalc` (tax math),
  `TAXY` (per-year brackets/limits) / `TAXC` (rates) / `STATE_TAX`.

## State shape (`S`, in localStorage)
`profile`, `people[]`, `properties[]` (incl. `basis`, `assessed`, `annualRent`, `annualExpenses`),
`bankAccounts[]`, `brokerageAccounts[]`, `llcs[]`, `subTrusts[]`, `customTrusts[]`/`trustNotes`,
`customStructures[]`, `tax{...}` (filing/year/state, ordinary/ltgain/…, estate+estateAuto,
exemption+exemptionAuto, dual, deathBenefit, sstb/drd…, `execSal` — the management/executor salary the
rental P&L deducts and that is taxed as the executor's own income — age/seComp,
ret401/retIra/retSimple/retSep, propTaxRate), `beneficiaries[]`, `insDist[]`,
`reserves{execPct,propMgmt,taxDebt}`, `gk{...}` (grandchildren funding), `outcome{...}` + `charity{...}`
(Strategy tab), `review`. `ensureTax()`/`ensureDist()`/`ensureOutcome()` backfill defaults. QBI and gross
rental are **derived, not inputs** (QBI = net rental P&L; gross = Σ per-property `annualRent`) and ILIT is
the Strategy tab's `outcome.ilit`, so the old `qbi`/`rentalGross`/`inILIT` tax defaults were removed.

## Conventions / guardrails
- `vercel.json` = `{ "cleanUrls": true }`; `.vercelignore` keeps only `index.html` + `vercel.json`.
- Tone: never "recommended final structure" — use "possible structure to discuss," "attorney-review item,"
  etc. Amber `.disclaimer` bars are collapsible ("Disclaimer — tap to read"); every estimate is labeled
  illustrative / not advice.
- All numeric inputs are floored (negatives can't corrupt totals); money uses `m$` (NaN → $0).

## Known limitations / caveats (by design — illustrative tool)
- **Login is cosmetic** (client-side; the password ships in the page). Real access control would need Vercel
  deployment password protection. Prompts every visit (no "remember me").
- Tax math is simplified for planning: ignores AMT, many phase-outs, NIIT on the real-estate step-up figure,
  IRA earned-income/eligibility & multi-account coordination (the retirement card sums whatever is entered),
  and uses forward-looking 2026 figures. Confirm everything with a CPA/attorney.
- The **insurance premium** to fund a policy must be entered from a carrier illustration (not derivable).
- Strategy-tab structure removals (FLP 30% discount, IDGT gifts asset value out, etc.) are simplified,
  internally-consistent estimates — not a substitute for modeling by professionals.

No known defects at handoff (2026-06-17). All four tabs render and reconcile; console clean.
