# WARP.md

This file provides guidance to [Warp](https://warp.dev) when working with code in this repository.

## What this repo is

This repository is a **Claude Code plugin** for WHMCS MCP, implemented entirely as Markdown + JSON.

The "runtime" artifact is `skills/whmcs-mcp/SKILL.md`: Claude Code reads the YAML frontmatter (metadata) and the prompt/instructions that follow. Everything else is supporting documentation and automation.

- `.mcp.json` is for Claude Code — auto-registers the WHMCS MCP server via `$WHMCS_MCP_URL`.
- `skills/whmcs-mcp/SKILL.md` is for Claude — the canonical, detailed tool reference with decision frameworks and workflow examples.
- `README.md` (root) is for humans — installation, usage, and a compact overview.
- `WARP.md` (this file) is for Warp editor — guidance on structure and safe editing.
- `hooks/hooks.json` is for Claude Code — automated behavior (context detection, confirmation gates).

## Key files (and how they relate)

### `skills/whmcs-mcp/SKILL.md`

- The actual skill definition. **Source of truth.**
- Starts with YAML frontmatter (`--- … ---`) containing `name` and `description`.
- After the frontmatter is the full reference: 54 tools across 9 categories, decision frameworks (API vs Chrome MCP), workflow examples, and security notes.

### `README.md`

- Installation and usage instructions.
- Contains a summarized tool categories table and version history.

### `hooks/hooks.json`

- Defines two automated hooks:
  - `UserPromptSubmit` — detects WHMCS context in user prompts and suggests loading the skill.
  - `PreToolUse` — gates destructive WHMCS operations (suspend, terminate, delete, etc.) behind user confirmation.

### `.mcp.json`

- Auto-registers the WHMCS MCP server when the plugin is enabled in Claude Code.
- Uses `${WHMCS_MCP_URL}` environment variable — each user sets their own WHMCS URL.
- Transport: HTTP with OAuth 2.1 (handled automatically by Claude Code).
- Server name: `whmcs-ntweb`.

### `.claude-plugin/plugin.json` + `marketplace.json`

- Plugin metadata for distribution via GitHub.
- `marketplace.json` registers the plugin so other users can install it via `extraKnownMarketplaces` in their `settings.json`.

When changing behavior/content, treat `SKILL.md` as the source of truth, and update `README.md` to stay consistent.

## Common commands

### Install as plugin (recommended)

1. Set the environment variable with your WHMCS MCP URL:

```bash
export WHMCS_MCP_URL="https://seu-whmcs.com/modules/addons/nt_mcp/mcp.php"
```

2. Install via `/plugin` in Claude Code, or add to `~/.claude/settings.json`.

The plugin auto-registers the MCP server via `.mcp.json` and handles OAuth automatically.

### How to "run" it (Claude Code)

The skill auto-triggers when Claude detects WHMCS-related context. You can also invoke it manually:

```
/whmcs-mcp
```

### Validate hooks JSON

```bash
jq . hooks/hooks.json
```

## Making changes safely

### Versioning (keep in sync)

- `skills/whmcs-mcp/SKILL.md` has a `version` field (when added to frontmatter).
- `.claude-plugin/plugin.json` has a `version` field.
- `README.md` has a "Version History" section.

If you bump the version, update all three.

### Editing SKILL.md

- Preserve valid YAML frontmatter formatting and indentation.
- Keep the tool table structure stable: `| Tool | Required Params | Optional Params |`.
- Tool count (currently 54) should match across `SKILL.md`, `README.md`, and `plugin.json` description.
- The 9 categories are: Clients, Billing, Tickets, Services, Domains, Orders, Projects, CRM, System.

### Editing hooks.json

- Must be valid JSON — broken JSON silently disables all hooks.
- The `PreToolUse` matcher is a pipe-separated list of tool names. Add/remove tools as needed.
- Test with: `echo '{"tool_name":"mcp__whmcs-ntweb__whmcs_suspend_service"}' | jq -r '.tool_name | split("__") | last'`

### Documenting non-obvious fixes

If you change the skill to handle a tricky failure mode (e.g., an API that returns unexpected data, or a Chrome MCP workflow that changed), add a note to `README.md`'s version history describing what was fixed and why.
