# Installing the Telenow MCP server (instructions for AI agents)

Telenow is a hosted remote MCP server. Do not clone, build or run anything.

1. Add a remote MCP server named `telenow`:
   - URL: `https://api.telenow.ai/mcp`
   - Transport: Streamable HTTP (`streamableHttp` / `http`)
2. Authentication — pick one:
   - **OAuth (preferred):** leave headers empty. On first use the client opens the Telenow sign-in page; the user signs in and picks the organization, numbers and permissions.
   - **API key:** if the client has no OAuth support, ask the user for an organization API key (starts with `vai_live_`) and set the header `Authorization: Bearer <key>`. Never invent a key.

Example for Cline (`cline_mcp_settings.json`):
```json
{
  "mcpServers": {
    "telenow": {
      "type": "streamableHttp",
      "url": "https://api.telenow.ai/mcp",
      "headers": { "Authorization": "Bearer ${TELENOW_API_KEY}" },
      "disabled": false
    }
  }
}
```

3. Verify: call `list_numbers`. A list of phone numbers means the server is working. A 401 means the key is missing or wrong.

Rules when using the tools:
- `place_call`, `add_campaign_targets` and `start_campaign` reach real people and cost money. Always confirm with the user first.
- `place_call` returns at once with a `call_id`; poll `get_call` for the result and transcript.
