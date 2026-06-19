---
name: huntr-accounts-to-contacts
description: Converts an existing company list or CSV into a unified contact list by finding selected roles at each account and optionally adding work emails. Use when the user has target accounts and asks to find contacts, decision-makers, buyers, or employees across those companies.
---

# Huntr Accounts to Contacts

Company list → role-matched people → optional work emails → CSV.

## Shared execution contract

Verify Huntr with `get_balance`, inspect current pricing, show a concrete maximum cost, and obtain explicit approval before paid calls. Create CSV output before execution, persist every success or failure immediately, never exceed the approved ceiling, and resume only unfinished rows. If Huntr is not connected, direct the user to https://docs.tryhuntr.com/build-with-ai/huntr-mcp and never request their key in chat.

## Input gate

Require:

- at least one company name or domain;
- target roles or title families;
- contacts per company;
- whether work emails or full LinkedIn profiles are needed.

Accept pasted rows, typed lists, or CSV. Preserve every source column and normalize to `company_name` and `domain`.

## Workflow

1. Parse and report total companies, valid domains, name-only rows, duplicates, and malformed rows.
2. Resolve name-only companies with a narrowly scoped `company_search`. If several plausible companies remain, mark the row ambiguous instead of guessing.
3. Probe one representative company with `person_search_count` or a small approved search when filter quality is uncertain.
4. Quote the full run.
5. After approval, process each unique company domain:
   - `person_search` with `currentCompanyWebsite`;
   - `currentJobTitle` containing user-approved title variants;
   - optional location, skills, and keyword filters.
6. Keep up to the approved contacts per account.
7. Optionally call `person_linkedin` for full profile fields.
8. Optionally call `person_email` with full name and domain.
9. Persist each company and person result immediately.

## Output

Create:

- `<workflow>-accounts.csv`
- `<workflow>-contacts.csv`
- `<workflow>-summary.csv`

Contacts must retain `source_account_row_id` and include company, domain, name, title, LinkedIn URL, requested enrichment fields, status, error, and cost.

List zero-result and ambiguous accounts explicitly.

## Edge cases

- Name-only account cannot be resolved: preserve it as `not_found`.
- Company returns no target roles: preserve the account and add no fabricated contacts.
- Same person appears under multiple source rows: preserve source lineage and avoid duplicate paid enrichment.
- Credits stop mid-run: finish writing the current result and report the next account index.
