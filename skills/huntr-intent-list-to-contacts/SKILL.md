---
name: huntr-intent-list-to-contacts
description: Processes a website-visitor, intent, event, product-usage, or scored-account CSV into a prioritized outreach list. Classifies person and company rows, preserves intent signals, finds target contacts, optionally adds work emails, and saves all decisions and results to CSV.
---

# Huntr Intent List to Contacts

Intent CSV → classify and rank rows → enrich known people or find buyers at companies → outreach CSV.

## Shared execution contract

Verify Huntr with `get_balance`, inspect current pricing, show a concrete maximum cost, and obtain explicit approval before paid calls. Create CSV output before execution, persist every success or failure immediately, never exceed the approved ceiling, and resume only unfinished rows. If Huntr is not connected, direct the user to https://docs.tryhuntr.com/build-with-ai/huntr-mcp and never request their key in chat.

## Input gate

Read the uploaded CSV and identify:

- person identifiers: name, LinkedIn URL, title, company, domain;
- company identifiers: name, domain, LinkedIn URL;
- intent fields: visits, page views, tags, score, event date, source, product actions;
- existing email or profile fields.

Ask for the target-person definition, optional company-fit criteria, contacts per company, requested enrichments, and ranking rules when no score exists. Do not invent a proprietary intent score; obtain approval for any scoring formula.

## Triage

Classify every row:

- **Direct person** — person matches the target persona; enrich that person.
- **Company pivot** — person does not match, but the company is worth pursuing.
- **Company row** — find target contacts at the company.
- **Skip** — explicitly fails user-approved fit rules.
- **Insufficient data** — cannot resolve a person or company safely.

Preserve the classification and reason.

## Workflow

1. Parse, normalize, deduplicate, and summarize the input before paid work.
2. Aggregate company-level intent across related rows while preserving source rows.
3. Rank direct people and companies using the approved formula.
4. Quote direct-person enrichment and company-to-contact searches separately.
5. After approval:
   - direct people: use `person_linkedin_url` when needed, `person_linkedin` when requested, and `person_email` when name + domain are available;
   - company pivots and company rows: use `person_search` scoped by `currentCompanyWebsite`, then optional `person_email`.
6. Avoid paid enrichment when the requested field already exists in the source CSV.
7. Persist each source row, classification, contact result, and failure immediately.

## Output

Create:

- `<workflow>-triage.csv`
- `<workflow>-contacts.csv`
- `<workflow>-summary.csv`

Contacts should include priority, intent score, signal explanation, source row, source type, person and company fields, work email, status, and cost.

## Edge cases

- Empty title: treat as unknown, not automatically as a bad fit.
- Same company appears in many rows: aggregate intent and avoid duplicate searches.
- Existing person appears in person-search output: retain the direct-person row and avoid duplicate enrichment.
- Missing domain: resolve cautiously from company information or mark insufficient data.
- Large files: process in approved priority order so partial runs return the highest-value rows first.
