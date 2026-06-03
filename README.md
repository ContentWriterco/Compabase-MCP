# 🇵🇱 Polish Companies MCP Server — Compabase

[![MCP Protocol](https://img.shields.io/badge/MCP-2024--11--05-blue)](https://modelcontextprotocol.io)
[![License](https://img.shields.io/badge/License-MIT-green.svg)](LICENSE)
[![Compabase](https://img.shields.io/badge/Powered_by-Compabase-orange)](https://compabase.com)
[![Free Tier](https://img.shields.io/badge/Free_Tier-Available-brightgreen)](https://compabase.com/mcp)

**Compabase MCP** is a [Model Context Protocol](https://modelcontextprotocol.io) server that gives AI assistants — Claude, Cursor, Windsurf, and any other MCP-compatible client — direct access to **3 million+ Polish companies** from the **KRS (National Court Register)** and **CEIDG** registries.

Ask your AI about Polish business in plain language: company financials, ownership structures, industry rankings, board members, and more — all backed by real registry data.

---

## What Is This?

This is the **MCP server for Polish companies data**. Instead of building API integrations yourself, you connect your AI assistant to the Compabase MCP endpoint and talk to it naturally:

> *"Who are the top 10 manufacturing companies in Silesia by revenue?"*
> *"Show me the financial history of Orlen over the last 5 years."*
> *"How many active logistics companies are there in Mazovia?"*
> *"Who sits on the board of PKN Orlen?"*

The AI handles tool selection and query construction. You get real answers from real Polish business registry data.

---

## Quick Start

### 1. Get an MCP Key

[Sign up at compabase.com/mcp →](https://compabase.com/mcp) and generate a free MCP key (`mcpk_...`).

### 2. Connect Your AI Client

**Cursor** — add to `.cursor/mcp.json`:

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

**Claude Desktop** — add to `claude_desktop_config.json`:

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

**Generic MCP client**

| Field | Value |
|-------|-------|
| Endpoint | `https://compabase.com/api/mcp` |
| Authentication | `Authorization: Bearer <mcpk_...>` |
| Protocol | MCP Streamable HTTP (JSON-RPC 2.0) |
| MCP Version | `2024-11-05` |

### 3. Start Asking

Once connected, just talk to your AI assistant about Polish companies:

```
"What were Allegro's revenues in 2022, 2023, and 2024?"
"Find active e-commerce companies in Warsaw with revenue over 50M PLN."
"Who owns and manages LPP SA?"
```

---

## Available Tools

The Compabase MCP server exposes **6 structured tools**. Your AI client selects the right tool automatically based on your question.

### `search_companies`

**Search and filter Polish companies.** Returns up to 50 results with key financial metrics per company.

Supports filtering by: name, KRS, NIP, city, county, voivodeship, postal code, PKD industry code (primary or any), legal form, revenue, net profit, operating profit, EBITDA, total assets, share capital, estimated value, active/inactive status, email/website presence, and fiscal year.

Monetary filters default to PLN; set `currency` to `usd` or `eur` to use pre-converted columns.

```
"Top 20 construction companies in Małopolska by revenue"
"Active spółka z o.o. companies in Poznań with EBITDA over 5M PLN"
"Companies with PKD 62.01.Z (software development) in Wrocław"
```

---

### `count_companies`

**Count companies matching a filter set.** Returns a single number without fetching rows — ideal for "how many" and market sizing questions.

```
"How many active logistics companies are registered in Poland?"
"What share of Warsaw companies have a website?"
"How many companies reported revenue over 100M PLN in 2024?"
```

---

### `get_company`

**Full company profile by KRS or NIP.** Returns name, address, legal identifiers (KRS/NIP/REGON), latest financial metrics, PKD activity list, registration and closure dates.

```
"Give me the full profile of Allegro — KRS 0000627860"
"What is Orlen's NIP, address, and primary business activity?"
```

---

### `get_financials`

**Historical financial statements for a single company.** Returns all available P&L and balance sheet metrics broken down by fiscal year/period, in PLN.

Includes: `revenue_total`, `profit_net`, `profit_sales`, `ebitda`, `total_assets`, `currency`, fiscal period dates.

```
"Show me PKN Orlen's revenue and net profit for 2020–2024"
"What has Dino Polska's EBITDA trend been over the last 4 years?"
```

---

### `get_company_people`

**Board members, shareholders, and proxies for a company.** Returns display names, role labels, and relationship types (management / ownership / other).

```
"Who is on the supervisory board of CD Projekt?"
"List all shareholders of LPP SA"
"Who are the proxies authorized to act for Allegro?"
```

---

### `execute_sql`

**Read-only SQL SELECT** against the Compabase database. Used by the AI for complex aggregations, multi-table joins, and calculations that the structured tools cannot express.

> This tool is invoked automatically by the AI when needed — you do not call it directly.

---

## Use Cases

### Polish Business Intelligence for AI Assistants

Connect Compabase MCP to turn any AI assistant into a **Polish business analyst**:

- **Market research** — find all companies in a sector, filter by size, location, and activity
- **Competitive analysis** — compare revenue, EBITDA, and asset growth across competitors in an industry
- **Due diligence** — retrieve ownership structures, management teams, and multi-year financial statements
- **Lead generation** — filter active companies by PKD, voivodeship, revenue range, and contact availability
- **Company monitoring** — check legal status, recent financials, and registry changes

### MCP for Polish Business Data in Agentic Workflows

Compabase MCP is built for **agentic AI workflows** where the model needs to query Polish company data autonomously:

- Cursor agents building business dashboards
- Claude researching Polish market opportunities
- Custom agents doing automated due diligence on target companies
- LLM pipelines that need to enrich datasets with Polish registry information

### Polish Companies MCP — What Data Is Covered

| Data Type | Source | Coverage |
|-----------|--------|----------|
| Company profiles | KRS + CEIDG | 3M+ entities |
| Financial statements | KRS e-filings (eKRS) | P&L + balance sheet, multi-year |
| Ownership structures | KRS | Shareholders, holding %, values |
| Management & roles | KRS | Board members, proxies, supervisory boards |
| PKD industry codes | GUS classification | Full PKD 2007 hierarchy |
| Geographic data | KRS + CEIDG | Voivodeship, city, postal code, county |

---

## Authentication

All requests require an MCP key sent as a Bearer token:

```
Authorization: Bearer mcpk_YOUR_KEY_HERE
```

MCP keys are separate from REST API keys. Generate them at [compabase.com/mcp](https://compabase.com/mcp).

---

## Plans & Quota

Each MCP key has a monthly query quota. Every `tools/call` invocation counts as one query.

See plans and pricing at [compabase.com/pricing](https://compabase.com/pricing).

---

## Protocol Details

The Compabase MCP server implements the **MCP Streamable HTTP** transport over JSON-RPC 2.0.

| Property | Value |
|----------|-------|
| Endpoint | `POST https://compabase.com/api/mcp` |
| Transport | Streamable HTTP |
| Protocol version | `2024-11-05` |
| Capabilities | `tools` |
| Server name | `compabase` |
| Server version | `1.0.0` |

Supported JSON-RPC methods:

| Method | Description |
|--------|-------------|
| `initialize` | Handshake and capability negotiation |
| `tools/list` | List all available tools and their schemas |
| `tools/call` | Execute a tool |
| `ping` | Health / keep-alive |

---

## Related

- **[Compabase REST API](https://github.com/compabase/compabase-api)** — full REST API for Polish companies (KRS & CEIDG) with OpenAPI spec
- **[Interactive API Docs](https://compabase.com/docs/)** — try the REST endpoints in the browser
- **[compabase.com](https://compabase.com)** — browse Polish companies, search by sector, region, and financials, chat with AI about Polish business

---

## License

MIT
