---
name: huntr-account-penetration
description: Finds decision-makers at one company with Huntr person-search scoped by currentCompanyWebsite, then person-email and optional person-linkedin. Use when the user wants to penetrate an account, find decision-makers at a domain, who is the CISO/CTO at, ABM contacts at one company, or enrich people at a single employer.
---

# Huntr Account Penetration

One domain → people → emails. Recipe: https://docs.tryhuntr.com/workflows/account-penetration

## Workflow

1. **Confirm domain** — single `currentCompanyWebsite` target (e.g. `stripe.com`). Not company name alone unless user has no domain.
2. **Personas** — collect title tiers (economic buyer, champion, entry). Build `currentJobTitle.include` with synonyms — contains match.
3. **Preview** — `person-search-count` (free).
4. **Search** — `person-search` with pagination if needed ([references/pagination.md](../../references/pagination.md)).
5. **Enrich** — `person-email` for selected rows; `person-linkedin` if profile detail needed.
6. **Generate script** — [references/api-client.md](../../references/api-client.md). Support multiple persona queries merged and deduped by LinkedIn URL.

## Rules

- Never `currentCompanyWebsite: ["linkedin.com"]`.
- Do not `/research` each person — use `huntr-account-research` for one buyer brief only.
- For many companies, use `huntr-outbound-pipeline`.
- See [references/common-mistakes.md](../../references/common-mistakes.md).

## Output

Runnable script + `.env.example` + CLI args for domain and optional title filter.
