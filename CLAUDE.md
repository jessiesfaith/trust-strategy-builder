# Trust Strategy Builder — repo notes

Generic workflow rules live in `~/.claude/CLAUDE.md` (they apply to every repo). This file holds
**repo-specific facts only**. Source of truth = this repo, not the chat transcript.

## Repo facts (created 2026-06-10; major expansion 2026-06-17 — see HANDOFF.md)
- **What:** Single-page static estate/trust/entity **planning & attorney-prep** tool. Tabs:
  **Overview** (people/roles, real estate, cash/bank, brokerage/retirement, LLCs, sub-trusts, Estate
  Map diagram, planning memo, executor/trustee protection — all collapsible), **Tax Estimator**
  (federal + state income/cap-gains/estate/SE/DRD/retirement; a per-property rental P&L — gross rent &
  operating expenses summed from each property, net P&L (rental-business types only, at FMV-reassessed property tax) drives QBI/§199A — plus a management/executor
  salary modeled as a deductible expense and taxed to the executor as a single filer; real-estate
  property tax (split rental-business vs personal/land–Schedule A) & cap gains; a 4-metric headline: Total estate value · Grand total tax due · Estate tax
  with added structures · Estate remaining after taxes; Tax Considerations framework), **Distribution**
  (beneficiary % allocation + timing, milestone life-insurance distributions (reserved before the split) + itemized reserves incl. a
  **grandchildren milestone-funding** reserve → distributable), and **Strategy** (enter the
  life-insurance death benefit + toggle ILIT/FLP/QPRT/IDGT → No-planning vs Your-setup comparison + a
  charitable break-even calculator; the toggles also feed the Tax Estimator's "Estate tax with added
  structures," and an ILIT removes the death benefit from the estate). Per-trust tabs + user-created
  custom trusts. **Planning/organization only —
  not legal/tax/financial advice; creates no legal documents.** Client-side; state in `localStorage`;
  no accounts/backend. A cosmetic client-side login gate prompts every visit (NOT real security).
- **Package manager / framework:** none. Vanilla HTML/CSS/JS, no `package.json`, no build step.
  Mermaid.js loaded from CDN (`cdn.jsdelivr.net/npm/mermaid@10`).
- **Key file:** `index.html` (the entire tool: inlined CSS + JS). Data-driven forms via the `ENTITY`
  map; flags in `deriveFlags()`/`propertyFlags()`; diagrams in `buildMindmap()` (Mermaid export) +
  `buildMindmapHTML()`/`buildOwnershipSVG()` (the on-screen SVG maps); tax engine in `computeTax()` +
  `renderTaxEstimator()`, with single-source helpers `rentalPnl()` (per-property gross/opex rental P&L over rental-business types, at FMV-reassessed property tax)
  and `annualIncomeTax()` (one year of during-life tax incl. the executor's own tax; QBI = net rental
  P&L); cross-tab single source of truth `netAfterAllTaxes()` reconciles the Estate card, the headline,
  and the Distribution base; tabs via `switchTab()`. **See HANDOFF.md for the full architecture map.**
- **Dev (localhost):** `python -m http.server 8000` -> http://localhost:8000 (run from repo root).
- **Build / test / lint / typecheck:** none configured. Verification = manual + URL check.
- **Git:** branch `main`. Remote `origin` = **github.com/jessiesfaith/trust-strategy-builder**
  (Jessica's account). Push via SSH host alias `github-jessica` (key `~/.ssh/jessiesfaith_ed25519`);
  `origin` is set to `git@github-jessica:jessiesfaith/trust-strategy-builder.git`. The machine's HTTPS
  credential is Chris's (`dogsleddev`) and CANNOT push here — this is Jessica's repo.
  NOTE: `vercel git connect` can't parse the `github-jessica` alias — to re-connect, temporarily
  `git remote set-url origin https://github.com/jessiesfaith/trust-strategy-builder.git`, connect,
  then restore the alias.
- **Deploy:** **`git push` -> auto-deploy** (Vercel project `trust-strategy-builder`, account
  `jessicadougherty4321-6324`, git-connected to the repo above). Manual fallback:
  `npx vercel deploy --prod --yes`.
- **Production:** `https://trust-strategy-builder.vercel.app`. The pretty path
  `app.fastinsights.io/trust-strategy-builder` is served by a redirect+rewrite added to the
  `fast-insights-app` project's `vercel.json` (same pattern as cashflow/revrec/property-for-stock).

## Conventions (match the other Fast Insights tools)
- `vercel.json` = `{ "cleanUrls": true }`. `.vercelignore` keeps everything but `index.html` +
  `vercel.json` out of the deploy.
- Guardrail tone: never "recommended final structure." Use "possible structure to discuss,"
  "attorney-review item," "flagged for CPA review," etc. Every screen carries the planning-only
  disclaimer and review badges (attorney / CPA / lender / insurance / banking-EIN).

## Environment notes (this machine)
- **OneDrive Files-On-Demand:** this repo is inside a OneDrive-synced vault. Files (incl. `.git/`)
  may be cloud placeholders that tools intermittently fail to see. Retry once; `git` reads it fine.
- **GitHub = Jessica (`jessiesfaith`).** This repo + its Vercel project are Jessica's. Before any
  GitHub work here, confirm: Chris or Jessica?
