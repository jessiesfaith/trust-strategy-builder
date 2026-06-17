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

- **Overview** — `#essentials` (always shown) + collapsible sections: `#snapshot` Strategy Snapshot,
  `#assets` Asset Summary, `#mindmap` Estate Map (live diagram), `#details` Planning Detail (accordions),
  `#report` Attorney memo. Collapsible idiom: `toggleSection(id)` toggles `.collapsed`; CSS hides
  `.collapse-body` and rotates `.collapse-chev`. Most default collapsed.
- **Tax Estimator** (`#taxconsider` + `#taxest`, shown together on the `tax` tab) — `renderTax()` builds the
  Tax Considerations framework (numbered subsections collapsible via `toggleTC`/`tcOpen`); `renderTaxEstimator()`
  builds the estimator inputs; `computeTax()` fills all result spans (it does NOT call `ensureTax()` itself —
  `render()` and the tab switch run `ensureTax()` first).
- **Distribution** (`#dist`) — `renderDistribution()` / `computeDistribution()`. Beneficiary list (% allocation
  + timing), reserves (executor %, property mgmt, taxes/debts, **grandchildren milestone funding**), and
  milestone life-insurance distributions. Distributable = `netAfterAllTaxes().net − reserves`.
- **Strategy** (`#outcome`) — `renderOutcome()` / `computeOutcome()`. Toggle structures
  (trust/ILIT/FLP/QPRT/IDGT) → No-planning vs Your-setup comparison (estate tax, cap gains, property tax,
  net to heirs) + a charitable CLAT/own-NFP break-even calculator (`computeCharity`).

## Tax engine — key functions
- `ensureTax()` — defaults `S.tax`; **auto-syncs** the taxable estate to live assets (`estateAuto`) and the
  exemption to the current-year figure (`exemptionAuto`) unless the user overrode them; defaults the
  spousal-doubling `dual` from marital status.
- `estateFromAssets()` — single source for the asset-based estate (used by `renderTax` AND `ensureTax`, so the
  Tax Estimator and Tax Considerations never drift).
- `netAfterAllTaxes()` — **single source of truth** for "net to heirs" = estate − federal estate tax − entered
  capital-gains tax − real-estate capital-gains tax. Used by the Estate-tax card ("Net to heirs after all
  taxes"), the headline ("Estate remaining for heirs"), and the Distribution base — they always reconcile.
- `reCapInfo()` / `reCapTaxOn(g)` — capital-gains tax on a real-estate gain, stacked above ordinary + entered
  long-term gains (federal LTCG + CA state; NIIT not included — noted in the UI).
- Real-estate capital gains are managed **only** in the Real estate card (not the Capital gains card); they're
  excluded from the recurring "Est. annual income tax" headline.
- Helpers: `groupNum`/`unfmtNum`/`fmtNumField` (comma-formatted numeric inputs — stored values stay
  comma-free), `m$`/`num` (money/number display), `ltcgStack`/`progTax`/`stateTax`/`seTaxCalc` (tax math),
  `TAXY` (per-year brackets/limits) / `TAXC` (rates) / `STATE_TAX`.

## State shape (`S`, in localStorage)
`profile`, `people[]`, `properties[]` (incl. `basis`, `assessed`), `bankAccounts[]`, `brokerageAccounts[]`,
`llcs[]`, `subTrusts[]`, `customTrusts[]`/`trustNotes`, `customStructures[]`, `tax{...}` (filing/year/state,
ordinary/ltgain/…, estate+estateAuto, exemption+exemptionAuto, dual, deathBenefit/inILIT, profit/salary/qbi/
sstb/drd…, age/seComp, ret401/retIra/retSimple/retSep, propTaxRate), `beneficiaries[]`, `insDist[]`,
`reserves{execPct,propMgmt,taxDebt}`, `gk{...}` (grandchildren funding), `outcome{...}` + `charity{...}`
(Strategy tab), `review`. `ensureTax()`/`ensureDist()`/`ensureOutcome()` backfill defaults.

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
