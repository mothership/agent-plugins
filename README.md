# Mothership plugins

Plugins that connect Claude, ChatGPT, Codex, and Cursor to your [Mothership](https://www.mothership.com) freight account. Each host reads its own marketplace file, and all three list the same plugin folders:

| Marketplace file | Read by |
| --- | --- |
| `.claude-plugin/marketplace.json` | Claude |
| `.agents/plugins/marketplace.json` | ChatGPT and Codex |
| `.cursor-plugin/marketplace.json` | Cursor |

Each plugin folder carries the portable [Agent Plugins](https://agent-plugins.org) manifest that ChatGPT and Codex read, the `.claude-plugin/plugin.json` manifest that Claude and Cursor read, and one `mcp.json` that every host uses.

| Plugin | What it does |
| --- | --- |
| [`mothership`](plugins/mothership) | Look up shipments and work with your Mothership account through the Mothership MCP server |

## Install

Claude Code:

```bash
claude plugin marketplace add mothership/agent-plugins
claude plugin install mothership@mothership
```

Codex:

```bash
codex plugin marketplace add mothership/agent-plugins
codex plugin add mothership@mothership
```

ChatGPT workspace admins: in the Admin Console, open **Plugins**, select **Add > Import marketplace**, enter `https://github.com/mothership/agent-plugins` as the source, and leave **Path** empty. ChatGPT syncs the marketplace daily.

Cursor team admins (Teams and Enterprise plans): in the Cursor dashboard, open **Plugins & MCPs**, select **Add Marketplace** under **Team Marketplaces**, choose **Import from Repo**, and enter `https://github.com/mothership/agent-plugins`. Members then install the plugin from **Customize** in Cursor.

After installing, sign in with your Mothership account when the assistant asks to connect Mothership.

## Support

Visit https://help.mothership.com for help with your account or a shipment.
