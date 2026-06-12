---
name: huntr-signal-scanner
description: Scores accounts from Huntr company-tech-stack and company-jobs signals. Use when the user wants hiring signals, tech stack scan, account scoring from jobs, trigger-based outbound, or scan domains for stack fit. Optional /research only on hot accounts. Generates production Node.js or Python with parallel-safe rate limiting.
---

# Huntr Signal Scanner

Deterministic signals → scoring → optional research on hot accounts. Recipe: https://docs.tryhuntr.com/workflows/signal-scanner

## Workflow

1. **Input** — list of domains (file, array, or from prior company-search). Confirm scan frequency if cron.
2. **Per domain** — call `company-tech-stack` and `company-jobs` (parallel OK with aggregate rate limit under 5 req/s).
3. **Scoring** — generate user-defined or sensible default scoring in code (hiring, title keywords, stack keywords). Huntr does not score — your script does.
4. **Threshold** — if score ≥ threshold, optionally queue one `/research` per hot account (not per entire list).
5. **Generate script** — [references/api-client.md](../../references/api-client.md). Persist JSON rows: domain, score, is_hiring, stack highlights, scanned_at.
6. **Output** — runnable scanner + config block for thresholds and title/stack keywords.

## Rules

- Never `/research` every domain in a large list.
- Respect when-found billing — check `GET /pricing` in preflight comment.
- Optional `company-contact` only when user asks for site emails/phones.
- See [references/common-mistakes.md](../../references/common-mistakes.md).

## Output

Runnable script + `.env.example` + example output JSON line.
