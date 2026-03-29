# WHMCS MCP Plugin

> **Plugin para operar WHMCS via Claude com 54 MCP tools.**
> Empacota Habilidade (Skill) + Conector (MCP) + Hooks de seguranca num so plugin.
>
> O **servidor MCP** (addon PHP para WHMCS) esta em **[fcs7/whmcs-mcp](https://github.com/fcs7/whmcs-mcp)**.

---

## Conceitos do Claude

O Claude organiza capacidades externas em 3 camadas:

| Conceito | O que faz | Claude Code | Claude Desktop |
|----------|-----------|:-----------:|:--------------:|
| **Habilidade** (Skill) | Ensina o Claude *como* usar ferramentas — parametros, workflows, boas praticas | Sim | Sim |
| **Conector** (Connector) | Conecta o Claude a um sistema externo via MCP — expoe as ferramentas | Sim | Sim |
| **Plugin** | Empacota Habilidade + Conector + Hooks numa instalacao so | Sim | **Nao** |

> **Este repositorio e um Plugin.** No Claude Code, instale o plugin e pronto. No Claude Desktop, configure a Habilidade e o Conector separadamente.

---

## Pre-requisito: Servidor MCP no WHMCS

Antes de tudo, instale o addon PHP no seu servidor WHMCS → **[fcs7/whmcs-mcp](https://github.com/fcs7/whmcs-mcp)**

Ao final voce tera uma URL como:
```
https://seu-whmcs.com/modules/addons/nt_mcp/mcp.php
```

---

## Setup — Claude Code

> Claude Code = CLI, Desktop app, Web app, IDE extensions (VS Code, JetBrains).

### 1. Configure a variavel de ambiente

O plugin usa `WHMCS_MCP_URL` para conectar ao seu servidor. Adicione ao shell profile (`~/.bashrc`, `~/.zshrc`):

```bash
export WHMCS_MCP_URL="https://seu-whmcs.com/modules/addons/nt_mcp/mcp.php"
```

### 2. Instale o plugin

```bash
# Opcao A: Via /plugin no Claude Code (recomendado — auto-updates)
# Digite /plugin no chat e selecione whmcs-mcp-plugin

# Opcao B: Manualmente no ~/.claude/settings.json:
```

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

**Pronto.** O plugin configura tudo automaticamente:
- **Conector**: `.mcp.json` registra o servidor MCP usando `$WHMCS_MCP_URL`
- **Habilidade**: SKILL.md com referencia dos 54 tools e carregada sob demanda
- **Hooks**: confirmacao obrigatoria antes de operacoes destrutivas
- **OAuth**: iniciado automaticamente na primeira chamada de tool

---

## Setup — Claude Desktop

> Claude Desktop = app standalone da Anthropic. Nao suporta plugins, mas suporta Habilidades e Conectores separadamente.

### 1. Adicionar Conector

Va em **Settings > Conectores** e adicione a URL do seu servidor MCP:

```
https://seu-whmcs.com/modules/addons/nt_mcp/mcp.php
```

O OAuth e iniciado automaticamente na primeira chamada de tool.

> Para configuracao avancada (token estatico, troubleshooting), veja as instrucoes no **[README do servidor MCP](https://github.com/fcs7/whmcs-mcp)**.

### 2. Adicionar Habilidade

Va em **Settings > Habilidades** e crie uma **habilidade pessoal** com o conteudo do arquivo [`skills/whmcs-mcp/SKILL.md`](skills/whmcs-mcp/SKILL.md) deste repositorio.

Isso ensina o Claude a usar os 54 tools corretamente — parametros, workflows, decision framework (API vs browser).

### Diferenca entre Claude Code e Desktop

| Recurso | Claude Code (plugin) | Claude Desktop (manual) |
|---------|:--------------------:|:-----------------------:|
| 54 MCP tools | Sim (Conector auto) | Sim (Conector manual) |
| Referencia dos tools | Sim (Habilidade auto) | Sim (Habilidade manual) |
| Hooks de seguranca | Sim | Nao |
| Deteccao de contexto WHMCS | Sim | Nao |
| Auto-updates | Sim | Nao |

---

## O Que o Plugin Inclui

```
whmcs-mcp-plugin/
├── .mcp.json                    # Conector — registra servidor MCP via $WHMCS_MCP_URL
├── skills/
│   └── whmcs-mcp/
│       └── SKILL.md             # Habilidade — referencia dos 54 tools
├── hooks/
│   └── hooks.json               # Hooks de seguranca + deteccao de contexto
├── .claude-plugin/
│   ├── plugin.json              # Metadados do plugin
│   └── marketplace.json         # Registro GitHub marketplace
├── README.md
├── WARP.md
└── LICENSE                      # MIT
```

### Habilidade (SKILL.md)

- **54 tools documentados** com parametros obrigatorios e opcionais
- **Decision framework** — quando usar API (MCP tools) vs browser (Chrome MCP)
- **Workflows prontos** — pesquisar cliente, gerenciar fatura, responder ticket, etc.
- **Addons Chrome** — Proxmox VE, CRM ModulesGarden, Project Manager visual

### Hooks de Seguranca

| Hook | Funcao |
|------|--------|
| **UserPromptSubmit** | Detecta contexto WHMCS no prompt e sugere carregar a skill |
| **PreToolUse** | Exige confirmacao para 10 operacoes destrutivas |

Operacoes protegidas: `suspend_service`, `terminate_service`, `accept_order`, `cancel_order`, `delete_order`, `register_domain`, `renew_domain`, `send_email`, `upgrade_service`, `close_client`.

---

## Links

| Recurso | URL |
|---------|-----|
| **Servidor MCP (addon WHMCS)** | [fcs7/whmcs-mcp](https://github.com/fcs7/whmcs-mcp) |
| **Habilidade (source of truth)** | [`skills/whmcs-mcp/SKILL.md`](skills/whmcs-mcp/SKILL.md) |

## License

MIT — ver [LICENSE](LICENSE)
