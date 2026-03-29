# WHMCS MCP Skill — Claude Code

A Claude Code skill that teaches Claude how to operate WHMCS via Model Context Protocol.
The skill auto-triggers when your prompt mentions WHMCS concepts (clients, invoices, tickets, domains, etc.).

## Quick Install

```bash
mkdir -p ~/.claude/skills
git clone https://github.com/fcs7/whmcs-mcp-plugin.git ~/.claude/skills/whmcs-mcp
```

Or as a plugin (auto-updates):

```jsonc
// ~/.claude/settings.json
{
  "extraKnownMarketplaces": {
    "whmcs-mcp-plugin": {
      "source": { "source": "github", "repo": "fcs7/whmcs-mcp-plugin" }
    }
  },
  "enabledPlugins": {
    "whmcs-mcp@whmcs-mcp-plugin": true
  }
}
```

## What It Does

The skill provides Claude with a complete reference for 54 WHMCS MCP tools, organized by domain, plus decision frameworks for when to use the API vs browser automation.

### Tool Categories (54 tools)

| Category | Tools | Key Operations |
|----------|-------|----------------|
| **Clients** | 8 | list, get, create, update, close, products, domains, invoices |
| **Billing** | 6 | list/get/create/update invoices, add payment, transactions |
| **Tickets** | 5 | list, get, open, reply, update (with status lifecycle) |
| **Services** | 5 | list, suspend, unsuspend, terminate, upgrade |
| **Domains** | 4 | list, register, renew, update nameservers |
| **Orders** | 5 | list, get, accept, cancel, delete |
| **Projects** | 10 | CRUD projects/tasks, timers, messages |
| **CRM** | 8 | contacts, leads, followups, notes, kanban |
| **System** | 3 | stats, send email, activity log |

### Decision Framework

The skill teaches Claude to choose the right approach:

- **API first** — Use MCP tools for CRUD operations, data extraction, and structured actions
- **Chrome MCP fallback** — Switch to browser automation when the API doesn't cover the operation (client status changes, product configuration, visual reports, Proxmox VE, CRM kanban)

### Security Hooks

Included `hooks.json` adds safety guardrails:

- **Context detection** — Auto-suggests loading the skill when WHMCS terms appear in prompts
- **Confirmation gates** — Requires explicit user approval before destructive operations (suspend, terminate, delete, register domain, send email)

## Prerequisites

- [Claude Code](https://docs.anthropic.com/en/docs/claude-code) CLI or Desktop
- A WHMCS instance with MCP Server configured (see [whmcs-mcp](https://github.com/fcs7/whmcs-mcp))
- `jq` (for hooks)

## Repository Structure

```
whmcs-mcp-plugin/
├── .claude-plugin/
│   ├── plugin.json          # Plugin metadata (name, version, author)
│   └── marketplace.json     # Registry for GitHub-based installation
├── skills/
│   └── whmcs-mcp/
│       └── SKILL.md         # The skill definition (source of truth)
├── hooks/
│   └── hooks.json           # Security hooks (context detection + confirmation)
├── WARP.md                  # Guidance for Warp (warp.dev) editor
├── README.md                # This file (human documentation)
└── LICENSE                  # MIT
```

## Key Files

| File | Purpose |
|------|---------|
| `skills/whmcs-mcp/SKILL.md` | **Source of truth.** YAML frontmatter + full tool reference, decision frameworks, and workflow examples. Claude reads this at runtime. |
| `README.md` | Human documentation. Installation, overview, and structure. |
| `WARP.md` | Editor guidance for Warp (warp.dev). Explains repo structure and safe editing practices. |
| `hooks/hooks.json` | Automation hooks. Context detection and destructive operation gates. |

## Making Changes

### Editing the skill

1. Edit `skills/whmcs-mcp/SKILL.md` — this is the source of truth
2. Preserve YAML frontmatter formatting (`---` delimiters, `name:`, `description:`)
3. Keep tool tables consistent (Tool | Required Params | Optional Params)
4. Update `README.md` if the change affects the category table or tool count

### Versioning

- `SKILL.md` frontmatter: update `version` field (when added)
- `plugin.json`: update `version` field
- `README.md`: keep tool count in sync

## Version History

| Version | Date | Changes |
|---------|------|---------|
| 1.0.0 | 2026-03-29 | Initial release — 54 tools, 9 categories, Chrome MCP workflows, security hooks |

## License

MIT — see [LICENSE](LICENSE)
