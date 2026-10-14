---
name: get-started
description: Use when the user first connects Mothership, asks what the Mothership plugin can do, or asks which Mothership account is connected. Confirms the connected account and introduces what the plugin offers.
---

# Get started with Mothership

Mothership is a freight shipping platform. Businesses use it to quote, book, and track LTL, truckload, and local shipments. This plugin connects to the Mothership MCP server with the user's own Mothership login.

## Confirm the connection

1. Call the `account-get` tool. It needs no arguments.
2. Tell the user which Mothership user and organization are connected, using the one-line summary the tool returns.
3. Keep the reply to the user and organization.

If `account-get` returns an authorization error, ask the user to reconnect Mothership.

## Introduce what's available

Describe the tools that are available in this conversation. Which ones appear depends on the account's permissions.

- `quote-create` gets freight rates for a shipment and creates a quote in the user's Mothership dashboard. Hosts that support it show the rates as a quote card. The quote-create skill explains what to gather, how to read the rates, and how to get a quote ready to book.
- `shipment-create` books a quoted rate once the user has reviewed the total and said to book. The quote-create skill covers it.
- `shipment-get` looks up a shipment and shows a shipment card in hosts that support it. The shipment-get skill explains how to read the results.
- `shipment-cancel` checks what cancelling a shipment costs and refunds, then cancels it once the user says to. The shipment-cancel skill walks through it.
- `shipment-rebill-list` and `shipment-rebill-get` explain charges a carrier billed after a shipment, and where a dispute stands. The shipment-rebill skill covers them.
- `location-search` checks whether Mothership serves an address, and whether it's residential or limited access. The location-check skill covers it.
- `freight-class-calculate` estimates a freight class from weight and dimensions. The freight-class skill covers it.

## Booking, changes, and account help

Quotes are booked here with `shipment-create`, as the quote-create skill explains. Each quote's `dashboardUrl` also opens it in the dashboard. Edits, reschedules, rebill disputes, and payment happen in the Mothership dashboard at https://dashboard.mothership.com. For help with an account or a shipment, the Mothership help center is at https://help.mothership.com.

Base shipment details, prices, transit times, and carrier names on what the tools return. When a detail isn't in the results, say it isn't available yet.
