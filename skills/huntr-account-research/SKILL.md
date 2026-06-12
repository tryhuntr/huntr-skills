---
name: huntr-account-research
description: Runs Huntr POST /research for account briefs, buyer research, and structured schema output. Use when the user wants an account brief, pre-meeting research, buyer research, research this company, diligence question, or synthesized GTM answer on one target. Supports sync and async callback_url. Rejects bulk patterns and routes to outbound pipeline instead.
---

# Huntr Account Research

One target → synthesized brief. Recipe: https://docs.tryhuntr.com/workflows/account-research

## Workflow

1. **Confirm single target** — one company, buyer, or bounded question. If user wants many accounts + per-row enrichment, stop and use `huntr-outbound-pipeline` or `huntr-icp-list-build`.
2. **Build prompt** — include seller context, one primary target, decision to support, specific facts, date ranges for time-sensitive work.
3. **Pick tier** — `lite` (web), `standard` (+ company enrichment), `deep` (+ people/contact). Suggest `GET /pricing` preflight in script.
4. **Optional schema** — flat map `{ "field": "description" }` for structured JSON. Not JSON Schema.
5. **Generate script** — [references/api-client.md](../../references/api-client.md). Handle `422` + `BULK_ENRICHMENT_REQUIRED` (price 0). Optional `callback_url` for async + poll `/research/{id}/status`.
6. **Interpret confidence** — document `_data_source` / per-field `confidence` in output handling.

## Rules

- One primary target per `/research` call.
- Failed research: `price: 0` — retry on 500, do not double-charge assumption.
- Do not use `/research` when a direct endpoint suffices (tech stack only → `company-tech-stack`).
- See [references/common-mistakes.md](../../references/common-mistakes.md).

## Output

Runnable script + example prompt in comments + `.env.example`. For internal tools, show both sync and async patterns if latency matters.
