---
name: whmcs-mcp
description: Use when operating WHMCS via mcp__whmcs-ntweb__* tools — managing clients, billing, tickets, services, domains, orders, projects, or CRM leads in Claude Code. Also use when the user asks for complex WHMCS operations that require navigating the admin panel via browser (Chrome MCP), such as changing client status, configuring products, accessing reports, managing Proxmox VE VPS & Cloud VMs, operating CRMv2 (ModulesGarden CRM), or using the Project Manager visual interface.
---

# WHMCS MCP — Guia de Referência

## Overview

54 tools MCP para operar o WHMCS da **NT Web** (`novo.ntweb.net.br`) via Claude Code.
Todos os tools são **deferred** — carregue com ToolSearch antes de chamar.

---

## Framework de Decisão: API ou Browser?

Antes de qualquer ação, escolha o modo correto:

### Usar WHMCS MCP (modo padrão) quando:
- A operação tem um tool correspondente na tabela de referência abaixo
- É uma operação CRUD direta (listar, criar, atualizar, deletar)
- Precisa extrair dados estruturados rapidamente
- A ação é simples e sem ambiguidade

### Usar Chrome MCP (modo browser) quando:
- A operação **não tem tool disponível** (ex: alterar status ativo/inativo do cliente)
- O pedido envolve **configurações do sistema** (módulos, gateways, planos/produtos)
- O usuário precisa de **relatórios/views** que a API não expõe
- A operação é **multi-etapa dentro do painel** admin
- Um tool MCP retornou erro e a ação pode ser feita manualmente no painel

> Regra prática: se você verificou a tabela de referência e não existe um tool para a
> operação pedida, mude para Chrome MCP automaticamente — não informe a limitação
> sem oferecer a alternativa via browser.

---

## Protocolo Chrome MCP — Planejar antes de Navegar

Quando decidir usar Chrome MCP, **sempre siga esta sequência**:

### Passo 1: Coletar dados via WHMCS MCP
Use os tools de leitura (list, get) para obter os IDs e dados necessários antes de abrir o browser. Isso evita navegar sem contexto.

```
Exemplo: Para alterar status do cliente "João"
→ whmcs_list_clients(search="João") → obter userid=42
→ Agora sei qual URL acessar: /admin/clientssummary.php?userid=42
```

### Passo 2: Criar e apresentar o plano ao usuário

Antes de executar, escreva explicitamente:
- **URL de destino**
- **Sequência de ações** (cliques, campos, valores)
- **Estado esperado ao final**
- **Confirmação:** perguntar ao usuário se pode prosseguir

```
Exemplo de plano:
"Vou fazer o seguinte no painel admin:
 1. Navegar para /admin/clientssummary.php?userid=42
 2. Clicar em 'Edit'
 3. Alterar campo 'Status' de Active para Inactive
 4. Clicar em 'Save Changes'
 Posso prosseguir?"
```

### Passo 3: Carregar tools Chrome e executar

```
ToolSearch → select:mcp__claude-in-chrome__tabs_context_mcp
ToolSearch → select:mcp__claude-in-chrome__navigate
ToolSearch → select:mcp__claude-in-chrome__computer
ToolSearch → select:mcp__claude-in-chrome__find
ToolSearch → select:mcp__claude-in-chrome__form_input
ToolSearch → select:mcp__claude-in-chrome__get_page_text
```

Executar passo a passo, tirando screenshot (`computer`) após cada ação relevante para confirmar o resultado visual.

### URLs-chave do painel NT Web

| Destino | URL |
|---------|-----|
| Admin principal | `novo.ntweb.net.br/gestor/` |
| Resumo do cliente | `/gestor/clientssummary.php?userid=ID` |
| Editar cliente | `/gestor/clients.php?action=edit&userid=ID` |
| Serviços do cliente | `/gestor/clientsservices.php?userid=ID` |
| Faturas do cliente | `/gestor/clientsinvoices.php?userid=ID` |
| Configurar produtos | `/gestor/configproducts.php` |
| Servidores | `/gestor/configservers.php` |
| Relatórios | `/gestor/reports.php` |
| Log de atividade | `/gestor/activitylog.php` |
| Pedidos | `/gestor/orders.php` |
| Tickets | `/gestor/supporttickets.php` |
| **Proxmox VE VPS & Cloud** | `/gestor/index.php?m=proxmox` |
| **CRM ModulesGarden (CRMv2)** | `/gestor/index.php?m=modulesgardencrm` |
| **Project Manager visual** | `/gestor/index.php?m=project_management` |

---

## Workflows Chrome MCP — Exemplos Concretos

### Buscar cliente no painel Chrome (Pesquisa Avançada)
```
1. [Chrome] navigate → /gestor/clientssummary.php
2. [Chrome] Usar sidebar "Pesquisa Avançada":
            → Selecionar "Clients" no 1º dropdown
            → Selecionar "Client Name" no 2º dropdown
            → Digitar nome no campo de texto
            → Clicar botão "Procurar"
3. [Chrome] A página carrega o perfil do cliente com userid correto na URL
4. [Chrome] Clicar aba "Produtos/Serviços" → dropdown mostra todos os serviços com IDs
5. [Chrome] Selecionar serviço + clicar "Ir" → ou navegar direto: ?userid=ID&productselect=SERVICEID
```
> Nota: "Login para o Painel de Controle" faz SSO direto no Plesk/cPanel — sem login manual

### Alterar status do cliente (ativo ↔ inativo)
```
1. [MCP]    whmcs_list_clients(search="nome") → userid
2. [PLANO]  Apresentar ao usuário o que será feito
3. [Chrome] navigate → /gestor/clients.php?action=edit&userid=ID
4. [Chrome] computer(screenshot) → confirmar que está na página certa
5. [Chrome] find("Status") → form_input(select "Active"/"Inactive")
6. [Chrome] find("Save Changes") → click
7. [Chrome] computer(screenshot) → confirmar resultado
```

### Configurar novo produto/plano
```
1. [PLANO]  Definir nome, preço, ciclo, grupo de produto
2. [Chrome] navigate → /admin/configproducts.php
3. [Chrome] computer(screenshot) → localizar botão "Add New Product"
4. [Chrome] preencher formulário passo a passo
5. [Chrome] computer(screenshot) → confirmar produto criado
```

### Acessar relatório financeiro detalhado
```
1. [Chrome] navigate → /gestor/reports.php
2. [Chrome] computer(screenshot) → identificar relatório desejado
3. [Chrome] get_page_text → extrair dados do relatório
4. Formatar e apresentar ao usuário
```

---

## Addons WHMCS — Chrome MCP Obrigatório

Estes addons não têm tools MCP disponíveis. Use sempre Chrome MCP.

---

### Proxmox VE VPS & Cloud

**URL:** `/gestor/index.php?m=proxmox`

**Operações disponíveis:**
- Listar VMs por cliente ou nó Proxmox
- Ver status: running / stopped / paused
- Start, Stop, Reboot, Suspend VM
- Acessar console noVNC
- Ver uso de CPU, RAM, disco
- Criar nova VM / Reinstalar SO

**Workflow — Gerenciar VM de um cliente:**
```
1. [MCP]    whmcs_list_clients(search="nome") → userid
            whmcs_get_client_products(clientid=ID) → identificar serviço Proxmox (serverid≠0)
2. [Chrome] navigate → /gestor/clientsservices.php?userid=ID&productselect=SERVICEID
3. [Chrome] computer(screenshot) → localizar botões Start/Stop/Console
4. [PLANO]  Apresentar ao usuário o que será feito antes de executar
5. [Chrome] Executar ação e confirmar com screenshot
```

**Workflow — Listar todas as VMs no painel Proxmox:**
```
1. [Chrome] navigate → /gestor/index.php?m=proxmox
2. [Chrome] computer(screenshot) → ver lista de VMs e status
3. [Chrome] get_page_text → extrair dados estruturados
```

> **Atenção:** O dashboard mostrou erro `ModulesGardenProxmoxAddonWidget: But with no role`
> — se aparecer, o usuário admin atual não tem role Proxmox configurada. Informar ao usuário.

---

### CRMv2 — ModulesGarden CRM

**URL:** `/gestor/index.php?m=modulesgardencrm`

**Quando usar Chrome vs API:**
- **API primeiro** (`whmcs_crm_*`): buscar contatos, criar leads, adicionar notas simples
- **Chrome** quando API retornar erro de tabela, ou para operações visuais:
  - Kanban com drag-and-drop de estágios
  - Notas com formatação rich text
  - Timeline de interações
  - Relatórios de leads/conversão
  - Visualização do funil de vendas

**Workflow — Acessar Kanban do CRM:**
```
1. [Chrome] navigate → /gestor/index.php?m=modulesgardencrm
2. [Chrome] computer(screenshot) → identificar view Kanban
3. [Chrome] Operar cards, mover estágios, adicionar notas
```

**Workflow — Criar lead e acompanhar no funil:**
```
1. [MCP]    whmcs_crm_create_lead(name="...", email="...") → se API disponível
2. [Chrome] navigate → /gestor/index.php?m=modulesgardencrm → confirmar criação visual
3. [Chrome] Adicionar detalhes ricos via interface
```

---

### Project Manager — Interface Visual

**URL:** `/gestor/index.php?m=project_management`

**Decisão API vs Chrome:**

| Operação | Usar |
|----------|------|
| Criar/listar/atualizar projetos e tarefas | **API** (mais rápido) |
| Gantt chart / timeline visual | **Chrome** |
| Drag-and-drop de tarefas | **Chrome** |
| Comentários com @menção / anexos | **Chrome** |
| Calendário de projetos | **Chrome** |
| Timer de tarefa | API (`whmcs_start_task_timer`) |

**Workflow — Ver projetos no painel visual:**
```
1. [MCP]    whmcs_list_projects() → listar IDs e nomes (rápido)
2. [Chrome] navigate → /gestor/index.php?m=project_management
3. [Chrome] computer(screenshot) → localizar projeto na lista
4. [Chrome] Executar operações visuais conforme solicitado
```

---

## Passo Obrigatório: Carregar Tools API

```
ToolSearch → select:mcp__whmcs-ntweb__<nome_do_tool>
```

Carregue múltiplos tools em paralelo quando souber quais vai precisar:
```
ToolSearch: select:mcp__whmcs-ntweb__whmcs_list_clients,mcp__whmcs-ntweb__whmcs_get_client
```

---

## Referência Rápida por Categoria

### Clients (8 tools)

| Tool | Parâmetros Obrigatórios | Opcionais Úteis |
|------|------------------------|-----------------|
| `whmcs_list_clients` | — | `search`, `limitnum` |
| `whmcs_get_client` | `clientid` | — |
| `whmcs_create_client` | `firstname`, `lastname`, `email`, `password2` | `phonenumber`, `address1`, `city`, `state`, `country`(padrão BR) |
| `whmcs_update_client` | `clientid` | `firstname`, `lastname`, `email`, `phonenumber`, `companyname`, `address1`… |
| `whmcs_close_client` | `clientid` | — |
| `whmcs_get_client_products` | `clientid` | — |
| `whmcs_get_client_domains` | `clientid` | — |
| `whmcs_get_client_invoices` | `clientid` | `status` |

---

### Billing (6 tools)

| Tool | Parâmetros Obrigatórios | Opcionais Úteis |
|------|------------------------|-----------------|
| `whmcs_list_invoices` | — | `clientid`, `status`(Unpaid/Paid/Overdue), `limitnum` |
| `whmcs_get_invoice` | `invoiceid` | — |
| `whmcs_create_invoice` | `userid`, `date`, `itemdescription[]`, `itemamount[]` | — |
| `whmcs_add_payment` | `invoiceid`, `transid`, `amount`, `gateway` | — |
| `whmcs_update_invoice` | `invoiceid` | `status`, `duedate` |
| `whmcs_get_transactions` | — | `clientid`, `transid` |

---

### Tickets (5 tools)

| Tool | Parâmetros Obrigatórios | Opcionais Úteis |
|------|------------------------|-----------------|
| `whmcs_list_tickets` | — | `clientid`, `status`(padrão Open), `limitnum` |
| `whmcs_get_ticket` | `ticketid` | — |
| `whmcs_open_ticket` | `clientid`, `deptid`, `subject`, `message` | `priority`(Low/Medium/High) |
| `whmcs_reply_ticket` | `ticketid`, `message` | `status`(padrão Customer-Reply) |
| `whmcs_update_ticket` | `ticketid` | `status`, `priority`, `deptid` |

**Status de ticket válidos:** `Open`, `Answered`, `Customer-Reply`, `On Hold`, `In Progress`, `Closed`

---

### Services (5 tools)

| Tool | Parâmetros Obrigatórios | Opcionais Úteis |
|------|------------------------|-----------------|
| `whmcs_list_services` | `clientid` | `status` |
| `whmcs_suspend_service` | `serviceid` | `reason` |
| `whmcs_unsuspend_service` | `serviceid` | — |
| `whmcs_terminate_service` | `serviceid` | — |
| `whmcs_upgrade_service` | `serviceid`, `newproductid`, `paymentmethod` | `newproductbillingcycle`(padrão monthly), `type`(padrão product) |

**⚠ `whmcs_terminate_service` é irreversível.** Confirme com o usuário antes.

---

### Domains (4 tools)

| Tool | Parâmetros Obrigatórios | Opcionais Úteis |
|------|------------------------|-----------------|
| `whmcs_list_domains` | — | `clientid`, `status` |
| `whmcs_register_domain` | `clientid`, `domain` | `regperiod`(padrão 1), `nameserver1`, `nameserver2` |
| `whmcs_renew_domain` | `domainid` | `regperiod`(padrão 1) |
| `whmcs_update_nameservers` | `domainid`, `ns1`, `ns2` | `ns3`, `ns4` |

---

### Orders (5 tools)

| Tool | Parâmetros Obrigatórios | Opcionais Úteis |
|------|------------------------|-----------------|
| `whmcs_list_orders` | — | `status`, `clientid`, `limitnum` |
| `whmcs_get_order` | `orderid` | — |
| `whmcs_accept_order` | `orderid` | `sendEmail`(padrão true) |
| `whmcs_cancel_order` | `orderid` | `sendEmail`(padrão false) |
| `whmcs_delete_order` | `orderid` | — |

**Ciclo de vida de pedido:** `Pending` → aceitar (`whmcs_accept_order`) → Active
ou cancelar (`whmcs_cancel_order`) → depois deletar (`whmcs_delete_order`)

---

### Project Manager (10 tools)

| Tool | Parâmetros Obrigatórios | Opcionais Úteis |
|------|------------------------|-----------------|
| `whmcs_list_projects` | — | `userid`, `status`, `completed`, `limitnum` |
| `whmcs_get_project` | `projectid` | — |
| `whmcs_create_project` | `title`, `adminid` | `userid`, `status`, `duedate`, `notes` |
| `whmcs_update_project` | `projectid` | `title`, `status`, `duedate`, `completed` |
| `whmcs_add_project_task` | `projectid`, `task`, `duedate`, `adminid` | `completed` |
| `whmcs_update_project_task` | `projectid`, `taskid` | `task`, `duedate`, `completed` |
| `whmcs_delete_project_task` | `projectid`, `taskid` | — |
| `whmcs_start_task_timer` | `projectid`, `taskid`, `adminid` | — |
| `whmcs_end_task_timer` | `projectid`, `taskid`, `adminid` | — |
| `whmcs_add_project_message` | `projectid`, `message`, `adminid` | — |

---

### CRM ModulesGarden (8 tools) ⚠ Requer addon

| Tool | Parâmetros Obrigatórios | Opcionais Úteis |
|------|------------------------|-----------------|
| `whmcs_crm_list_contacts` | — | `type`, `limit`, `offset` |
| `whmcs_crm_get_contact` | `id` | — |
| `whmcs_crm_create_lead` | `name`, `email` | `phone`, `company`, `notes` |
| `whmcs_crm_update_contact` | `id` | `name`, `email`, `phone`, `company`, `notes`, `status`, `stage` |
| `whmcs_crm_add_followup` | `contactId`, `note`, `duedate` | — |
| `whmcs_crm_add_note` | `contactId`, `note` | — |
| `whmcs_crm_list_followups` | `contactId` | `limit` |
| `whmcs_crm_get_kanban` | — | — |

**⚠ Estes tools falham com `RuntimeException` se o addon ModulesGarden CRM não estiver instalado.**

---

### System (3 tools)

| Tool | Parâmetros | Uso |
|------|-----------|-----|
| `whmcs_get_stats` | — | Receita, total de clientes, tickets abertos |
| `whmcs_send_email` | `id`, `messagename` | Envia email por template WHMCS |
| `whmcs_get_activity_log` | `limitnum`, `limitstart` | Auditoria de ações no sistema |

---

## Workflows API Comuns

### Pesquisar e visualizar cliente
```
1. whmcs_list_clients(search="nome ou email") → anotar clientid
2. whmcs_get_client(clientid=ID)
3. [opcional] whmcs_get_client_products(clientid=ID)
```

### Gerenciar fatura em atraso
```
1. whmcs_get_client_invoices(clientid=ID, status="Overdue")
2. whmcs_get_invoice(invoiceid=ID)
3. whmcs_add_payment(invoiceid=ID, transid="TX123", amount=199.90, gateway="banktransfer")
```

### Responder e fechar ticket
```
1. whmcs_get_ticket(ticketid=ID)
2. whmcs_reply_ticket(ticketid=ID, message="Resposta...", status="Answered")
3. whmcs_update_ticket(ticketid=ID, status="Closed")
```

### Aceitar pedido pendente
```
1. whmcs_list_orders(status="Pending")
2. whmcs_get_order(orderid=ID)
3. whmcs_accept_order(orderid=ID, sendEmail=true)
```

---

## Operações com Confirmação Obrigatória (PreToolUse Hook)

| Tool | Motivo |
|------|--------|
| `whmcs_suspend_service` | Afeta cliente real |
| `whmcs_terminate_service` | **Irreversível** |
| `whmcs_accept_order` | Ativa serviço real |
| `whmcs_cancel_order` | Cancela serviço real |
| `whmcs_delete_order` | Deleta permanentemente |
| `whmcs_register_domain` | Custo financeiro real |
| `whmcs_renew_domain` | Custo financeiro real |
| `whmcs_send_email` | Envia email para cliente real |
| `whmcs_upgrade_service` | Modifica cobrança real |
| `whmcs_close_client` | Fecha conta de cliente |

> Chrome MCP também requer confirmação antes de qualquer ação que modifique dados no painel.

---

## Limitações da API → Resolvidas via Chrome MCP

| Limitação | Causa | Solução via Chrome MCP |
|-----------|-------|------------------------|
| Não altera status do cliente (ativo/inativo) | `whmcs_update_client` sem campo `status` | Workflow Chrome: `/admin/clients.php?action=edit&userid=ID` |
| Não cria/edita produtos | API não expõe endpoint | Workflow Chrome: `/admin/configproducts.php` |
| Relatórios financeiros retornam `{}` | Permissão ou limitação da API key | Workflow Chrome: `/admin/reports.php` |
| CRM tools falham sem ModulesGarden | Addon externo não instalado | Verificar addon ou usar Chrome para acessar o módulo |
| `whmcs_list_services` exige `clientid` | Design do tool | Buscar cliente antes com `whmcs_list_clients` |

---

## NT Web — Contexto Específico

- **WHMCS em:** `novo.ntweb.net.br`
- **Painel admin:** `novo.ntweb.net.br/gestor/` (path renomeado por segurança)
- **Slugs de produtos:** `nt-fiber`, `nt-movel-ip`, `nt-host`, `nt-cloud`, `nt-backup`, `nt-fone`, `nt-movel`, `nt-recarga`
- **País padrão:** BR (já configurado como default nos tools)
