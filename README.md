# WHMCS MCP Plugin — Claude Code

> **Skill + hooks para operar WHMCS via Claude Code com 54 MCP tools.**
>
> Este e o **plugin client-side** (Claude Code). O **servidor MCP** (addon PHP para WHMCS) esta em **[fcs7/whmcs-mcp](https://github.com/fcs7/whmcs-mcp)**.

## Quick Install

```bash
mkdir -p ~/.claude/skills
git clone https://github.com/fcs7/whmcs-mcp-plugin.git ~/.claude/skills/whmcs-mcp
```

Ou como plugin (auto-updates) — adicione ao `~/.claude/settings.json`:

```jsonc
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

## O Que Faz

Ensina o Claude a operar WHMCS via MCP, com:

- **54 tools** organizados em 9 categorias (Clients, Billing, Tickets, Services, Domains, Orders, Projects, CRM, System)
- **Decision framework** — quando usar API vs Chrome MCP (browser automation)
- **Security hooks** — confirmacao obrigatoria para operacoes destrutivas
- **Deteccao de contexto** — auto-carrega a skill quando detecta termos WHMCS no prompt

Documentacao detalhada da skill: [`skills/whmcs-mcp/SKILL.md`](skills/whmcs-mcp/SKILL.md)

## Arquitetura: 2 Repositorios

```
┌─────────────────────────────┐      ┌──────────────────────────────┐
│  whmcs-mcp-plugin ← este    │      │  whmcs-mcp                   │
│  github.com/fcs7/           │      │  github.com/fcs7/whmcs-mcp   │
│  whmcs-mcp-plugin           │      │                              │
│                             │      │  Addon WHMCS (PHP):          │
│  Plugin Claude Code:        │      │  • mcp.php (endpoint HTTP)   │
│  • SKILL.md (referencia)    │ ──── │  • oauth.php (OAuth 2.1)     │
│  • hooks.json (seguranca)   │ MCP  │  • src/Tools/ (54 tools)     │
│  • plugin.json (metadados)  │      │  • src/Auth/ (Bearer+OAuth)  │
│                             │      │                              │
│  Roda em: Claude Code CLI   │      │  Roda em: Servidor WHMCS     │
│           Claude Desktop    │      │           PHP 8.2+           │
└─────────────────────────────┘      └──────────────────────────────┘
```

## Hooks de Seguranca

O plugin inclui hooks automaticos em `hooks/hooks.json`:

- **UserPromptSubmit** — detecta contexto WHMCS no prompt e sugere carregar a skill
- **PreToolUse** — exige confirmacao para 10 operacoes destrutivas (suspend, terminate, delete, register domain, send email, etc.)

Os hooks sao carregados automaticamente quando o plugin esta habilitado — nao precisa configurar nada.

## Estrutura do Repositorio

```
whmcs-mcp-plugin/
├── .claude-plugin/
│   ├── plugin.json              # Metadados do plugin
│   └── marketplace.json         # Registro para instalacao via GitHub
├── skills/
│   └── whmcs-mcp/
│       └── SKILL.md             # Definicao da skill (source of truth)
├── hooks/
│   └── hooks.json               # Hooks de seguranca WHMCS
├── README.md                    # Este arquivo
├── WARP.md                      # Guia para editor Warp (warp.dev)
└── LICENSE                      # MIT
```

## Pre-requisitos

1. [Claude Code](https://docs.anthropic.com/en/docs/claude-code) CLI ou Desktop
2. Servidor MCP WHMCS instalado e configurado — ver **[fcs7/whmcs-mcp](https://github.com/fcs7/whmcs-mcp)**
3. `jq` (para os hooks)

## Links

| Recurso | URL |
|---------|-----|
| **Servidor MCP (addon WHMCS)** | [fcs7/whmcs-mcp](https://github.com/fcs7/whmcs-mcp) |
| **Skill (source of truth)** | [`skills/whmcs-mcp/SKILL.md`](skills/whmcs-mcp/SKILL.md) |
| **Guia editor Warp** | [`WARP.md`](WARP.md) |

## License

MIT — ver [LICENSE](LICENSE)
