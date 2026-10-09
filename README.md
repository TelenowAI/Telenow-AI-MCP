<p align="center">
  <img src="assets/logo-512.png" width="96" alt="Telenow logo">
</p>

<h1 align="center">Telenow MCP Server</h1>
<p align="center"><b>Let your AI assistant make real phone calls.</b></p>

<p align="center">
  <a href="https://telenow.ai/docs/assistant-connectors">Docs</a> ·
  <a href="https://telenow.ai/docs/mcp-tools">Tools reference</a> ·
  <a href="https://telenow.ai/docs/mcp-developers">Protocol &amp; OAuth</a> ·
  <a href="https://telenow.ai/docs/mcp-costs">Costs per minute</a>
</p>

Telenow is a **remote MCP server** that gives Claude, ChatGPT, Cursor, Grok, Gemini CLI and any MCP client a real phone. Place calls from your own Telenow numbers, get the outcome, summary and transcript, build voice agents, quote the price per minute, and run outbound calling campaigns.

> This repository holds documentation and client configuration only. The server is hosted by Telenow at `https://api.telenow.ai/mcp`; there is nothing to install or run.

| | |
|---|---|
| **Server URL** | `https://api.telenow.ai/mcp` |
| **Transport** | Streamable HTTP (stateless, JSON responses) |
| **Auth** | OAuth 2.1 sign-in (PKCE, dynamic client registration) or an organization API key `Authorization: Bearer vai_live_…` |
| **Protocol versions** | `2026-07-28`, `2025-11-25`, `2025-06-18`, `2025-03-26` |
| **Pricing** | Pay-as-you-go from your Telenow wallet, about $0.05/min for a typical setup |

## What you can ask

- "Call +1 415 555 0100 and book a table for 4 at 8pm Friday under my name. Tell me what they said."
- "What calls came in on my sales line yesterday? Summarize anything that needs a follow-up."
- "Build a voice agent that confirms dental appointments in Spanish, and tell me its cost per minute."
- "Draft a campaign calling these 200 leads between 10am and 6pm their time, two retries on no answer. Don't start it yet."
- "How did yesterday's renewal campaign go? Who asked to be called back?"

## Install

You need a Telenow account with at least one phone number. When you connect, Telenow asks you to sign in and choose the organization, numbers and permissions the assistant may use.

### Claude (claude.ai, Desktop, mobile)
Settings → Connectors → **Add custom connector** → URL `https://api.telenow.ai/mcp` → Connect and sign in.

### Claude Code
```bash
# OAuth sign-in
claude mcp add --transport http telenow https://api.telenow.ai/mcp

# or with an API key
claude mcp add --transport http telenow https://api.telenow.ai/mcp \
  --header "Authorization: Bearer vai_live_…"
```

### ChatGPT
Settings → Apps & Connectors → Advanced → enable **Developer mode** → Create connector → URL `https://api.telenow.ai/mcp`, auth OAuth. On Business/Enterprise workspaces an admin must allow custom connectors first.

### Cursor
[![Add to Cursor](https://cursor.com/deeplink/mcp-install-dark.svg)](cursor://anysphere.cursor-deeplink/mcp/install?name=telenow&config=eyJ1cmwiOiJodHRwczovL2FwaS50ZWxlbm93LmFpL21jcCJ9)

Or add to `~/.cursor/mcp.json`:
```json
{
  "mcpServers": {
    "telenow": { "url": "https://api.telenow.ai/mcp" }
  }
}
```

### VS Code (GitHub Copilot)
```json
// .vscode/mcp.json
{
  "servers": {
    "telenow": { "type": "http", "url": "https://api.telenow.ai/mcp" }
  }
}
```

### Windsurf / Cline / other clients with OAuth
Add a remote (Streamable HTTP) server with URL `https://api.telenow.ai/mcp`. The client will open the Telenow sign-in page.

### Clients without OAuth
Send an organization API key as a header:
```json
{
  "mcpServers": {
    "telenow": {
      "url": "https://api.telenow.ai/mcp",
      "headers": { "Authorization": "Bearer vai_live_…" }
    }
  }
}
```
A read-only key gives read-only tools; a write-capable key has full permissions in its organization.

### Gemini CLI
```bash
gemini extensions install https://github.com/telenow/telenow-mcp
```

### Grok
grok.com/connectors → New Connector → Custom → URL `https://api.telenow.ai/mcp`.

## Tools (17)

| Area | Tool | What it does | Kind |
|---|---|---|---|
| Calls | `list_numbers` | Numbers the assistant may call from; first is default caller ID | read-only |
| | `place_call` | Places one real call and returns a `call_id` at once | reaches people |
| | `get_call` | One call: status, outcome, summary, transcript | read-only |
| | `list_calls` | Recent inbound/outbound calls, newest first | read-only |
| Agents | `list_agents` | The organization's voice agents | read-only |
| | `get_agent` | One agent's configuration, keys masked | read-only |
| | `create_agent` | Single-context agent from a name and system prompt | creates |
| | `create_flow_agent` | Multi-step agent defined as a graph | creates |
| | `update_agent` | Changes an agent this assistant created | changes |
| | `list_voices` | Voices with audio samples | read-only |
| | `estimate_cost` | Cost per minute (USD) for an agent or planned setup | read-only |
| Campaigns | `list_campaigns` | Campaigns on the assistant's numbers | read-only |
| | `get_campaign` | One campaign and per-person results | read-only |
| | `create_campaign` | Draft campaign; nothing is dialed | creates |
| | `add_campaign_targets` | Adds up to 1,000 people | reaches people |
| | `start_campaign` | Starts or resumes a campaign | reaches people |
| | `pause_campaign` | Pauses; calls in progress finish | changes |

Full parameters: https://telenow.ai/docs/mcp-tools

## Safety and permissions

- The assistant must ask before anything reaches real people.
- It can only call from the numbers you chose when connecting.
- It never sees or sets your API keys.
- It can only change agents and campaigns it created.
- Agent tools, webhooks and transfers stay off unless you tick **Give agents tools** (`agents:tools` scope).
- OAuth scopes: `calls:read`, `calls:write`, `agents:write`, `agents:tools`, `campaigns:write`, `offline_access`.
- Rate limit: 3,000 requests per 15 minutes per connection or API key.

## Pricing

Calls are billed per minute from your organization's wallet, within your spend limits. A typical setup (Cartesia voice, GPT-4o-mini, Deepgram Nova-3, Plivo) is about **$0.049/min**; a 2-minute call to 100 people is about $10. Use the `estimate_cost` tool for an exact quote. Parts running on your own vendor keys or carrier are not billed by Telenow. See https://telenow.ai/docs/mcp-costs.

## Support

- Docs: https://telenow.ai/docs
- Email: support@telenow.ai
- Privacy: https://telenow.ai/privacy · Terms: https://telenow.ai/terms

## License

The files in this repository are MIT licensed. The Telenow service is proprietary and governed by Telenow's Terms of Service.
