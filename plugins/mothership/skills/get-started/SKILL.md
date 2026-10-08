---
name: get-started
description: Use when the user first connects Mothership, asks what the Mothership plugin can do, or asks which Mothership account is connected. Confirms the connected account and introduces what the plugin offers.
---

# Get started with Mothership

Mothership is a freight shipping platform. Customers use it to quote, book, and track LTL and truckload shipments. This plugin connects to the Mothership MCP server with the user's own Mothership login.

## Confirm the connection

1. Call the `account-get` tool. It needs no arguments.
2. Tell the user which Mothership user and organization are connected, using the one-line summary the tool returns.
3. Keep the reply to the user and organization.

If `account-get` returns an authorization error, ask the user to reconnect Mothership.

## Introduce what's available

Describe the tools that are available in this conversation. Which ones appear depends on the account's permissions.

- `quote-create` gets freight rates for a shipment and creates a quote in the user's Mothership dashboard. Hosts that support it show the rates as a quote card. The quote-create skill explains what to gather, how to read the rates, and how to get a quote ready to book.
- `shipment-get` looks up a shipment and shows a shipment card in hosts that support it. The shipment-get skill explains how to read the results.

## Booking, changes, and account help

Quotes are booked in the Mothership dashboard. Each quote's `dashboardUrl` opens it there. Changes to a shipment and payment also happen in the dashboard at https://dashboard.mothership.com. For help with an account or a shipment, the Mothership help center is at https://help.mothership.com.

Base shipment details, prices, transit times, and carrier names on what the tools return. When a detail isn't in the results, say it isn't available yet.
