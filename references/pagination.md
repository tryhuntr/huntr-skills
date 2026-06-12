# Pagination (company-search and person-search)

- One page per request. Default size 100, max 200.
- Response includes `pagination.token` when more pages exist.
- Next request: same `query`, `pagination: { "size": N, "token": "..." }`.
- **Never change `query` between pages.**
- Tokens expire — complete pagination in one run when possible.

## Loop pattern

```javascript
async function paginateSearch(path, query, pageSize = 100) {
  const rows = [];
  let token;
  do {
    const page = await huntrPost(path, {
      query,
      pagination: { size: pageSize, ...(token ? { token } : {}) },
    });
    const key = path.includes("company") ? "companies" : "people";
    rows.push(...(page[key] ?? []));
    token = page.pagination?.token;
  } while (token);
  return rows;
}
```

## Cost

Charged per row returned in each page. Empty pages cost $0. Use `*-count` endpoints first (free).
