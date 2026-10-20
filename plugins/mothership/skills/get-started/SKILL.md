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

- `quote-create` gets freight rates for a shipment and creates a quote in the user's Mothership dashboard. Hosts that support it can show the rates as a quote card. The quote-create skill explains what to gather, how to read the rates, and how to get a quote ready to book.
- `shipment-get` looks up a shipment, which hosts that support it can show as a shipment card. The shipment-get skill explains how to read the results.
- `render-ui` shows a shipment, a list of shipments, a quote's rates or payment methods as a card in hosts that support it, when the user wants to see one. Results that can be shown say how.
- `shipment-cancel` checks what cancelling a shipment costs and refunds, then cancels it once the user says to. The shipment-cancel skill walks through it.
- `shipment-rebill-list` and `shipment-rebill-get` explain charges a carrier billed after a shipment, and where a dispute stands. The shipment-rebill skill covers them.
- `location-search` checks whether Mothership serves an address, and whether it's residential or limited access. The location-check skill covers it.
- `nmfc-search` finds the NMFC item for what the user ships, `nmfc-get` reads the classes an NMFC code sets, and `freight-class-calculate` estimates a freight class from weight and dimensions. The freight-class skill covers all three.

## Booking, changes, and account help

Quotes are booked in the Mothership dashboard from each quote's `dashboardUrl`, as the quote-create skill explains. Edits, reschedules, rebill disputes, and payment happen in the Mothership dashboard at https://dashboard.mothership.com. For help with an account or a shipment, the Mothership help center is at https://help.mothership.com.

Base shipment details, prices, transit times, and carrier names on what the tools return. When a detail isn't in the results, say it isn't available yet.
