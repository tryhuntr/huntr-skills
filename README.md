# Huntr Skills

Agent skills for **GTM engineers** using [Huntr](https://tryhuntr.com) — turn workflow recipes into production scripts with correct endpoints, pagination, rate limits, and cost guardrails.

## Prerequisites

```bash
export HUNTR_API_KEY="hntr_live_xxxxxxxxxxxx"
```

Get a key at [tryhuntr.com/dashboard](https://tryhuntr.com/dashboard). Never commit API keys.

---

## Install skills

### 1. Recommended — `npx skills` (Claude Code, Cursor, Codex, 40+ agents)

Install all skills globally (all projects on this machine):

```bash
npx skills add tryhuntr/huntr-skills -g -y
```

Install into the **current project only** (omit `-g`):

```bash
npx skills add tryhuntr/huntr-skills -y
```

Install **one** skill:

```bash
npx skills add tryhuntr/huntr-skills --skill huntr-outbound-pipeline -g -y
```

List skills without installing:

```bash
npx skills add tryhuntr/huntr-skills --list
```

Requires [Node.js](https://nodejs.org/) for `npx`.

### 2. Git clone + manual copy

```bash
git clone https://github.com/tryhuntr/huntr-skills.git
```

Copy the skill folders you need into your agent skills directory:

| Agent | Global path | Project path |
| --- | --- | --- |
| Claude Code | `~/.claude/skills/` | `.claude/skills/` |
| Cursor | `~/.cursor/skills/` | `.cursor/skills/` |

Example — install outbound pipeline only:

```bash
cp -r huntr-skills/skills/huntr-outbound-pipeline ~/.claude/skills/
```

Each folder must contain a `SKILL.md`. Restart the agent or start a new session after copying.

### 3. Claude Code plugin

This repo includes [`.claude-plugin/plugin.json`](./.claude-plugin/plugin.json). If your Claude Code setup loads plugins from a GitHub repo, point it at `tryhuntr/huntr-skills`. For most users, `npx skills add` above is simpler.

### 4. Pin a branch or path

```bash
npx skills add https://github.com/tryhuntr/huntr-skills/tree/main/skills/huntr-account-research -g -y
```

---

## Connect Huntr in your editor (optional)

Skills generate scripts and guide workflows. To call the **live API** from Claude or Cursor, add Huntr MCP.

### Cursor

`~/.cursor/mcp.json`:

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

Restart Cursor. Use Agent mode.

### Claude Desktop

`~/Library/Application Support/Claude/claude_desktop_config.json` (Mac) or `%APPDATA%\Claude\claude_desktop_config.json` (Windows) — same `mcpServers` block as above.

### Claude Code (CLI)

```bash
claude mcp add huntr --transport http https://api.tryhuntr.com/mcp \
  -H "x-api-key: hntr_YOUR_KEY"
```

Run `/mcp` in a session to verify.

### Codex

`~/.codex/config.toml`:

```toml
[mcp_servers.huntr]
url = "https://api.tryhuntr.com/mcp"

[mcp_servers.huntr.headers]
x-api-key = "hntr_YOUR_KEY"
```

Full details: [Huntr MCP docs](https://docs.tryhuntr.com/build-with-ai/huntr-mcp).

### REST only (no MCP)

Skills can generate scripts that call `https://api.tryhuntr.com` directly with `HUNTR_API_KEY`. No MCP required.

---

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

## Repository layout

```
skills/huntr-*/SKILL.md   # Agent instructions
references/               # Shared client, pagination, mistakes
.claude-plugin/           # Claude Code plugin manifest
```

## License

MIT — see [LICENSE](./LICENSE).
