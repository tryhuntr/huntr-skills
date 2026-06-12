# Shared HTTP client for Huntr scripts

Base URL: `https://api.tryhuntr.com` (override with `HUNTR_BASE_URL`).

Authentication: header `x-api-key: ${HUNTR_API_KEY}`.

Never hardcode keys. Read from environment. Add `.env` to `.gitignore`.

## Node.js client template

```javascript
const BASE = process.env.HUNTR_BASE_URL ?? "https://api.tryhuntr.com";
const KEY = process.env.HUNTR_API_KEY;
if (!KEY) throw new Error("Set HUNTR_API_KEY");

const MIN_INTERVAL_MS = 220; // stay under 5 req/s
let lastCall = 0;

async function rateLimit() {
  const wait = MIN_INTERVAL_MS - (Date.now() - lastCall);
  if (wait > 0) await new Promise((r) => setTimeout(r, wait));
  lastCall = Date.now();
}

async function huntr(method, path, body) {
  for (let attempt = 0; attempt < 4; attempt++) {
    await rateLimit();
    const res = await fetch(`${BASE}${path}`, {
      method,
      headers: {
        "content-type": "application/json",
        "x-api-key": KEY,
      },
      body: body ? JSON.stringify(body) : undefined,
    });
    if (res.status === 429) {
      const reset = Number(res.headers.get("x-ratelimit-reset"));
      const delay = reset ? Math.max(0, reset - Date.now()) : 2000 * 2 ** attempt;
      await new Promise((r) => setTimeout(r, delay));
      continue;
    }
    if (res.status === 402) {
      const err = await res.json().catch(() => ({}));
      throw new Error(`Insufficient credits: ${JSON.stringify(err)}`);
    }
    if (!res.ok) throw new Error(`${res.status}: ${await res.text()}`);
    return res.json();
  }
  throw new Error("Rate limit retries exhausted");
}

async function huntrPost(path, body) {
  return huntr("POST", path, body);
}

async function huntrGet(path) {
  return huntr("GET", path);
}
```

## Python client template

```python
import os
import time
import requests

BASE = os.environ.get("HUNTR_BASE_URL", "https://api.tryhuntr.com")
KEY = os.environ["HUNTR_API_KEY"]  # raises if missing
MIN_INTERVAL = 0.22
_last_call = 0.0

def _rate_limit():
    global _last_call
    wait = MIN_INTERVAL - (time.monotonic() - _last_call)
    if wait > 0:
        time.sleep(wait)
    _last_call = time.monotonic()

def huntr_post(path: str, body: dict) -> dict:
    for attempt in range(4):
        _rate_limit()
        res = requests.post(
            f"{BASE}{path}",
            json=body,
            headers={"x-api-key": KEY},
            timeout=120,
        )
        if res.status_code == 429:
            time.sleep(2 ** attempt)
            continue
        if res.status_code == 402:
            raise RuntimeError(f"Insufficient credits: {res.text}")
        res.raise_for_status()
        return res.json()
    raise RuntimeError("Rate limit retries exhausted")
```

## Preflight

Call `GET /balance` or `GET /pricing` before large jobs to confirm key validity and current rates.
