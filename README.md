# Huntr Skills

[![skills.sh](https://skills.sh/b/tryhuntr/huntr-skills)](https://skills.sh/tryhuntr/huntr-skills)

Agent skills for **GTM engineers** using [Huntr](https://tryhuntr.com) — turn workflow recipes into production scripts with correct endpoints, pagination, rate limits, and cost guardrails.

## Quickstart

```bash
npx skills add tryhuntr/huntr-skills -g -y
```

Install one skill:

```bash
npx skills add tryhuntr/huntr-skills --skill huntr-outbound-pipeline -g -y
```

List skills:

```bash
npx skills add tryhuntr/huntr-skills --list
```

Requires `HUNTR_API_KEY` in your environment. Get a key at [tryhuntr.com/dashboard](https://tryhuntr.com/dashboard).

## Skills

| Skill | Workflow | Docs |
| --- | --- | --- |
| [huntr-icp-list-build](./skills/huntr-icp-list-build/SKILL.md) | ICP company list with pagination | [Recipe](https://docs.tryhuntr.com/workflows/icp-list-build) |
| [huntr-outbound-pipeline](./skills/huntr-outbound-pipeline/SKILL.md) | Companies → people → emails | [Recipe](https://docs.tryhuntr.com/workflows/outbound-pipeline) |
| [huntr-account-research](./skills/huntr-account-research/SKILL.md) | Account / buyer research briefs | [Recipe](https://docs.tryhuntr.com/workflows/account-research) |
| [huntr-signal-scanner](./skills/huntr-signal-scanner/SKILL.md) | Tech stack + jobs scoring | [Recipe](https://docs.tryhuntr.com/workflows/signal-scanner) |
| [huntr-account-penetration](./skills/huntr-account-penetration/SKILL.md) | One domain → decision-makers | [Recipe](https://docs.tryhuntr.com/workflows/account-penetration) |
| [huntr-workflow-review](./skills/huntr-workflow-review/SKILL.md) | Audit before running at scale | [Workflows hub](https://docs.tryhuntr.com/workflows) |

## Example prompts

```
Use huntr-icp-list-build: US SaaS, 50-200 employees, Software Development. Node.js, export JSON.

Use huntr-outbound-pipeline: VP Engineering at first 50 companies from that ICP.

huntr-workflow-review: audit scripts/huntr-outbound.mjs before I run 10k rows.
```

## Huntr MCP (optional)

For live API calls from your editor:

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

See [Huntr MCP docs](https://docs.tryhuntr.com/build-with-ai/huntr-mcp).

## Repository layout

```
skills/huntr-*/SKILL.md   # Agent instructions
references/               # Shared client, pagination, mistakes
.claude-plugin/           # Claude Code plugin manifest
```

## License

MIT — see [LICENSE](./LICENSE).
