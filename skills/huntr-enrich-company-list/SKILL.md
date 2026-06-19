---
name: huntr-enrich-company-list
description: Enriches company domains, names, LinkedIn URLs, or a company CSV with selected firmographic, LinkedIn, technology, contact, hiring, social, and content data. Use for enrich these companies, clean my account list, add tech stacks, find company contacts, or append hiring and LinkedIn signals.
---

# Huntr Enrich Company List

Company rows → selected intelligence fields → related child records → CSV.

## Shared execution contract

Verify Huntr with `get_balance`, inspect current pricing, show a concrete maximum cost, and obtain explicit approval before paid calls. Create CSV output before execution, persist every success or failure immediately, never exceed the approved ceiling, and resume only unfinished rows. If Huntr is not connected, direct the user to https://docs.tryhuntr.com/build-with-ai/huntr-mcp and never request their key in chat.

## Input gate

Accept domains, company names, LinkedIn company URLs, or CSV rows. Ask the user to select:

- LinkedIn company profile and firmographics;
- technology stack;
- public website emails, phones, and social profiles;
- active job postings;
- recent LinkedIn company posts.

Explain that some groups require a domain and others require a LinkedIn company URL.

## Workflow

1. Parse and preserve all input rows.
2. Normalize domains and LinkedIn URLs.
3. Resolve missing identifiers only when needed:
   - `company_search` for a domain and base firmographics from a company name;
   - `company_linkedin_url` for a LinkedIn company URL from a domain or name.
4. Quote only the selected enrichment groups.
5. After approval, call applicable tools:
   - `linkedin_company`;
   - `tech_stack`;
   - `company_contact`;
   - `job_postings`;
   - `company_linkedin_posts`.
6. Persist every tool result immediately.
7. Keep nested job and post records in related child CSVs rather than flattening them into a single oversized cell.

## Output

Create:

- `<workflow>-companies.csv`
- `<workflow>-jobs.csv` when requested;
- `<workflow>-linkedin-posts.csv` when requested;
- `<workflow>-summary.csv`.

Company rows should expose selected fields such as industry, employee count, headquarters, funding metadata when available, technologies, public contact routes, social profiles, hiring status, and per-enrichment status.

## Edge cases

- Domain exists but LinkedIn URL cannot be resolved: continue domain-based enrichments and mark LinkedIn steps `not_found`.
- LinkedIn URL exists but domain is missing: continue LinkedIn enrichment and skip domain-only steps.
- Empty contact or job results: preserve the row and apply the tool's reported billing behavior.
- Do not treat missing public funding or technology data as proof that none exists.
