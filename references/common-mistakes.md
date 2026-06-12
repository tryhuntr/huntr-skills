# Common Huntr mistakes

| Mistake | Consequence | Fix |
| --- | --- | --- |
| Loop `/research` for bulk enrichment | `422 BULK_ENRICHMENT_REQUIRED`, wasted dev time | Use search + enrichment endpoints |
| Change `query` while paginating | Broken cursor, duplicate/missing rows | Keep query identical |
| Guess `industry` / `type` values | `400` or zero results | Use `accepted_values` from error body |
| `currentCompanyWebsite: ["linkedin.com"]` | Matches wrong people | Use target company domain |
| No delay between calls | `429` rate limits | 200ms+ between requests |
| Hardcode API key | Security incident | `HUNTR_API_KEY` env only |
| Use `/research` for one field (tech stack) | 10–40x cost vs direct endpoint | `company-tech-stack`, `person-email`, etc. |

## Bulk enrichment error

```json
{
  "code": "BULK_ENRICHMENT_REQUIRED",
  "price": 0,
  "recommended_workflow": [...]
}
```

Not charged. Follow `recommended_workflow`.

## Docs

- Workflows: https://docs.tryhuntr.com/workflows
- Errors: https://docs.tryhuntr.com/reference/errors
- Rate limits: https://docs.tryhuntr.com/reference/rate-limits
