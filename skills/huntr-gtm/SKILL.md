---
name: huntr-gtm
description: Universal Huntr-only GTM planner and executor. Use for any company, people, contact, LinkedIn, website, market, account, intent, enrichment, or research request that should be completed with Huntr MCP. Inspects available Huntr tools, explains what is supported, builds and prices a plan, requests approval, executes it, and updates durable CSV files after every individual result.
---

# Huntr GTM

Give Huntr a GTM data or research objective in plain language. This skill determines which Huntr MCP tools can satisfy it, builds a safe execution plan, asks for approval, executes only approved work, and records every result immediately in CSV.

## Scope

Accept any prompt. Evaluate it strictly against the currently connected Huntr MCP tools.

Possible outcomes include:

- finding companies or people;
- building prospect and account lists;
- resolving LinkedIn URLs;
- enriching professional profiles and work emails;
- enriching companies with LinkedIn, technology, contact, hiring, or content data;
- collecting LinkedIn posts, reactions, and comments;
- analyzing public web pages;
- researching one bounded company, person, market, or business question;
- processing uploaded company, people, visitor, event, intent, or CRM-export CSV files.

Huntr does not send campaigns, update CRMs, or operate external sequencers. When a prompt includes a non-Huntr action, execute the supported Huntr data work and export CSV, but mark the external action unsupported.

## Connection gate

Call `get_balance` before planning paid execution.

If Huntr is unavailable or unauthorized:

1. stop before paid work;
2. tell the user Huntr MCP must be connected;
3. direct them to `https://docs.tryhuntr.com/build-with-ai/huntr-mcp`;
4. never request or expose their API key in chat, files, CSV, or logs.

## Mandatory guarantees

1. Treat the live Huntr MCP tool list, schemas, and `get_pricing` response as authoritative.
2. Never invent a filter, field, endpoint, match, price, or guarantee.
3. Distinguish search filters from checks performed after discovery.
4. Prefer deterministic direct tools over `research` when the required operation is known.
5. Use `research` for one bounded synthesis objective, never as a bulk enrichment loop.
6. Make no paid call before explicit approval of scope and maximum cost.
7. Never exceed the approved maximum cost.
8. Create durable CSV output before the first paid call.
9. Update and flush CSV state after every individual Huntr result.
10. Never discard failures, empty results, duplicates, unsupported items, or partial work.

## Phase 1: understand the request

Extract:

- desired business outcome;
- target entity types;
- known identifiers and supplied files;
- search criteria;
- qualification criteria;
- requested output fields;
- target count or stopping condition;
- date range where relevant;
- preferred CSV filename or destination.

Ask only questions required to build a defensible plan. Do not ask users which endpoints to call.

If the request is vague, ask for the smallest missing constraint. Examples:

- desired company profile;
- target people roles;
- number of records;
- requested enrichments;
- definition of an ambiguous phrase.

## Phase 2: inspect and classify

Inspect the available Huntr MCP tools and call `get_pricing`.

Classify every requirement:

- **Native** — directly supported by a tool input or search filter.
- **Post-qualification** — candidate records must be found first and then checked with another Huntr tool.
- **Supported output** — returned by an enrichment or research tool but not usable as a native filter.
- **Unsupported** — cannot be produced reliably by current Huntr MCP.

Example: company funding amount is not a native company-search filter. It may be investigated after company discovery through company enrichment or bounded research, with missing or uncertain values retained.

Tell the user about all post-qualification and unsupported requirements. Never silently remove or reinterpret them.

## Phase 3: select Huntr tools

Choose the smallest valid tool chain.

### Search and discovery

- `company_search_count` before paid company discovery where useful.
- `company_search` for company lists using supported firmographic filters.
- `person_search_count` before paid people discovery where useful.
- `person_search` for people by employer, title, location, skills, or profile keywords.

### Person enrichment

- `person_linkedin_url` to resolve a LinkedIn profile URL from name and context.
- `person_linkedin` for full professional profile data.
- `person_email` for a work email when full name and company domain are available.

### Company enrichment

- `company_linkedin_url` to resolve a LinkedIn company URL.
- `linkedin_company` for LinkedIn company and available funding metadata.
- `tech_stack` for detected website technologies.
- `company_contact` for public emails, phones, and company social profiles.
- `job_postings` for active hiring data.
- `company_linkedin_posts` for recent LinkedIn company content.

### LinkedIn activity

- `linkedin_post` for one post's detail.
- `linkedin_post_reactions` for paginated reactions.
- `linkedin_post_comments` for paginated comments.

### Web and research

- `find_pages` to locate relevant pages on a known domain.
- `page_scrape` for structured content from one known URL.
- `page_analyze` for a structured or narrative answer from one known page.
- `research` for a bounded synthesis question involving one primary target.

### Account operations

- `get_balance` for authentication and available credits.
- `get_pricing` for current prices.
- `get_usage` when the user asks for prior usage or when verifying recorded spend is necessary.

Do not assume a tool exists merely because it appears in this document. Confirm it is present in the live MCP tool list.

## Phase 4: build the execution graph

For each step define:

- purpose;
- tool and parameters;
- input source;
- dependencies;
- maximum calls, pages, or rows;
- requested output columns;
- missing-data behavior;
- maximum cost;
- target CSV and row relationship.

Account for:

- pagination;
- one-to-many fan-out;
- deduplication before downstream paid work;
- free-if-not-found or refund behavior;
- ambiguous identifiers;
- partial completion.

Prefer free count calls before paid searches. A paid sample or probe also requires approval.

## Phase 5: prepare CSV state

Complete this before the first paid call.

Create one primary CSV when rows share a natural shape. For mixed or one-to-many results, create linked files such as:

- `<workflow>-companies.csv`
- `<workflow>-people.csv`
- `<workflow>-engagements.csv`
- `<workflow>-jobs.csv`
- `<workflow>-posts.csv`
- `<workflow>-results.csv`
- `<workflow>-summary.csv`

Preserve every input column. Add:

- `workflow_id`
- `row_id`
- `parent_row_id`
- `entity_type`
- `step`
- `source_tool`
- `status`
- `error`
- `cost_usd`
- `processed_at`
- `raw_result_json`

Use statuses:

- `pending`
- `success`
- `not_found`
- `failed`
- `skipped`
- `duplicate`
- `unsupported`
- `ambiguous`

Seed known input or planned rows as `pending`.

### Per-result durability

After every individual MCP result:

1. update or append the affected CSV row;
2. write any newly discovered child rows;
3. record status, tool, cost, timestamp, error, and raw result;
4. flush the file to durable storage;
5. only then start the next dependent paid call.

For concurrent independent calls, persist each result as soon as that call completes. Do not wait for the whole batch or concurrency group.

If no durable writable file or downloadable artifact is available, stop before paid execution.

Never store API keys, authorization headers, secrets, or hidden reasoning.

## Phase 6: quote and approve

Present:

- interpreted outcome;
- supported native criteria;
- post-qualification checks;
- unsupported requirements;
- ordered tool plan;
- maximum calls and rows;
- CSV files and columns;
- likely cost when useful;
- maximum cost;
- current balance;
- current conditional billing behavior.

Ask for explicit approval of the concrete scope and maximum cost.

If balance is insufficient, calculate a smaller executable scope and wait for the user to choose.

External writes or integrations are outside this skill and must not be implied by approval of Huntr calls.

## Phase 7: execute

After approval:

1. execute independent steps concurrently only when safe;
2. respect live pagination, batch, and rate limits;
3. update CSV after every result;
4. deduplicate before repeated enrichment;
5. track actual cost from responses where available;
6. continue independent rows after a row-level failure;
7. mark dependency-blocked rows `skipped`;
8. stop before a call that could exceed the approved maximum.

Do not retry a paid call unless it was clearly not charged or the user approves retry risk. Never rerun completed rows.

## Phase 8: finish or resume

Return:

- CSV paths or downloadable artifacts;
- rows attempted and counts by status;
- actual recorded cost and approved maximum;
- missing fields and caveats;
- unsupported requested actions;
- the exact resume point.

To resume:

1. load existing CSV state;
2. process only `pending` rows;
3. preserve all terminal rows;
4. inspect current tools, pricing, and balance again;
5. obtain approval for any additional paid calls.

## Input-file rules

For an uploaded CSV:

1. inspect headers and sample rows;
2. map available columns to Huntr inputs;
3. report missing required identifiers;
4. preserve source order and source row IDs;
5. avoid enriching fields already populated unless the user requests verification;
6. deduplicate normalized identifiers without deleting source rows;
7. keep every classification and transformation auditable.

## Cost calculation

Never hardcode prices. Build a conservative ceiling from current pricing:

```text
search and pagination maximum
+ profile or company enrichment maximum
+ per-row contact enrichment maximum
+ post-qualification maximum
+ bounded research maximum
= approval ceiling
```

Base approval on the ceiling. Do not market a fixed cost per lead because workflows and hit rates vary.

## Boundaries

- Universal prompt intake does not mean universal execution.
- Execute only capabilities confirmed in Huntr MCP.
- Unsupported deterministic criteria do not become supported by using `research`.
- Missing public data does not prove that a condition is false.
- Do not expose internal providers.
- Do not send outreach, write to a CRM, or call non-Huntr integrations.
- CSV is the durable workflow state and final handoff.
