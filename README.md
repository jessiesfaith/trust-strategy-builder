# Trust Strategy Builder

Single self-contained `index.html` — an **estate / trust / entity strategy and attorney-prep tool**
(Fast Insights). It helps a user organize people, roles, real estate, cash/bank accounts, brokerage
and retirement accounts, LLC options, sub-trusts, and executor/trustee protection planning, and to
estimate taxes (income / capital-gains / estate / SE), plan beneficiary distributions, and model
planning structures **before** meeting with an attorney. Runs entirely in the browser (nothing is
uploaded; state is saved to `localStorage`).

> **Planning and organization only.** This tool does not provide legal, tax, investment, banking,
> lending, or insurance advice, and it does not create legal documents. It does not replace an estate
> attorney, CPA, financial advisor, or insurance advisor. Review all estate, trust, deed, LLC,
> beneficiary, EIN, and bank-account decisions with qualified professionals before taking action.

## What it does (preview)

- **Preview Mode** banner + sample scenario (Jessica & Chris Dougherty, California, 2 rentals, etc.).
- **Estate Formation Essentials** — six always-visible summary cards (who/charge/receives/assets/structure/reviews).
- **Strategy Snapshot** — possible review flags derived from your inputs (never "recommended final structure").
- **Asset Summary** cards with add / view-details.
- **Dynamic Estate Map** — an SVG mind map + ownership map that update as data changes (with a Mermaid
  code export).
- **Tax Estimator tab** — federal + state income / capital-gains / estate / SE tax, with a rental P&L
  summed from per-property annual rent and operating expenses (QBI = net rental P&L; modeled at FMV-reassessed property tax, rental-business properties only), a management /
  executor salary modeled as a deductible expense and taxed to the executor, a property-tax / step-up
  card, a **Cash card** (gift-now vs pass-at-death — the choice rolls into the taxed estate total), and a
  4-metric headline (total estate value · grand total tax due · estate tax with added
  structures · estate remaining after taxes).
- **Property tab** — per-property trust & tax-strategy scenarios: an 8-column table (purchase price /
  assessed / current & FMV-reassessed property tax / FMV / cap gain-loss / cap-gains tax), a deed/title
  comparison (trust vs beneficiary vs gifted) with the Fed + CA/FTB step-up callout, an FLP + IDGT
  installment-sale ("loan") freeze model, and a rental P&L card.
- **Financial tab** — retirement / brokerage / cash: step-up vs IRD, gross-estate inclusion, income tax
  to heirs, and the trust strategies for each (flagged for gross-estate impact).
- **Charity tab** — CLAT / CRT / private foundation / DAF / outright: an interactive deduction &
  estate-impact calculator (lifetime vs at-death) plus a vehicle comparison; encodes the gross-vs-taxable
  rule and the §2036 grantor-retained-CRT caveat.
- **Estate Roll-up tab** — the gross-estate centerpiece: every strategy sorted into 3 buckets (reduces the
  gross estate / reduces only the taxable estate / reduces only estate tax or after-tax value), a headline
  waterfall, and the master **probate avoidable-vs-not-avoidable list** + by-asset table.
- **Probate notes + code references** — a probate callout on every relevant tab (where probate bites and
  whether the planned move removes the asset), and a collapsible Probate · IRC · FTB/R&T **code-reference
  footer** at the bottom of every tab, with inline citations next to the notes.
- **Distribution tab** — beneficiary % allocation + timing, milestone life-insurance distributions, and
  itemized reserves (incl. milestone life-insurance distributions and a grandchildren milestone-funding reserve), starting from the true net after all taxes.
- **Strategy tab** — enter the life-insurance death benefit and toggle revocable trust / ILIT / FLP /
  QPRT / IDGT to compare no-planning vs. your setup; the toggles also drive the Tax Estimator's
  estate-tax-with-structures projection (an ILIT removes the death benefit from the estate). Includes a
  charitable break-even calculator.
- **Collapsible detail sections** — People & Roles, Profile, Real Estate, Brokerage, Cash/Bank, LLC,
  Sub-Trusts, Executor/Trustee Protection Plan, Professional Questions, Attorney/Entity intake.
- **Full CRUD** — add / edit / duplicate / remove, plus clear-form, clear-all, reset-to-sample, and
  clear-scenario, all with confirmation modals for destructive actions.
- **Attorney-Ready Planning Memo** preview (current structure, possible structure, funding / LLC /
  bank / EIN / sub-trust / trustee-protection checklists, attorney/CPA/insurance/lender questions).
- **Export Center** — PDF/CSV placeholders ("Export coming in full version"), plus working Mermaid
  code copy and a downloadable Markdown memo.
- **Preview Review Checklist + Review Notes** panel (local-only).

## Run locally

    python -m http.server 8000

Then open http://localhost:8000

No build step, no dependencies to install. Mermaid.js loads from a CDN.

## Deploy

GitHub repo `jessiesfaith/trust-strategy-builder`, Vercel project `trust-strategy-builder`, served at
`https://app.fastinsights.io/trust-strategy-builder/`:

    git push        # main is git-connected to Vercel → auto-deploys to production

Manual fallback:

    npx vercel deploy --prod --yes

The pretty path `app.fastinsights.io/trust-strategy-builder` is served by a rewrite in the
`fast-insights-app` project (same pattern as the other tools). The deploy ships only `index.html`
(see `.vercelignore`).

---

Planning/organization tool — not legal, tax, or financial advice. No legal documents are created.
