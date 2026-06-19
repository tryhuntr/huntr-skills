---
name: huntr-build-target-list
description: Builds an outreach-ready prospect list from a company ICP and buyer roles, then optionally finds work emails and saves every company and person result to CSV. Use for requests such as build a target list, create a prospect list, find decision-makers at matching companies, or generate an outbound list.
---

# Huntr Build Target List

Company ICP → matching accounts → target people → optional work emails → CSV.

## Shared execution contract

Verify Huntr with `get_balance`, inspect current pricing, show a concrete maximum cost, and obtain explicit approval before paid calls. Create CSV output before execution, persist every success or failure immediately, never exceed the approved ceiling, and resume only unfinished rows. If Huntr is not connected, direct the user to https://docs.tryhuntr.com/build-with-ai/huntr-mcp and never request their key in chat.

## Input gate

Require:

- at least one supported company criterion;
- target company count;
- target people roles or titles;
- contacts per company;
- requested output fields.

Useful native company criteria include industry, headquarters location, headcount, headcount growth, revenue, type, keyword, name, and domain. Classify funding amount, installed technology, and other non-native criteria as post-qualification or unsupported rather than pretending they are search filters.

## Workflow

1. Convert the ICP into supported `company_search` filters.
2. Call `company_search_count` for a free size check.
3. If the result pool is too small or too broad, propose one filter change at a time.
4. Quote company retrieval, people retrieval, post-qualification, and optional email costs.
5. After approval, paginate `company_search` until the approved target or available matches are reached.
6. For each unique company domain, call `person_search_count` when useful, then `person_search` using:
   - `currentCompanyWebsite` for the company domain;
   - `currentJobTitle` with realistic title variants;
   - optional person location, skills, or keyword filters.
7. Retain the requested number of best-matching people per company.
8. Call `person_email` only when work email was requested and both full name and company domain are available.
9. Save company and people results incrementally.

## Output

Create:

- `<workflow>-companies.csv`
- `<workflow>-people.csv`
- `<workflow>-summary.csv`

Company rows should include the original criteria and returned company fields. People rows should include `parent_row_id`, company domain, name, title, LinkedIn URL, work email, and operational status columns from the core skill.

Report companies requested/found, zero-contact companies, people found, emails found, rows by status, and actual cost versus approved maximum.

## Edge cases

- Missing company domain: keep the company row, but skip domain-dependent people or email calls with a reason.
- Fewer matches than requested: return all valid rows and suggest which single criterion could be broadened.
- Duplicate companies or people: preserve source rows and mark duplicates.
- Unsupported criterion: include it in the quote as unsupported or describe the approved post-qualification method.
