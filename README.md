# Mothership MCP server

[Mothership](https://www.mothership.com) is a freight marketplace for LTL, truckload and local freight. Its MCP server connects your AI assistant to your Mothership account, so you can get rates, track and cancel shipments, find freight classes and check your bills by asking in plain language.

The server is at `https://mcp.mothership.com` and speaks Streamable HTTP. You sign in with your Mothership account over OAuth 2.1, and the assistant sees only what your account can see. The [official MCP Registry](https://registry.modelcontextprotocol.io) lists it as `com.mothership/mothership`.

This repo also holds the Mothership plugin for Claude, ChatGPT, Codex and Cursor, which adds Mothership's skills to the server. See [Plugins](#plugins).

## What it can do

With the server connected, your assistant can:

- Quote LTL, truckload and local freight. It shows each carrier's price and delivery date, with a link to book the rate you pick in the Mothership dashboard. Every quote it gets is saved to your account.
- Track a shipment by its Mothership reference, PRO, BOL or pickup number, or your own reference: status, pickup and delivery windows, carrier, cargo, documents and the newest tracking updates.
- Find, list and count your shipments by date, status or carrier.
- Cancel a shipment. It shows the cancellation fee and refund first, and cancels only after you confirm.
- Find the NMFC item for what you ship, read an item's freight classes, and estimate a class from weight and dimensions.
- Check whether Mothership serves an address, and whether it's residential or limited access.
- Show your invoices, rebills, payment methods and billing details.
- Start freight claims, follow them, and accept or rebut a claim decision.
- Keep your address book, invite and manage teammates, and set up webhooks.
- Search Mothership's help articles and your support conversations.

In clients that support [MCP Apps](https://github.com/modelcontextprotocol/ext-apps), such as Claude and ChatGPT, quotes and shipments can show as cards.

## Connect

Any MCP client that supports remote servers with OAuth can connect to `https://mcp.mothership.com`. The first time it connects, it opens a Mothership sign-in page, and you approve the client's access. You need a Mothership account. If you don't have one, sign up at https://dashboard.mothership.com/signup.

Clients identify themselves with a Client ID Metadata Document, as Claude, ChatGPT and VS Code do. The server doesn't support dynamic client registration. Cursor signs in with a client ID that Mothership set up for it.

### Claude

In claude.ai or the Claude desktop app, add Mothership as a custom connector. On Free, Pro and Max plans:

1. Go to **Customize > Connectors**, select **+ Add**, then **Add custom connector**.
2. Enter `Mothership` as the name and `https://mcp.mothership.com` as the URL, and select **Continue**.
3. Keep the authentication settings Claude detects. Under **OAuth client**, leave **Use Claude's published identity** selected, since Mothership doesn't support **Register automatically**.
4. Select **Add**, then sign in with your Mothership account.

On Team and Enterprise plans, an Owner adds the connector from **Organization settings > Connectors** with **Add > Custom > Web**. Each member then connects it from **Customize > Connectors** and signs in with their own Mothership account. See [Anthropic's guide to custom connectors](https://support.claude.com/en/articles/11175166-getting-started-with-custom-connectors-using-remote-mcp).

### ChatGPT

1. Go to [Plugins](https://chatgpt.com/plugins), select **+**, then **Add custom MCP server**.
2. Enter a name and description. Under **Connection**, choose **Public endpoint** and enter `https://mcp.mothership.com`.
3. Choose OAuth for authentication, accept the risk warning, and select **Create as a plugin**.
4. Sign in with your Mothership account.

Your workspace's policies decide who can add a custom MCP server. ChatGPT keeps the tool list it found when you added the server, so when Mothership adds tools, open the server in Plugins and select **Refresh**. See [OpenAI's guide](https://developers.openai.com/apps-sdk/deploy/connect-chatgpt).

### VS Code

Open the Command Palette, run **MCP: Add Server**, choose **HTTP (HTTP or Server-Sent Events)**, enter `https://mcp.mothership.com`, and give the server the ID `mothership`. For a personal setup, choose **Global**, then **Copilot Global**. VS Code asks you to sign in with your Mothership account the first time the server starts. See [VS Code's MCP guide](https://code.visualstudio.com/docs/copilot/customization/mcp-servers).

### Cursor

Add Mothership to `~/.cursor/mcp.json` for every project, or to `.cursor/mcp.json` in one project:

```json
{
  "mcpServers": {
    "Mothership": {
      "url": "https://mcp.mothership.com",
      "auth": { "CLIENT_ID": "cursor" }
    }
  }
}
```

`CLIENT_ID` names the OAuth client Mothership set up for Cursor, because the server has no dynamic client registration. Sign in with your Mothership account when Cursor asks. See [Cursor's MCP guide](https://cursor.com/docs/context/mcp).

### Other clients

Other clients can connect with the same URL if they support OAuth with a Client ID Metadata Document. A client that only supports dynamic client registration can't sign in.

## Plugins

The [`mothership`](plugins/mothership) plugin connects to the same server and adds Mothership's skills. Skills are instructions that teach the assistant how to get started, quote, track and cancel shipments, explain rebills, check locations and find freight classes. The assistant loads them like its own skills. Without the plugin, the server serves the same skills through its `skill-get` tool.

Claude Code:

```bash
claude plugin marketplace add mothership/agent-plugins
claude plugin install mothership@mothership
```

Claude on the web and desktop: go to **Customize > Plugins**, select **Add > Add marketplace**, and enter `mothership/agent-plugins`. Add the Mothership plugin, then add and connect Mothership from its **Connectors** tab. On Team and Enterprise plans, an Owner may need to allow the marketplace or add the connector first.

Codex:

```bash
codex plugin marketplace add mothership/agent-plugins
codex plugin add mothership@mothership
```

ChatGPT workspace admins: go to **Admin > Plugins**, select **Add > Import marketplace**, enter `https://github.com/mothership/agent-plugins` as the **Source**, and leave **Path** empty. ChatGPT syncs the marketplace daily. It marks imported plugins that connect to an MCP server as Desktop only, so the plugin works in the ChatGPT desktop app. On chatgpt.com, add the server as a [custom MCP server](#chatgpt) instead.

Cursor team admins, on Teams and Enterprise plans: in the Cursor dashboard, open **Plugins & MCPs**, select **Add Marketplace** under **Team Marketplaces**, choose **Import from Repo**, and enter `https://github.com/mothership/agent-plugins`. Members then install the plugin from **Customize** in Cursor.

After you install the plugin, sign in with your Mothership account when the assistant asks to connect Mothership.

### What's in this repo

Each host reads its own marketplace file, and all three list the same plugin folder:

| Marketplace file | Read by |
| --- | --- |
| `.claude-plugin/marketplace.json` | Claude |
| `.agents/plugins/marketplace.json` | ChatGPT and Codex |
| `.cursor-plugin/marketplace.json` | Cursor |

The plugin folder carries the portable [Agent Plugins](https://agent-plugins.org) manifest that ChatGPT and Codex read, the `.claude-plugin/plugin.json` manifest that Claude reads, and the `.cursor-plugin/plugin.json` manifest that Cursor reads. ChatGPT, Codex and Claude share one `mcp.json`, and the Cursor manifest names the same server. `server.json` is the server's MCP Registry entry.

## Support

Visit https://help.mothership.com for help with your account or a shipment.

## License

The files in this repository are under the [MIT License](LICENSE). The Mothership name and logo are trademarks of Mothership Technologies, Inc., and the license doesn't cover them. Using them needs Mothership's prior written permission. See [TRADEMARKS.md](TRADEMARKS.md). The Mothership terms of service at https://help.mothership.com/terms-of-service-general-terms govern your use of the Mothership service and its MCP server.
