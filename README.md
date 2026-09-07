# 🇵🇱 Polish Companies MCP Server – Compabase

[![MCP Protocol](https://img.shields.io/badge/MCP-2024--11--05-blue)](https://modelcontextprotocol.io)
[![License](https://img.shields.io/badge/License-MIT-green.svg)](LICENSE)
[![Compabase](https://img.shields.io/badge/Powered_by-Compabase-orange)](https://compabase.com)
[![Docs](https://img.shields.io/badge/Docs-Interactive_Redoc-blue)](https://compabase.com/docs/mcp/)
[![Free Tier](https://img.shields.io/badge/Free_Tier-Available-brightgreen)](https://compabase.com/mcp)

**Compabase MCP** is a [Model Context Protocol](https://modelcontextprotocol.io) server that gives AI assistants – Claude, Cursor, Windsurf, VS Code, and any MCP-compatible client – direct access to **3 million+ Polish companies** from the **KRS (National Court Register)** and **CEIDG** registries, plus financials, people, rankings, and public-register enrichments (SUDOP, BZP, TED, URE, BDO, GPW, KRZ, CRBR, MSiG, EU funds).

Ask in plain language. The model maps prompts to structured tools over Streamable HTTP (JSON-RPC 2.0).

---

## Quick Start

You can connect **without a key**. The agent signs you up and writes `mcpk_…` into the client config after email confirmation.

**Cursor / Windsurf / VS Code** – `.cursor/mcp.json` (or the equivalent MCP JSON):

```json
{
  "mcpServers": {
    "compabase": {
      "url": "https://compabase.com/api/mcp"
    }
  }
}
```

Then ask the agent to sign you up (`request_signup` with your email). After you click the magic link, poll `check_signup` until `status: ready`. Restart the client if it does not hot-reload MCP config.

**Claude Desktop** – `claude_desktop_config.json` (after you have a key):

```json
{
  "mcpServers": {
    "compabase": {
      "url": "https://compabase.com/api/mcp",
      "headers": {
        "Authorization": "Bearer mcpk_YOUR_KEY_HERE"
      }
    }
  }
}
```

Keys can also be created in [Integrations → MCP](https://compabase.com/integrations?tab=mcp). MCP keys (`mcpk_…`) are separate from REST API keys (`cb_…`).

| Field | Value |
|-------|-------|
| Endpoint | `POST https://compabase.com/api/mcp` |
| Authentication | `Authorization: Bearer mcpk_…` or `X-API-Key: mcpk_…` (optional until after signup) |
| Protocol | MCP Streamable HTTP (JSON-RPC 2.0) |
| MCP version | `2024-11-05` |
| Server | `compabase` **1.6.2** |

---

## Example prompts

- Find active IT companies in Warsaw with revenue over 5 million PLN.
- What is the latest revenue and net profit of Allegro?
- List board members of the company with KRS 0000028860.
- How many active companies in mazowieckie have primary PKD starting with 62?
- What is the median revenue in PKD section J?
- Is Orlen listed on GPW? What is the ticker and P/E?

---

## Available tools

Live traffic is always `POST https://compabase.com/api/mcp` with `method: tools/call` and `params.name` set to the tool name. Identify a company with `name`, `krs`, or `nip` when a tool accepts them. Monetary filters default to PLN.

Full schemas: [`mcp.yaml`](mcp.yaml) · [interactive docs](https://compabase.com/docs/mcp/).

### Onboarding (no key)

| Tool | Purpose |
|------|---------|
| `request_signup` | Send a magic-link email; returns `setup_id` |
| `check_signup` | Poll until email confirmed; returns `mcp_key` **once** |
| `list_plans` | Billing plans and limits; then `upgrade_plan` |

### Company data

| Tool | Purpose |
|------|---------|
| `search_companies` | Filter KRS companies (name, PKD, city, voivodeship, financials, status, contacts). Up to 50 rows. Does not browse JDG. |
| `count_companies` | Count matching KRS companies (same filters, no sort/limit) |
| `get_company` | One profile + headline financials for the **latest** filed period |
| `get_financials` | Full P&L / balance-sheet metrics per fiscal period |
| `get_company_people` | Board, shareholders, proxies, other roles |
| `get_company_rankings` | Revenue rank in PKD / region / Poland |
| `get_financial_stats` | Sector/region/PKD aggregates (median, p25–p90, n, …) |
| `execute_sql` | Read-only `SELECT` / `WITH … SELECT` for custom joins |

### Register enrichments

| Tool | Source |
|------|--------|
| `get_company_sudop` | SUDOP public aid / de minimis (NIP) |
| `get_company_bzp` | BZP procurement awards and buyer notices (NIP) |
| `get_company_ted` | TED EU-threshold tenders (NIP) |
| `get_company_ure` | URE energy concessions (NIP) |
| `get_company_bdo` | BDO waste / packaging register (NIP) |
| `get_company_gpw` | GPW / NewConnect / GlobalConnect listing + delayed quotes |
| `get_company_fe` | European Funds (MFiPR lists, cohesion) |
| `get_company_fts` | European Commission FTS grants (Horizon, LIFE, …) |
| `get_company_msig` | Court and Commercial Gazette (MSiG) |
| `get_company_krz` | KRZ insolvency / restructuring (KRS or CEIDG NIP) |
| `get_company_beneficiaries` | CRBR beneficial owners (KRS). Names only – **never PESEL** |

### Account (skip `mcp_queries`)

| Tool | Purpose |
|------|---------|
| `get_usage` | Period usage: `mcp_queries`, `api_requests`, `export_companies`, `ask_ai_credits`, `watchlist_companies` |
| `get_plan` | Active plan, limits, prepaid credit, reset date |
| `list_watchlist` / `add_to_watchlist` / `remove_from_watchlist` / `batch_add_to_watchlist` | Watchlist (KRS or NIP, including CEIDG) |
| `list_api_keys` / `create_api_key` / `delete_api_key` | REST keys (`cb_…`) |
| `list_mcp_keys` / `create_mcp_key` / `delete_mcp_key` | MCP keys (`mcpk_…`) |
| `export_companies` | Bulk CSV / XLSX / JSON (up to 500 rows). Uses the **export** meter, not `mcp_queries` |
| `upgrade_plan` | Stripe Checkout URL (`pro` / `scale`) |
| `set_webhook_url` | Watchlist change webhooks |
| `get_byok_keys` / `set_byok_key` / `delete_byok_key` | BYOK (OpenAI / Claude / Gemini), Pro+ |
| `set_notification_preferences` | Email alerts for watchlist changes |

---

## Quota

**Data tools** (search, financials, enrichments, SQL): each successful `tools/call` consumes one `mcp_queries` unit.

**Account tools** skip `mcp_queries`. `export_companies` consumes `export_companies` instead.

`initialize`, `tools/list`, and `ping` do not consume quota. Limits reset with the billing period.

| Plan | MCP queries / month | API requests | Exports |
|------|---------------------|--------------|---------|
| Free | 10 | 100 | 50 |
| Pro | 50 | 5 000 | 1 000 |
| Scale | 1 000 | 100 000 | 20 000 |
| Enterprise | Custom | Custom | Custom |

See [compabase.com/pricing](https://compabase.com/pricing).

---

## Protocol

| Property | Value |
|----------|-------|
| Endpoint | `POST https://compabase.com/api/mcp` |
| Transport | Streamable HTTP |
| Protocol version | `2024-11-05` |
| Capabilities | `tools` |
| Server name | `compabase` |
| Server version | `1.6.2` |

| Method | Description |
|--------|-------------|
| `initialize` | Handshake |
| `tools/list` | Tools and schemas |
| `tools/call` | Execute a tool |
| `ping` | Keep-alive |
| `notifications/initialized` | Client ready |

---

## Related

- **[Compabase REST API](https://github.com/ContentWriterco/compabase-api)** – OpenAPI 3.1 (`v1.yaml`)
- **[Interactive API docs](https://compabase.com/docs/)**
- **[Interactive MCP docs](https://compabase.com/docs/mcp/)**
- **[compabase.com](https://compabase.com)**

---

## License

MIT
