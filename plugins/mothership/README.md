# Mothership

Mothership is a freight shipping platform for quoting, booking, and tracking LTL and truckload shipments. This plugin connects your AI assistant, such as Claude, ChatGPT, Codex, or Cursor, to the Mothership MCP server at https://mcp.mothership.com, so you can work with your organization's Mothership account in plain language.

## What it does

- Looks up a shipment by its reference number, PRO, BOL, pickup number, or your own reference: status, pickup and delivery windows, carrier and tracking numbers, cargo, location when the carrier reports it, documents, and the newest tracking updates.
- Confirms which Mothership user and organization are connected.
- Offers the other tools the Mothership MCP server makes available to your account.

## How to use it

1. Install the plugin and connect Mothership when your assistant asks.
2. Sign in with your Mothership account. The assistant can reach only what your account can see.
3. Ask something like "Where is shipment MS123456?" or "Which Mothership account am I connected as?"

## What data it sends

When your assistant calls a Mothership tool, it sends the tool's arguments to https://mcp.mothership.com with the access token from your Mothership sign-in. The arguments are what you asked about, such as a shipment reference number, an address or ZIP code, or cargo details. Mothership sends back the matching data from your account, and the assistant answers from it. Read the Mothership privacy policy at https://help.mothership.com/privacy-policy.

## Support

Visit https://help.mothership.com for help with your account or a shipment, or https://dashboard.mothership.com to manage shipments.
