# Trust Strategy Builder

Single self-contained `index.html` — an **estate / trust / entity strategy and attorney-prep tool**
(Fast Insights). It helps a user organize people, roles, real estate, cash/bank accounts, brokerage
and retirement accounts, LLC options, sub-trusts, and executor/trustee protection planning **before**
meeting with an attorney. Runs entirely in the browser (nothing is uploaded; state is saved to
`localStorage`).

> **Planning and organization only.** This tool does not provide legal, tax, investment, banking,
> lending, or insurance advice, and it does not create legal documents. It does not replace an estate
> attorney, CPA, financial advisor, or insurance advisor. Review all estate, trust, deed, LLC,
> beneficiary, EIN, and bank-account decisions with qualified professionals before taking action.

## What it does (preview)

- **Preview Mode** banner + sample scenario (Jessica & Chris Dougherty, California, 2 rentals, etc.).
- **Estate Formation Essentials** — six always-visible summary cards (who/charge/receives/assets/structure/reviews).
- **Strategy Snapshot** — possible review flags derived from your inputs (never "recommended final structure").
- **Asset Summary** cards with add / view-details.
- **Dynamic Mermaid mind map + ownership flowchart** that update as data changes.
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

Vercel project `trust-strategy-builder`, served at `https://app.fastinsights.io/trust-strategy-builder/`:

    git push        # main is git-connected to Vercel → auto-deploys to production

Manual fallback:

    npx vercel deploy --prod --yes

The pretty path `app.fastinsights.io/trust-strategy-builder` is served by a rewrite in the
`fast-insights-app` project (same pattern as the other tools). The deploy ships only `index.html`
(see `.vercelignore`).

---

Planning/organization tool — not legal, tax, or financial advice. No legal documents are created.
