---
name: huntr-outbound-pipeline
description: Builds a company-to-people-to-email outbound pipeline with Huntr — company-search, person-search scoped by currentCompanyWebsite, and person-email. Use when the user wants an outbound pipeline, prospect list with emails, enrich contacts at companies, CRM export script, or company then person then email workflow. Generates production Node.js or Python with rate limits and cost controls.
---

# Huntr Outbound Pipeline

Company list → decision-makers → work emails. Recipe: https://docs.tryhuntr.com/workflows/outbound-pipeline

## Workflow

1. **Clarify ICP** — company filters + title personas + geography. Cap companies and people per company if budget matters.
2. **Company list** — `company-search-count` (free) → paginate `company-search`. Reuse [huntr-icp-list-build](../huntr-icp-list-build/SKILL.md) patterns or [references/pagination.md](../../references/pagination.md).
3. **People per domain** — for each company domain, `person-search` with `currentCompanyWebsite: { include: [domain] }` and `currentJobTitle` variants (contains match — list synonyms).
4. **Emails** — `person-email` with `company_domain` + name for selected people only.
5. **Generate script** — queue or sequential loop with [references/api-client.md](../../references/api-client.md). Log costs: per-row search + when-found email.
6. **Output** — flat rows: domain, company name, first/last, title, email, linkedin_url.

## Rules

- Never `currentCompanyWebsite: ["linkedin.com"]`.
- Never loop `/research` for emails at scale — `BULK_ENRICHMENT_REQUIRED`, not charged.
- Use `person-search-count` before paid person search when sizing.
- For buyer **narrative** on one person, use `huntr-account-research` instead of this skill.
- See [references/common-mistakes.md](../../references/common-mistakes.md).

## Output

One runnable script + `.env.example` + estimated cost formula (call `GET /pricing` in comments or preflight).
