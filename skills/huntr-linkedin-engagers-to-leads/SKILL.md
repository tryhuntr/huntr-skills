---
name: huntr-linkedin-engagers-to-leads
description: Turns reactions and comments from a LinkedIn post into a deduplicated, optionally enriched lead list with profile, company, and work-email fields. Use for export LinkedIn post engagers, find people who reacted or commented, turn post engagement into leads, or enrich commenters.
---

# Huntr LinkedIn Engagers to Leads

LinkedIn post → reactions/comments → deduplicated people → optional profiles and work emails → CSV.

## Shared execution contract

Verify Huntr with `get_balance`, inspect current pricing, show a concrete maximum cost, and obtain explicit approval before paid calls. Create CSV output before execution, persist every success or failure immediately, never exceed the approved ceiling, and resume only unfinished rows. If Huntr is not connected, direct the user to https://docs.tryhuntr.com/build-with-ai/huntr-mcp and never request their key in chat.

## Input gate

Require:

- one public LinkedIn post URL;
- reactions, comments, or both;
- maximum pages or maximum people;
- requested enrichment fields;
- optional qualification criteria such as titles, keywords, location, or company.

Do not promise all engagers unless the user approves enough pages to exhaust pagination.

## Workflow

1. Optionally call `linkedin_post` for post context and initial comments.
2. Determine the maximum page cost for `linkedin_post_reactions` and `linkedin_post_comments`.
3. Quote collection and optional enrichment separately.
4. After approval, paginate the selected engagement tools up to the approved limit.
5. Write each engagement record immediately with source type, reaction or comment text, author URL, headline, and post URL.
6. Deduplicate people by LinkedIn URL or stable actor identifier while preserving every engagement event.
7. Apply user-approved qualification rules to returned profile fields.
8. Optionally call `person_linkedin` for full professional profiles.
9. For optional work emails:
   - derive company name from the professional profile;
   - resolve the company domain with a narrow `company_search`;
   - call `person_email` only when name and domain are unambiguous.

## Output

Create:

- `<workflow>-engagements.csv`
- `<workflow>-people.csv`
- `<workflow>-summary.csv`

People rows should include engagement counts and source types. Keep comment text and reaction events in the engagement file.

Report pages processed, engagement events, unique and qualified people, profiles and emails found, and cost.

## Edge cases

- Actor has no public LinkedIn URL: preserve the engagement and skip profile enrichment.
- Same person reacts and comments: one person row, multiple engagement rows.
- Company domain is ambiguous: do not guess or run email enrichment.
- Pagination stops early or fails: preserve completed pages and report the exact next token.
