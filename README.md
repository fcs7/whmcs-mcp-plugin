# WHMCS MCP Plugin — Claude Code

Plugin para Claude Code que ensina como operar WHMCS via Model Context Protocol (54 tools).

## O que inclui

- **Skill `whmcs-mcp`** — Guia de referência completo com 54 tools organizados por categoria (Clients, Billing, Tickets, Services, Domains, Orders, Projects, CRM)
- **Hooks de segurança** — Confirmação obrigatória para operações destrutivas (suspend, terminate, delete, etc.)
- **Detecção de contexto** — Auto-detecta quando o prompt envolve WHMCS e carrega a skill automaticamente
- **Workflows Chrome MCP** — Guias para operações que a API não cobre (status de cliente, relatórios, Proxmox VE, CRM visual)

## Instalação

### Via GitHub (recomendado)

No `~/.claude/settings.json`, adicione ao `extraKnownMarketplaces`:

```json
{
  "extraKnownMarketplaces": {
    "whmcs-mcp-plugin": {
      "source": {
        "source": "github",
        "repo": "fcs7/whmcs-mcp-plugin"
      }
    }
  },
  "enabledPlugins": {
    "whmcs-mcp@whmcs-mcp-plugin": true
  }
}
```

### Manual (local)

Clone o repo e adicione ao marketplace local:

```bash
git clone https://github.com/fcs7/whmcs-mcp-plugin.git ~/.claude/plugins/local/whmcs-mcp
```

Depois adicione ao `~/.claude/plugins/local/.claude-plugin/marketplace.json`:

```json
{
  "name": "whmcs-mcp",
  "description": "WHMCS MCP Plugin",
  "version": "1.0.0",
  "source": "./whmcs-mcp",
  "skills": ["./whmcs-mcp/skills/whmcs-mcp"]
}
```

## Requisitos

- Claude Code CLI ou Claude Desktop
- MCP Server WHMCS configurado (ver [whmcs-mcp](https://github.com/fcs7/whmcs-mcp))
- `jq` instalado (para os hooks)

## Estrutura

```
whmcs-mcp-plugin/
├── .claude-plugin/
│   ├── plugin.json          # Metadados do plugin
│   └── marketplace.json     # Registro para instalação
├── skills/
│   └── whmcs-mcp/
│       └── SKILL.md         # Guia de referência (54 tools)
├── hooks/
│   └── hooks.json           # Hooks de segurança WHMCS
├── README.md
└── LICENSE
```

## License

MIT
