# Trust Strategy Builder — repo notes

Generic workflow rules live in `~/.claude/CLAUDE.md` (they apply to every repo). This file holds
**repo-specific facts only**. Source of truth = this repo, not the chat transcript.

## Repo facts (created 2026-06-10)
- **What:** Single-page static estate/trust/entity **planning & attorney-prep** tool. Organizes
  people/roles, real estate (residence + rentals), cash/bank, brokerage/retirement, LLC options,
  sub-trusts, and executor/trustee protection. Produces an attorney-ready planning memo, intake
  previews, checklists, and a dynamic Mermaid mind map + ownership flowchart. **Planning/organization
  only — not legal/tax/financial advice; creates no legal documents.** All client-side; state in
  `localStorage`; no accounts/backend.
- **Package manager / framework:** none. Vanilla HTML/CSS/JS, no `package.json`, no build step.
  Mermaid.js loaded from CDN (`cdn.jsdelivr.net/npm/mermaid@10`).
- **Key file:** `index.html` (the entire tool: inlined CSS + JS). Data-driven forms via the `ENTITY`
  map; flag/recommendation logic in `deriveFlags()` / `propertyFlags()`; diagrams in
  `buildMindmap()` / `buildFlow()`.
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
