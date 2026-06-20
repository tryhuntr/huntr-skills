---
name: huntr-gtm
description: Standalone generic Huntr workflow composer and executor. Use for any company, people, contact, LinkedIn, post, website, market, account, intent, enrichment, or research request that should be completed with Huntr MCP. Inspects live tools, schemas, and pricing; suggests relevant operations without selecting them; lets the user compose a custom workflow; validates dependencies and limits; obtains price approval; executes only approved steps; and persists every result to CSV.
---

# Huntr GTM

Turn a plain-language objective into a user-composed workflow built from the currently connected Huntr MCP tools.

This skill is standalone. Do not route to, invoke, or depend on another Huntr skill.

## Core contract

1. Treat the live MCP tool list, input schemas, and `get_pricing` response as authoritative.
2. Call `get_balance` to verify the connection, then inspect all live Huntr tools and pricing.
3. Suggest relevant operations, but never preselect optional operations.
4. Let the user add, remove, and order workflow operations.
5. Validate inputs, dependencies, pagination, fan-out, stopping conditions, and maximum calls.
6. Make no paid call before the user approves the concrete workflow and maximum cost.
7. Never exceed the approved maximum.
8. Create durable CSV state before the first paid call and flush after every individual result.
9. Never discard failures, empty results, duplicates, unsupported requirements, or partial work.
10. Never invent a filter, field, tool, price, candidate buffer, or enrichment limit.

## Phase 1: understand the objective

Extract:

- desired outcome;
- target entities;
- known identifiers, URLs, or input files;
- search and qualification criteria;
- target count or stopping condition;
- requested outputs;
- date range where relevant;
- preferred output destination.

Do not infer optional paid work from phrases such as "cold email", "outreach", "prospecting", "find this post", or "enrich this." These phrases establish intent, not the exact workflow.

Apply criteria to the entity named by the user. For example, "founders of US companies" constrains company location, not founder location.

## Phase 2: inspect live capabilities

After `get_balance`, inspect the complete live Huntr MCP tool list, schemas, and `get_pricing`.

Build an internal catalog for every customer-usable operation:

- customer-facing label;
- exact MCP tool name;
- purpose;
- required inputs;
- outputs;
- upstream dependencies;
- pagination or batch behavior;
- live price or conditional billing rule;
- availability.

Exclude internal-only operations. Do not claim an operation exists because it appears in this file or prior knowledge.

Classify each requested criterion:

- **Native filter** — accepted directly by a selected search tool.
- **Post-qualification** — requires a later operation.
- **Output only** — returned but unavailable as a filter.
- **Unsupported** — cannot be produced reliably by live tools.

Explain post-qualification and unsupported requirements before workflow selection.

## Phase 3: present the workflow composer

Present suggested operations first, followed by an explicit way to view the complete live catalog.

Use native multi-select controls when the host supports them. Otherwise use numbered checklists.

The first view must include:

```text
Suggested operations for your objective

[ ] 1. Customer-facing operation — exact_tool_name
        Purpose · required input/dependency · live price
[ ] 2. ...

Nothing is selected yet.
Reply with operation numbers, or choose "Show all Huntr operations."
```

Suggestions are not selections. Never execute or quote an optional operation solely because it was suggested.

If the user asks to show all operations, or the request is too ambiguous to produce useful suggestions, display every live customer-usable operation grouped by:

- search and discovery;
- person resolution and enrichment;
- company resolution and enrichment;
- LinkedIn company, post, reaction, and comment operations;
- website discovery, scraping, and analysis;
- research;
- account, balance, pricing, usage, and free count operations.

For each operation show:

```text
[ ] Number. Customer-facing name — exact_tool_name
    Purpose: ...
    Requires: direct input or upstream operation
    Billing: live price or condition
```

Do not hide tools merely because they appear unrelated to the initial prompt. Group or paginate the menu if needed, but preserve access to the complete live catalog.

If the user explicitly names exact operations, mark those as selected and show suggested additions separately. Ask the user to confirm the final operation set.

Avoid ambiguous labels:

- distinguish LinkedIn URL resolution from full LinkedIn profile enrichment;
- distinguish post details from post reactions and comments;
- distinguish company LinkedIn data from recent company posts;
- distinguish page scraping from page analysis;
- distinguish a discovery field from a paid enrichment call.

## Phase 4: compose and validate the workflow

Use only operations selected by the user. Do not add optional operations automatically.

For each selected step define:

- sequence and purpose;
- exact tool;
- parameters;
- input source;
- dependencies;
- maximum calls, pages, or rows;
- output columns;
- missing-data behavior;
- maximum cost;
- target CSV and row relationship.

When a selected operation lacks required input:

1. identify the missing dependency;
2. offer compatible live upstream operations or ask the user to provide the input;
3. show any additional cost;
4. wait for the user's selection.

Do not silently add the dependency.

Validate common dependency shapes:

- person email requires the identifiers accepted by the live schema;
- full profile enrichment requires the identifier accepted by the live schema;
- company enrichment requires the domain, company URL, or identifier accepted by that tool;
- post reactions or comments require the post identifier accepted by the live schema;
- page operations require a valid URL or a selected upstream page-discovery step;
- downstream fan-out must have an explicit maximum.

When an operation can return `not_found`, clarify whether the target count means:

- records attempted; or
- successful enriched records.

For a success-based target, require the user to approve an exact candidate and attempt ceiling. Never choose an over-fetch buffer.

Use free count tools only when selected by the user or clearly offered and confirmed as a free planning aid.

## Phase 5: quote and approve

Present the final workflow as:

```text
Step → exact tool → input/dependency → maximum calls → outputs → maximum cost
```

Also present:

- interpreted objective;
- native filters;
- post-qualification checks;
- unsupported requirements;
- pagination and stopping conditions;
- CSV files and columns;
- conditional billing behavior;
- maximum total cost;
- current balance.

Verify before requesting approval:

- every selected operation appears as a concrete step;
- no unselected optional operation appears;
- every dependency is satisfied or explicitly skipped;
- all fan-out and retry ceilings are bounded;
- prices come from current pricing;
- the maximum cost is conservative.

Ask for explicit approval of both the workflow and maximum spend. A workflow selection is not spending approval.

If balance is insufficient, offer smaller bounded alternatives and wait for the user's choice.

## Phase 6: prepare durable CSV state

Complete this before the first paid call.

Use one CSV when rows share a natural shape. Use linked files for mixed or one-to-many workflows, such as:

- `<workflow>-companies.csv`
- `<workflow>-people.csv`
- `<workflow>-posts.csv`
- `<workflow>-engagements.csv`
- `<workflow>-pages.csv`
- `<workflow>-results.csv`
- `<workflow>-summary.csv`

Preserve source columns and add:

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

If durable writable output is unavailable, stop before paid execution.

Never store API keys, authorization headers, secrets, or hidden reasoning.

## Phase 7: execute

After approval:

1. execute only selected and approved operations;
2. follow the approved order and dependencies;
3. run independent calls concurrently only when safe;
4. respect live pagination, batch, and rate limits;
5. deduplicate before repeated paid work;
6. persist each individual result immediately;
7. record status, source tool, cost, timestamp, error, and raw result;
8. continue independent rows after row-level failures;
9. mark dependency-blocked rows `skipped`;
10. stop before any call that could exceed an approved limit or maximum cost.

Do not retry a paid call unless it was clearly uncharged or the user approves the retry risk. Never rerun completed rows.

## Phase 8: finish or resume

Return:

- CSV paths or downloadable artifacts;
- the executed workflow;
- rows attempted and status counts;
- actual recorded cost and approved maximum;
- missing fields and caveats;
- unsupported requirements;
- exact resume point.

To resume:

1. load existing CSV state;
2. process only `pending` rows;
3. preserve terminal rows;
4. inspect live tools, pricing, schemas, and balance again;
5. obtain approval for changed steps or additional spend.

## Input files

For uploaded CSV input:

1. inspect headers and sample rows;
2. map columns to selected tool inputs;
3. report missing identifiers;
4. preserve source order and row IDs;
5. avoid replacing populated fields unless requested;
6. deduplicate normalized identifiers without deleting source rows;
7. keep every transformation auditable.

## Boundaries

- This skill composes workflows only from live Huntr MCP tools.
- It does not route to other skills.
- It does not send outreach, update CRMs, or operate external sequencers.
- Unsupported criteria do not become supported through `research`.
- `research` is for bounded synthesis, not bulk deterministic enrichment.
- Missing public data does not prove a condition is false.
- Do not expose internal providers.
- CSV is the durable workflow state and final handoff.
