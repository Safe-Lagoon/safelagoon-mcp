# Install / connect the SafeLagoon MCP server (for LLM agents)

This repository is **documentation only**. It does not contain MCP server source code, a package, or a command you can run locally. Do **not** clone this repo in order to start a server, do **not** invent a stdio command, and do **not** ask the user for a SafeLagoon password, token, or API key to paste into config.

SafeLagoon hosts a remote MCP server. Connect the user's MCP client to it over Streamable HTTP and complete SafeLagoon's OAuth sign-in in the browser.

## Facts to use (do not invent alternatives)

- Server URL: `https://mcp.safelagoon.com/mcp`
- Transport: Streamable HTTP (not stdio, not a local subprocess)
- Authentication: OAuth. The client must open SafeLagoon's sign-in and approval pages. Do not put bearer tokens, client secrets, or passwords in config files.
- Access: a SafeLagoon **parent** account with **Premium** or a **Premium trial**, and at least one paired child device
- Human guide (canonical): https://safelagoon.com/en/connect-ai/
- Privacy policy: https://safelagoon.com/privacy-policy/
- Support: support@safelagoon.com
- Registry name (when published): `com.safelagoon/mcp`

Call this product the SafeLagoon MCP server or SafeLagoon connector. Do not call it "the assistant" or "AI Assistant".

## Connect in the current client

If you are running inside a client that can add MCP servers, add a remote Streamable HTTP server with URL `https://mcp.safelagoon.com/mcp` and let the client start OAuth. Then ask the user to finish sign-in in the browser and to choose Allow access.

If the client needs a config file, use the matching snippet below. After writing it, tell the user to reload or restart the client and complete the SafeLagoon browser sign-in. Never write secrets into the file.

### Cursor (`~/.cursor/mcp.json` or project `.cursor/mcp.json`)

```json
{
  "mcpServers": {
    "safelagoon": {
      "url": "https://mcp.safelagoon.com/mcp"
    }
  }
}
```

### VS Code / Copilot portable config (`~/.copilot/mcp-config.json` or workspace `.mcp.json`)

```json
{
  "mcpServers": {
    "safelagoon": {
      "type": "http",
      "url": "https://mcp.safelagoon.com/mcp"
    }
  }
}
```

### Cline / similar clients that use `type: streamableHttp`

```json
{
  "mcpServers": {
    "safelagoon": {
      "type": "streamableHttp",
      "url": "https://mcp.safelagoon.com/mcp",
      "disabled": false,
      "autoApprove": []
    }
  }
}
```

Leave `autoApprove` empty. Write actions change a child's device rules or profile and must stay user-confirmed.

### Claude, ChatGPT, and Grok (no local config file)

These clients are configured in their own UI, not by editing this repository:

- Claude: Customize → Connectors → Add custom connector, URL `https://mcp.safelagoon.com/mcp`, then sign in. Guide: https://support.claude.com/en/articles/11175166-get-started-with-custom-connectors-using-remote-mcp
- ChatGPT (web): Plugins → Add custom MCP server, same URL, authentication OAuth. Guide: https://developers.openai.com/api/docs/guides/custom-mcp-server
- Grok: https://grok.com/connectors → New Connector → Custom, same URL. Guide: https://docs.x.ai/grok/connectors

## Verify the connection

After OAuth succeeds, ask a low-impact read-only question such as:

> Which children are on my SafeLagoon account, and how much screen time does each have left today?

A working connection calls `safelagoon_whoami` and/or `safelagoon_list_profiles` and `safelagoon_get_playtime_status`, then answers from those results. If the server says the plan does not include the connector, the account needs Premium or a Premium trial — do not work around this. If the profile list is empty, the parent still needs to pair a child device in the SafeLagoon app.

## What not to do

- Do not run `npx`, `uvx`, Docker, or Python to "install" SafeLagoon MCP.
- Do not configure stdio, SSE-only URLs, or a different host than `https://mcp.safelagoon.com/mcp`.
- Do not request or store the user's SafeLagoon password, one-time code, or access token.
- Do not call write tools (block/unblock, limits, schedules, device lock, extra-time decisions, profile create/update/delete, live-location request) without an explicit user confirmation for that change.
- Do not ask about anyone who is not a child on the signed-in account.
