---
name: huntr-workflow-review
description: Reviews Huntr integrations before running at scale. Checks endpoint choice, /research misuse in loops, pagination bugs, filter mistakes, rate limits, and API key handling. Use when the user says review my Huntr code, audit Huntr integration, preflight Huntr job, check my Huntr script, or before running at scale. Reports pass/warn/fail; fixes only after user confirms.
---

# Huntr Workflow Review

Diagnose Huntr usage — do not spend credits testing searches during review unless the user explicitly asks for a live probe.

## Workflow

1. **Find Huntr call sites** — grep for `api.tryhuntr.com`, `/research`, `/company-search`, `/person-search`, `/person-email`, MCP tool names (`company_search`, `research`, etc.).
2. **Endpoint fit** — flag `/research` in loops, bulk enrichment via research, wrong tool for job (tech stack via research, list build via research).
3. **Pagination** — same `query` across pages, token passed correctly, no unbounded infinite loops without caps.
4. **Filters** — `currentCompanyWebsite` not linkedin.com; industry/type exact enums; title variants for person search.
5. **Auth & secrets** — no hardcoded `hntr_` keys; `.env` in `.gitignore`.
6. **Rate limits** — delays or backoff on 429; concurrency ≤ 5 req/s effective.
7. **MCP (optional)** — if Huntr MCP connected, suggest `get_pricing` / `get_balance` preflight. Docs: https://docs.tryhuntr.com/build-with-ai/huntr-mcp
8. **Report** — pass / warn / fail checklist. Remediate one fix at a time after user confirms.

## Pass / warn / fail examples

| Check | Pass | Fail |
| --- | --- | --- |
| Bulk emails | `person-search` → `person-email` | `for (row) research(...)` |
| List build | `company-search` + pagination | `/research` "find 500 companies" |
| Single brief | one `/research` | N/A |
| Key | `process.env.HUNTR_API_KEY` | literal in source |

Reference: [references/common-mistakes.md](../../references/common-mistakes.md)

## Rules

- Diagnose first; no file edits without confirmation.
- Never print full API key — mask `hntr_…last4`.
- `BULK_ENRICHMENT_REQUIRED` handling should exist in any research wrapper.
- Suggest matching workflow skill or docs URL for each fail.

## Output

Markdown checklist with prioritized fixes and links to https://docs.tryhuntr.com/workflows
