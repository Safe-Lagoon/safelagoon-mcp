# [SafeLagoon](https://safelagoon.com)

This is the public documentation for SafeLagoon's remote [Model Context Protocol](https://modelcontextprotocol.io) (MCP) server. It contains no server source code. The server itself is hosted by SafeLagoon.

## What it does

SafeLagoon is a parental-control service for families. This MCP server connects your SafeLagoon parent account so you can check children's screen time and manage device rules in plain language from Claude, ChatGPT, Cursor, VS Code, Grok, and other MCP-compatible clients.

After you sign in, you can:

- See which children are on the account and how much screen time each has left today
- View daily totals, most-used apps, activity summaries, and the weekly screen-time report
- Review current rules, pending extra-time requests, and installed apps
- Approve or deny extra-time requests
- Block or unblock apps, set or remove daily limits, and create, update, or delete blocking windows
- Lock or unlock a child's device
- View a child's last known location and location history, and request a fresh live location
- Review AI Shield alerts
- View visited websites, YouTube watch history, and chats when those features are available on the child's device
- Add, update, or delete a child profile

Write actions are separate from read actions. Clients should ask you to confirm before changing a child's rules or profile. You can review or change the same settings in the SafeLagoon app at any time.

The server only shows data for children on your own SafeLagoon account.

## Remote server

| | |
| --- | --- |
| URL | `https://mcp.safelagoon.com/mcp` |
| Transport | Streamable HTTP |
| Authentication | OAuth sign-in with your SafeLagoon parent account |

Requirements:

- A SafeLagoon parent account with **Premium** or a **Premium trial**, and at least one child device paired in SafeLagoon
- Some features (for example chats, YouTube history, or location) depend on the child's device platform and settings
- Use text chat. Voice or live modes may not load connector tools

When you add the server, your client opens a SafeLagoon sign-in and approval page. The client never receives your SafeLagoon password.

Step-by-step screenshots and the approval-screen details are in the [SafeLagoon connect guide](https://safelagoon.com/en/connect-ai/).

## Connect

Use the same URL in every client: `https://mcp.safelagoon.com/mcp`.

### Claude

Works on [claude.ai](https://claude.com) and Claude Desktop. Claude connects from Anthropic's servers, not from your device. Official guide: [Custom connectors using remote MCP](https://support.claude.com/en/articles/11175166-get-started-with-custom-connectors-using-remote-mcp).

1. Open Claude and go to **Customize → Connectors**.
2. Click **+ Add**, then **Add custom connector**.
3. Enter a name (for example, SafeLagoon) and paste `https://mcp.safelagoon.com/mcp` as the server URL. Click **Continue**.
4. Review the authentication settings Claude detected. Under Authentication, choose **Sign in now**. If you see an OAuth client setting, keep **Use Claude's published identity (Recommended)**. Click **Continue**, then **Add**.
5. Sign in with your SafeLagoon parent account and choose **Allow access** on the approval screen.
6. In a chat, enable the SafeLagoon connector for that conversation and try: "Which children are on my account and how much screen time does each have left today?"

On Team and Enterprise plans, an Owner first adds the connector under **Organization settings → Connectors**. Members then open **Customize → Connectors** and click **Connect**. Claude's entry-level plan allows one custom connector per account.

### ChatGPT

Add a custom MCP server in ChatGPT on the web ([chatgpt.com](https://chatgpt.com)). Official guide: [Add a custom MCP server](https://developers.openai.com/api/docs/guides/custom-mcp-server). Availability depends on your ChatGPT plan and workspace settings.

1. In ChatGPT on the web, open **Plugins**.
2. Select the plus button, then **Add custom MCP server**.
3. Enter a name (for example, SafeLagoon). Under Connection, enter `https://mcp.safelagoon.com/mcp` as the server URL.
4. Set authentication to **OAuth**.
5. Read the risk warning, confirm it, and select **Create as a plugin**.
6. Install the plugin, select **Connect**, sign in to SafeLagoon, and choose **Allow access**.
7. Start a new text chat, type `@`, select SafeLagoon, and ask: "How much screen time does each of my kids have left today?"

If tools do not appear after you connect, open the plugin's details page and refresh it.

### Cursor

Official guide: [Model Context Protocol (MCP)](https://cursor.com/docs/mcp). Cursor supports Streamable HTTP remote servers and OAuth.

**Option A — UI.** Open **Customize → MCPs** (or Cursor Settings → MCP) and add a new server with the URL `https://mcp.safelagoon.com/mcp`. Complete the SafeLagoon OAuth sign-in when prompted.

**Option B — `mcp.json`.** Add this to `~/.cursor/mcp.json` (all projects) or `.cursor/mcp.json` (this project):

```json
{
  "mcpServers": {
    "safelagoon": {
      "url": "https://mcp.safelagoon.com/mcp"
    }
  }
}
```

Save the file. Cursor will start the OAuth sign-in for the remote server. Do not put access tokens in this file.

### VS Code

Official guide: [Add and use MCP servers in VS Code](https://code.visualstudio.com/docs/agent-customization/mcp-servers/) and the [MCP configuration reference](https://code.visualstudio.com/docs/agents/reference/mcp-configuration). Prefer the HTTP / Streamable HTTP transport.

**Option A — UI.** With Copilot selected, run **Chat: Open Customizations**, open **MCP Servers**, and select **Add Server**. Choose **HTTP (HTTP or Server-Sent Events)**, enter `https://mcp.safelagoon.com/mcp`, give it an ID such as `safelagoon`, and pick a configuration destination. Complete the SafeLagoon sign-in when prompted. You can also start the same flow with **MCP: Add Server** from the Command Palette.

**Option B — portable config.** Add this to `~/.copilot/mcp-config.json` (user / Copilot Global) or to a workspace-root `.mcp.json`:

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

If you are still using the older editor-managed format in `.vscode/mcp.json`, the equivalent entry uses a top-level `servers` object with `"type": "http"` and the same URL. New setups should use the portable `mcpServers` format above.

### Grok

Official guide: [Grok connectors](https://docs.x.ai/grok/connectors). Custom MCP connectors are added on [grok.com/connectors](https://grok.com/connectors).

1. Go to [grok.com/connectors](https://grok.com/connectors).
2. Click **New Connector**, then select **Custom**.
3. Enter `https://mcp.safelagoon.com/mcp` as the MCP server URL and complete authentication.
4. Sign in to SafeLagoon and choose **Allow access**.
5. Start a chat and ask: "Which children are on my account and how much screen time does each have left today?"

For Grok Business and Enterprise, a team admin first provisions the connector for the organization; members then connect their own SafeLagoon account on grok.com/connectors.

## Tools

The production server (SafeLagoon AI **0.1.36**) exposes these 31 tools. Titles come from the `ToolDef.title` field in the SafeLagoon AI source at tag `0.1.36`.

1. Account Overview (`safelagoon_whoami`)
2. List Child Profiles (`safelagoon_list_profiles`)
3. Get Child Profile (`safelagoon_get_profile`)
4. Add Child Profile (`safelagoon_create_profile`)
5. Update Child Profile (`safelagoon_update_profile`)
6. Delete Child Profile (`safelagoon_delete_profile`)
7. List Device Apps (`safelagoon_list_apps`)
8. Block App (`safelagoon_block_app`)
9. Unblock App (`safelagoon_unblock_app`)
10. Set Device Lock / Play Mode (`safelagoon_set_device_mode`)
11. List Extra-Time Requests (`safelagoon_list_time_requests`)
12. Approve or Deny Extra Time (`safelagoon_decide_time_request`)
13. List Screen Time Rules (`safelagoon_list_screentime_rules`)
14. Create Blocking Window (`safelagoon_create_schedule`)
15. Update Blocking Window (`safelagoon_update_schedule`)
16. Delete Blocking Window (`safelagoon_delete_schedule`)
17. Set Daily Screen Time Limit (`safelagoon_set_daily_limit`)
18. Remove Daily Screen Time Limit (`safelagoon_delete_daily_limit`)
19. Get Current Location (`safelagoon_get_location`)
20. Get Location History (`safelagoon_get_location_history`)
21. Request Live Location (`safelagoon_request_location`)
22. AI Shield Alerts Summary (`safelagoon_ai_shield_summary`)
23. AI Shield Alert Detail (`safelagoon_ai_shield_alert`)
24. Get Daily Screen Time (`safelagoon_get_screentime_summary`)
25. Get Most Used Apps (`safelagoon_get_top_apps`)
26. Get Activity Summary (`safelagoon_get_activity_summary`)
27. Get Screen Time Remaining (`safelagoon_get_playtime_status`)
28. Screen Time Report Card (`safelagoon_get_screentime_report`)
29. Get Visited Websites (`safelagoon_get_visited_sites`)
30. Get YouTube History (`safelagoon_get_youtube_history`)
31. Get Chats and Messages (`safelagoon_get_chats`)

## Privacy

See the [SafeLagoon Privacy Policy](https://safelagoon.com/privacy-policy/).

The MCP server only acts on children on the signed-in parent account, within the permissions approved at connect time. Disconnect the server in your client, then remove the connector's device entry under **SafeLagoon → Devices**, to revoke access.

## Support

Email [support@safelagoon.com](mailto:support@safelagoon.com). Help center: [help.safelagoon.com](https://help.safelagoon.com).

## License

The documentation and logo files in this repository are licensed under [CC BY 4.0](LICENSE). The SafeLagoon name and mark are trademarks of Safe Lagoon, S.L.
