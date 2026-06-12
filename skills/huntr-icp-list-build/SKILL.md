---
name: huntr-icp-list-build
description: Builds a paginated company list with Huntr company-search-count and company-search. Use when the user wants an ICP list, TAM export, company search script, paginate company-search, preview list size, or export companies matching filters. Generates production Node.js or Python with env-based auth, token pagination, rate limiting, and retries.
---

# Huntr ICP List Build

Turn ICP filters into an exportable company list. Recipe: https://docs.tryhuntr.com/workflows/icp-list-build

## Workflow

1. **Clarify filters** — industry, location, headcount, domain, keywords. At least one filter required. Fields AND'd; `include` OR'd within a field.
2. **Preview (free)** — `POST /company-search-count` with the `query`. If `total` exceeds budget/cap, tighten filters with the user.
3. **Validate enums** — `industry` and `type` must be exact LinkedIn labels. On `400`, use `accepted_values` from the response.
4. **Generate script** — paginate `POST /company-search` using [references/pagination.md](../../references/pagination.md). Client: [references/api-client.md](../../references/api-client.md).
5. **Export** — write JSON/CSV to stdout or file. Include `total` from count step in run output.
6. **Tell user how to run** — `HUNTR_API_KEY=... node script.mjs` or `uv run script.py`. Provide `.env.example`.

## Rules

- Never use `/research` for list building.
- Never hardcode API keys.
- Prefer `domain` over `name` when the user supplies domains.
- Add partition guidance if `total` > 50k — split by geography or headcount band.
- See [references/common-mistakes.md](../../references/common-mistakes.md).

## Output

One runnable script + `.env.example` + brief run instructions.
