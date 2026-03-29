# WARP.md

This file provides guidance to [Warp](https://warp.dev) when working with code in this repository.

## What this repo is

This repository is a **Claude Code skill** implemented entirely as Markdown.

The "runtime" artifact is `SKILL.md`: Claude Code reads the YAML frontmatter (metadata) and the prompt/instructions that follow. Everything else is supporting documentation and automation.

- `SKILL.md` is for Claude — the canonical, detailed tool reference with decision frameworks and workflow examples.
- `README.md` is for humans — installation, usage, and a compact overview.
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

### `.claude-plugin/plugin.json` + `marketplace.json`

- Plugin metadata for distribution via GitHub.
- `marketplace.json` registers the plugin so other users can install it via `extraKnownMarketplaces` in their `settings.json`.

When changing behavior/content, treat `SKILL.md` as the source of truth, and update `README.md` to stay consistent.

## Common commands

### Install the skill into Claude Code

Recommended (clone directly into Claude Code skills directory):

```bash
mkdir -p ~/.claude/skills
git clone https://github.com/fcs7/whmcs-mcp-plugin.git ~/.claude/skills/whmcs-mcp
```

Manual install/update (only the skill file):

```bash
mkdir -p ~/.claude/skills/whmcs-mcp
cp skills/whmcs-mcp/SKILL.md ~/.claude/skills/whmcs-mcp/
```

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
