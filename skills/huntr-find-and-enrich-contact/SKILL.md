---
name: huntr-find-and-enrich-contact
description: Finds and enriches one person or a CSV of people using names, companies, domains, or LinkedIn URLs. Returns matched LinkedIn profiles, professional profile data, and optional work emails. Use for find this person's email, enrich these contacts, resolve LinkedIn profiles, or process a names-and-companies list.
---

# Huntr Find and Enrich Contact

Known identity → matched professional profile → optional work email → CSV.

## Shared execution contract

Verify Huntr with `get_balance`, inspect current pricing, show a concrete maximum cost, and obtain explicit approval before paid calls. Create CSV output before execution, persist every success or failure immediately, never exceed the approved ceiling, and resume only unfinished rows. If Huntr is not connected, direct the user to https://docs.tryhuntr.com/build-with-ai/huntr-mcp and never request their key in chat.

## Input gate

Accept one row or a batch containing:

- full name plus company domain;
- full name plus company name or job title;
- LinkedIn profile URL;
- any combination of these fields.

Ask whether the user needs LinkedIn URL, full professional profile, work email, or all three. Huntr does not provide personal email or personal mobile enrichment; state this if requested.

## Workflow

For each unique person:

1. If a LinkedIn URL is supplied, validate and use it.
2. Otherwise call `person_linkedin_url` with full name and the strongest available company/domain/job-title context.
3. If full profile fields were requested and a LinkedIn URL exists, call `person_linkedin`.
4. Resolve a company domain when work email is requested:
   - use the supplied domain when available;
   - otherwise use company context from the profile and a narrowly scoped `company_search`;
   - do not guess when multiple companies match.
5. Call `person_email` only with a full name and resolved company domain.
6. Persist each stage and final row immediately.

For batches, deduplicate by normalized LinkedIn URL or normalized name + domain, but preserve every input row and link duplicates to the canonical result.

## Match handling

- Strong match: proceed within the approved plan.
- Multiple plausible matches for a single lookup: present options before paid downstream enrichment.
- Multiple plausible matches in a batch: mark `ambiguous`; do not guess.
- No profile or domain: record `not_found` or `skipped` for dependent steps.

## Output

Use one CSV retaining source columns plus resolved LinkedIn URL, professional profile fields, work email, match status, source tool, error, cost, and raw result. Store experience, education, skills, and languages as compact JSON when requested.

Report profile and email coverage separately.
