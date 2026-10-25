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

**Claude.ai (Web / Mobile Connectors)**:
- Go to **Settings → Connectors → Add Custom Connector**
- **URL**: `https://compabase.com/mcp?apiKey=mcpk_YOUR_KEY_HERE`
- **Authentication**: `None (Detected)` (key is passed in URL)

Keys can also be created in [Integrations → MCP](https://compabase.com/integrations?tab=mcp). MCP keys (`mcpk_…`) are separate from REST API keys (`cb_…`).

| Field | Value |
|-------|-------|
| Endpoint | `POST https://compabase.com/api/mcp` or `https://compabase.com/mcp` |
| Authentication | `Authorization: Bearer mcpk_…`, `X-API-Key: mcpk_…`, or `?apiKey=mcpk_…` |
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

<!-- tools:start -->
51 tools on the full server (generated from the live `tools/list`).

### Onboarding (no key)

| Tool | Purpose |
|------|---------|
| `request_signup` | Start Compabase account setup for an email |
| `check_signup` | Poll a request_signup session |
| `list_plans` | List Compabase billing plans and limits |

### Company data

| Tool | Purpose |
|------|---------|
| `search_companies` | Search and filter Polish KRS companies (spółki) |
| `get_company` | Get company registry profile and headline financials for one latest filed period (`financials.period_to` / `financial_period_to`): revenue, profit, costs… |
| `get_financials` | Get full financial statement metrics per fiscal period |
| `get_company_people` | Get people associated with a company |
| `count_companies` | Count KRS companies matching given filters |
| `execute_sql` | Execute a read-only SQL SELECT query against the Compabase database |
| `get_company_rankings` | Get a company's revenue rank positions within its industry (PKD), region (voivodeship), and all of Poland |
| `get_financial_stats` | Get precomputed aggregate financial statistics for a sector, region, PKD branch, or country |

### Register enrichments

| Tool | Purpose |
|------|---------|
| `get_company_public_aid` | Get SUDOP public aid / grants for a company |
| `get_company_eu_funds` | Get European Funds (Fundusze Europejskie) projects for a company |
| `get_company_procurement` | Get European Commission FTS awards for a company |
| `get_company_public_tenders` | Get BZP public procurement data |
| `get_company_energy_licenses` | Get URE energy concessions for a company |
| `get_company_public_contracts` | Get contracts from Poland's Central Register of Contracts (CRU JSFP, Ministry of Finance) where the company is the contractor of a public-sector unit… |
| `get_company_nfz_contracts` | Get contracts between the company (as a healthcare provider) and Poland's National Health Fund (NFZ) |
| `get_company_nfz_queues` | Get NFZ (National Health Fund) waiting lists of a healthcare provider |
| `get_company_trademarks` | Get Polish Patent Office (UPRP) trademarks owned or applied for by the company |
| `get_company_procurement_disputes` | Get National Appeal Chamber (Krajowa Izba Odwoławcza, KIO) rulings in public procurement disputes involving the company |
| `get_company_waste_registry` | Get BDO waste and packaging register data |
| `get_company_stock_market` | Get Warsaw Stock Exchange listing for a company |
| `get_company_eu_tenders` | Get TED EU-threshold public procurement |
| `get_company_court_gazette` | Get Court and Commercial Gazette (MSiG) notices for one company |
| `get_company_sanctions` | Official MSWiA sanctions listing for one company |
| `get_company_debt_registry` | Get KRZ insolvency / restructuring proceedings |
| `get_company_financial_supervision` | KNF register (Polish Financial Supervision Authority): licences and entries of a financial company |
| `get_company_secured_liabilities` | Secured liabilities from the latest financial-statement notes (period ending 2025+): mortgages (hipoteki), registered / financial pledges (zastawy)… |
| `get_company_real_estate` | Company real estate from the latest financial statement (period ending 2025+): land incl. perpetual usufruct, buildings and premises, investment property… |
| `get_company_beneficiaries` | Get CRBR beneficial owners for a KRS company |
| `get_company_articles` | Get full text of the company's articles of association or statute (umowa spółki / statut) from RAR (Repozytorium Akt Rejestrowych) |

### Account (skip `mcp_queries`)

| Tool | Purpose |
|------|---------|
| `get_usage` | Get current billing period usage and remaining quota |
| `get_plan` | Get the user's active billing plan details |
| `list_watchlist` | List companies on the user's watchlist with company data (name, KRS, NIP, revenue, city) |
| `add_to_watchlist` | Add a company to the user's watchlist |
| `remove_from_watchlist` | Remove a company from the user's watchlist |
| `list_api_keys` | List all active REST API keys (cb_…) for the user's account |
| `create_api_key` | Create a new REST API key (cb_…) |
| `delete_api_key` | Revoke (deactivate) a REST API key by its ID |
| `list_mcp_keys` | List all active MCP keys (mcpk_…) for the user's account |
| `create_mcp_key` | Create a new MCP key (mcpk_…) |
| `delete_mcp_key` | Revoke (deactivate) an MCP key by its ID |
| `export_companies` | Bulk file export of companies matching filters (CSV / XLSX / JSON) |
| `upgrade_plan` | Create a Stripe Checkout session for a plan upgrade |
| `set_webhook_url` | Configure, view, or remove webhook for watchlist event notifications |
| `get_byok_keys` | List configured Bring-Your-Own-Key (BYOK) provider keys status |
| `set_byok_key` | Add or update a BYOK provider API key |
| `delete_byok_key` | Remove a configured BYOK provider key |
| `add_watchlist_batch` | Add multiple companies to watchlist in one call |
| `set_notification_preferences` | Get or update email notification preferences for watchlist changes |
<!-- tools:end -->

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

## Gemini CLI

```bash
gemini extensions install https://github.com/ContentWriterco/Compabase-MCP
```

On first use Gemini CLI opens the Compabase sign-in page (OAuth). Run `/mcp auth compabase` if it does not.

## Claude Code plugin

This repository is also a Claude Code plugin (MCP server + skill):

```
/plugin marketplace add ContentWriterco/Compabase-MCP
/plugin install compabase@compabase-plugins
```

## Setup guides

Step-by-step setup guides for Claude, ChatGPT, Gemini, Grok, Le Chat, Perplexity, Cursor, VS Code and Claude Code: https://compabase.com/docs/mcp/connect/
