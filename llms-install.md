# Installing the Compabase MCP server (instructions for AI agents)

Compabase MCP is a **remote** server – nothing to build or install locally.

1. Add this entry to the client's MCP configuration (no key needed yet):

```json
{
  "mcpServers": {
    "compabase": {
      "url": "https://compabase.com/api/mcp"
    }
  }
}
```

   If the client supports only stdio servers, use:

```json
{
  "mcpServers": {
    "compabase": {
      "command": "npx",
      "args": ["-y", "mcp-remote", "https://compabase.com/api/mcp"]
    }
  }
}
```

2. Get a key without leaving the agent: call `request_signup` with the user's e-mail, ask the user to click the magic link,
   then poll `check_signup` with the returned `setup_id` until `status: ready`. It returns an `mcpk_…` key.
   (Alternatively the user can create a key at https://compabase.com/integrations?tab=mcp.)
3. Add the key to the same entry as a header and reload the MCP config:

```json
"headers": { "Authorization": "Bearer mcpk_USER_KEY" }
```

   For stdio via `mcp-remote` append `"--header", "Authorization: Bearer mcpk_USER_KEY"` to `args`.

4. Verify: call `get_usage` (free) – it returns the plan and remaining quota.
5. Typical flow: `search_companies` → `get_company` → `get_financials` / `get_company_people`.

Docs: https://compabase.com/docs/mcp/
