# Huntr Skills

Describe the GTM data you need. Huntr selects the appropriate search, enrichment, LinkedIn, web, and research tools; shows the maximum cost; waits for approval; executes the plan; and updates CSV files after every result.

These skills work with Huntr MCP in Codex, Claude Code, Cursor, and other compatible agents.

## What happens when you use a skill

Every Huntr workflow follows the same safety model:

1. Understand the requested outcome and required columns.
2. Inspect the currently available Huntr MCP tools and pricing.
3. Explain which requirements are directly supported, require post-processing, or are unsupported.
4. Show the tool plan, output files, and maximum cost.
5. Wait for explicit approval before paid calls.
6. Create CSV output before paid execution.
7. Update and flush CSV after every individual Huntr result.
8. Return completed files, coverage statistics, cost, caveats, and a resume point.

Completed rows are never intentionally rerun. Empty results, failures, skipped rows, ambiguous matches, and duplicates remain in the CSV so nothing is silently lost.

## Quick start

### 1. Install

Install all seven skills globally:

```bash
npx skills add tryhuntr/huntr-skills -g -y
```

Or install only the universal master skill:

```bash
npx skills add tryhuntr/huntr-skills --skill huntr-gtm -g -y
```

Requires [Node.js](https://nodejs.org/) for `npx`.

### 2. Connect Huntr MCP

Get an API key at [tryhuntr.com/dashboard](https://tryhuntr.com/dashboard). Never paste API keys into chat or commit them.

#### Codex

Add to `~/.codex/config.toml`:

```toml
[mcp_servers.huntr]
url = "https://api.tryhuntr.com/mcp"

[mcp_servers.huntr.headers]
x-api-key = "hntr_YOUR_KEY"
```

Start a new Codex session.

#### Claude Code

```bash
claude mcp add huntr --transport http https://api.tryhuntr.com/mcp \
  -H "x-api-key: hntr_YOUR_KEY"
```

Run `/mcp` to verify, then start a new session.

#### Cursor

Add to `~/.cursor/mcp.json`:

```json
{
  "mcpServers": {
    "huntr": {
      "type": "http",
      "url": "https://api.tryhuntr.com/mcp",
      "headers": { "x-api-key": "hntr_YOUR_KEY" }
    }
  }
}
```

Restart Cursor and use Agent mode.

#### Claude Desktop

Use the same `mcpServers` JSON in:

- macOS: `~/Library/Application Support/Claude/claude_desktop_config.json`
- Windows: `%APPDATA%\Claude\claude_desktop_config.json`

Restart Claude Desktop.

Full setup details: [Huntr MCP documentation](https://docs.tryhuntr.com/build-with-ai/huntr-mcp).

### 3. Start a workflow

Invoke a skill explicitly:

```text
Use $huntr-gtm to find 100 US software companies, add two sales leaders
per company, find available work emails, and save everything to CSV.
```

You can also describe the outcome naturally. Compatible agents may select the relevant skill automatically:

```text
Turn accounts.csv into a contact list with two security leaders per company.
```

Explicit `$skill-name` invocation is best when you want a specific workflow.

## Which skill should I use?

| You want to… | Use |
| --- | --- |
| Submit a broad or unusual Huntr request | [`$huntr-gtm`](./skills/huntr-gtm/SKILL.md) |
| Start from an ICP and build companies plus buyers | [`$huntr-build-target-list`](./skills/huntr-build-target-list/SKILL.md) |
| Start from a company list and find people | [`$huntr-accounts-to-contacts`](./skills/huntr-accounts-to-contacts/SKILL.md) |
| Find or enrich named people or LinkedIn profiles | [`$huntr-find-and-enrich-contact`](./skills/huntr-find-and-enrich-contact/SKILL.md) |
| Add intelligence to company rows | [`$huntr-enrich-company-list`](./skills/huntr-enrich-company-list/SKILL.md) |
| Turn LinkedIn post engagement into leads | [`$huntr-linkedin-engagers-to-leads`](./skills/huntr-linkedin-engagers-to-leads/SKILL.md) |
| Turn visitor, event, intent, or scored-account data into contacts | [`$huntr-intent-list-to-contacts`](./skills/huntr-intent-list-to-contacts/SKILL.md) |

When unsure, use `$huntr-gtm`. It evaluates the prompt against the live Huntr MCP tools and builds the appropriate plan.

## Skills

### `huntr-gtm` — universal Huntr planner

Use this for any Huntr-supported GTM data or research objective, particularly when the request combines several operations or does not fit a focused skill.

**You provide:** a plain-language outcome, target count, criteria, requested fields, and optionally a CSV.

**It can combine:** company and people search, professional profiles, work emails, company intelligence, LinkedIn activity, page extraction, and bounded research.

**You receive:** one or more linked CSV files, execution summary, coverage, actual cost, limitations, and resume state.

Example:

```text
Use $huntr-gtm to find US fintech companies, identify VP Marketing leaders,
enrich work emails, segment by employee count, and save every result to CSV.
```

The skill accepts any prompt but executes only capabilities currently available through Huntr MCP. CRM updates, email sequencing, and external campaign delivery are outside Huntr.

### `huntr-build-target-list`

Use this when you need to start from a company ICP and finish with an outreach-ready company and contact list.

**You provide:**

- company filters such as industry, headquarters, headcount, growth, revenue, type, or keywords;
- company target count;
- target roles or titles;
- contacts per company;
- desired output fields.

**You receive:**

- companies CSV;
- people CSV linked to company rows;
- summary CSV;
- optional work emails.

Example:

```text
Use $huntr-build-target-list to find 75 US cybersecurity companies with
50–500 employees, then find two sales leaders at each and their work emails.
```

Conditions that are not native company-search filters, such as an exact funding amount or installed technology, are presented as post-qualification steps when Huntr can investigate them.

### `huntr-accounts-to-contacts`

Use this when you already have target companies and need the right people at each account.

**You provide:** company names or domains, target roles, contacts per company, and optional enrichment fields.

**Input formats:** uploaded CSV, pasted rows, or a typed list.

**You receive:** preserved account rows, a linked contacts CSV, zero-result accounts, ambiguous company matches, and optional professional profiles or work emails.

Example:

```text
Use $huntr-accounts-to-contacts on accounts.csv. Find up to three CISOs,
security directors, or heads of security at each company and add work emails.
```

### `huntr-find-and-enrich-contact`

Use this for one person or a batch of people when you know some combination of name, company, domain, or LinkedIn URL.

**You provide:** person identifiers and requested fields.

**It can return:**

- resolved LinkedIn profile URL;
- professional profile details;
- current role and company;
- work email when a full name and company domain are available.

**It does not provide:** personal email or personal mobile-phone enrichment.

Example:

```text
Use $huntr-find-and-enrich-contact on contacts.csv. Resolve missing LinkedIn
profiles, append current job information, and find available work emails.
```

Ambiguous matches are not guessed. They are recorded for review.

### `huntr-enrich-company-list`

Use this when you have company names, domains, LinkedIn company URLs, or a company CSV and want to append intelligence.

**Available enrichment groups:**

- LinkedIn company profile and firmographics;
- available funding metadata;
- technology stack;
- public website emails, phones, and company social profiles;
- active job postings;
- recent LinkedIn company posts.

**You receive:** a primary companies CSV plus related jobs and posts CSVs when requested.

Example:

```text
Use $huntr-enrich-company-list on companies.csv. Add LinkedIn firmographics,
technology stacks, public contact routes, active jobs, and recent posts.
```

### `huntr-linkedin-engagers-to-leads`

Use this when you have a LinkedIn post URL and want to turn reactions or comments into a qualified people list.

**You provide:**

- LinkedIn post URL;
- reactions, comments, or both;
- maximum pages or people;
- optional title, location, company, or keyword qualification;
- requested profile or email enrichment.

**You receive:**

- every collected engagement event;
- deduplicated people;
- engagement counts and sources;
- optional professional profiles and work emails;
- pagination resume state.

Example:

```text
Use $huntr-linkedin-engagers-to-leads on this post. Collect reactions and
comments, keep VP Sales and above, enrich profiles and work emails, and save CSV.
```

### `huntr-intent-list-to-contacts`

Use this when you have website visitors, event attendees, product activity, scored accounts, or another intent-style CSV and need a prioritized outreach list.

**You provide:**

- the input CSV;
- target-person definition;
- optional company-fit rules;
- contacts per company;
- requested enrichment;
- ranking rules when the file has no existing score.

**The skill classifies rows as:**

- direct person to enrich;
- company to prospect into;
- explicit fit-rule skip;
- insufficient data.

**You receive:** triage CSV, prioritized contacts CSV, summary CSV, preserved source signals, and auditable classification reasons.

Example:

```text
Use $huntr-intent-list-to-contacts on visitors.csv. Prioritize repeat visitors,
enrich matching RevOps leaders directly, and find two RevOps leaders at every
other qualified company.
```

## Cost and approval

Skills call Huntr’s current pricing tools rather than relying on prices written in these files.

Before paid execution, the agent shows:

- interpreted request;
- supported and unsupported criteria;
- ordered tool plan;
- maximum calls or rows;
- expected CSV files;
- likely cost when useful;
- maximum cost;
- current credit balance.

The **maximum cost** is the ceiling the workflow must not exceed. Actual cost may be lower because some Huntr operations are free when nothing is found or are refunded on failure.

Changing the scope materially—such as adding another enrichment to every row—requires a revised quote and new approval.

## CSV behavior

CSV is both the output and the durable execution state.

- Files are created in the agent’s writable workspace or another destination you specify.
- If you do not specify a filename, the agent chooses descriptive workflow filenames.
- Input columns are preserved.
- Every planned row begins as `pending`.
- Every individual result is written and flushed immediately.
- Nested entities use linked files with `workflow_id`, `row_id`, and `parent_row_id`.
- Failures, empty results, duplicates, ambiguous matches, skipped rows, and unsupported fields remain visible.
- API keys and authentication headers are never stored.

Common outputs include:

```text
workflow-companies.csv
workflow-people.csv
workflow-engagements.csv
workflow-jobs.csv
workflow-posts.csv
workflow-summary.csv
```

If execution is interrupted, run the skill again and point it to the existing CSV files. It processes only unfinished `pending` rows after checking current pricing, balance, and approval.

## Supported versus unsupported requests

Every requirement is classified before execution:

- **Native:** directly supported by a Huntr search filter.
- **Post-qualification:** candidates are found first and checked with another Huntr tool.
- **Supported output:** available as returned data but not as a search filter.
- **Unsupported:** unavailable through the connected Huntr MCP tools.

Example:

> “Find companies that raised at least $10M.”

Funding amount is not a native company-search filter. Huntr can first discover candidate companies and then investigate available funding metadata, but missing public data must remain uncertain rather than being treated as a failed match.

If a prompt asks to push contacts to a CRM or sequencer, the skills can prepare the data and CSV but will mark the external action unsupported.

## Additional installation options

Install into the current project only:

```bash
npx skills add tryhuntr/huntr-skills -y
```

Install a focused skill:

```bash
npx skills add tryhuntr/huntr-skills \
  --skill huntr-linkedin-engagers-to-leads -g -y
```

List available skills:

```bash
npx skills add tryhuntr/huntr-skills --list
```

Target specific agents:

```bash
npx skills add tryhuntr/huntr-skills -g -y \
  -a cursor \
  -a claude-code \
  -a codex
```

After installation, start a new agent session.

The installer may report both “Installed 7 skills” and failures for agents that do not support global skill installation, such as PromptScript. This does not mean installation failed for Codex, Claude Code, or Cursor.

### Manual installation

```bash
git clone https://github.com/tryhuntr/huntr-skills.git
```

Copy any folder under `skills/` into your agent’s skills directory. Each copied folder must keep its `SKILL.md` and optional `agents/` metadata.

## Repository layout

```text
skills/
  huntr-gtm/
  huntr-build-target-list/
  huntr-accounts-to-contacts/
  huntr-find-and-enrich-contact/
  huntr-enrich-company-list/
  huntr-linkedin-engagers-to-leads/
  huntr-intent-list-to-contacts/
.claude-plugin/
```

## License

MIT — see [LICENSE](./LICENSE).
