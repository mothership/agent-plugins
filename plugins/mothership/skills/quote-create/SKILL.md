---
name: quote-create
description: Use when the user wants a freight quote, rates, or prices for a shipment, asks what it would cost or how long it would take to ship something, or wants to book. Finds the addresses with location-search, gets rates with quote-create, explains them accurately, and gathers what booking still needs.
---

# Get a Mothership quote

## Gather the shipment

`quote-create` needs four things to price a shipment. Ask for whichever the user hasn't given, and never fill one in yourself.

- The pickup and delivery addresses. A full street address is best, because it shows whether a stop is residential or limited access. A ZIP code or a city and state also works.
- The pickup date, and the time the freight is ready that day. The ready time has to fall within the pickup location's hours.
- Each cargo line: the handling unit, such as pallet or crate, how many, and the weight and dimensions of one unit in whole pounds and inches. A standard pallet is 48 x 40 inches, but its height and weight have to come from the user. Pass a freight class only when the user knows it. Otherwise carriers price on density, and `freight-class-calculate` can estimate the class if the user asks.
- Hazardous materials, alcohol, or tobacco on any line. Mothership ships all three. Hazmat lines need the UN number, hazard class, packing group, proper shipping name, package count and type, and a 24-hour emergency contact.

Pass along anything else the user mentions, such as company names, contacts, phone numbers, facility hours, cargo descriptions, reference numbers, a declared value for Freight Protect coverage, or a promotion code. Booking needs most of these, and carriers price on facility hours.

Leave out the payment method and account credits. The quote uses the organization's default checkout method and applies credits on its own.

## Find the addresses

1. Call `location-search` once with every pickup and delivery address.
2. When a result's `candidateCount` is above 1, confirm the address with the user before quoting.
3. When an address has no `quoteLocation`, Mothership can't quote it. That happens outside the lower 48 states, or when the address didn't resolve to a full place, so ask for a more complete one.
4. Look at each location's `quoteLocation.recommendedServices`, such as liftgate and residential for a home, and the residential and limited-access flags. Ask the user which apply, so the carrier doesn't add charges later. A stop without a loading dock needs liftgate.

## Get the rates

Call `quote-create` with each `quoteLocation` exactly as `location-search` returned it, and everything the user gave you.

Every call creates a real quote in the user's Mothership dashboard. Gather changes and quote again once, rather than once per change.

If the call fails, its message says why, such as a holiday pickup date or a lane carriers don't serve. When it names rejected fields, ask the user about those.

## Explain the rates

Hosts that support it show the rates as a quote card. When the card is showing, answer the question in a sentence or two and leave the full list to the card.

- `rates` is every rate, cheapest first. Lead with the cheapest, and mention a faster or guaranteed option when there is one.
- `price` is the rate as the rate card shows it, before Freight Protect, checkout fees, credits, and promotions. When the user asks what they'll pay, use `breakdown.total` and walk through `breakdown` if they want the detail. When `breakdown.total` is null, say the checkout total isn't available. Write amounts like $1,137.80.
- `pickupDate`, `deliveryDate`, and `transit` are estimates unless `guaranteed` is true. `transit` uses the dashboard's wording, such as "4 business days (Guaranteed by 12PM)".
- `mode` is LTL for freight that shares a truck, or FTL for a truck dedicated to the shipment.
- `unavailableCarriers` lists carriers that returned no rate, with their reasons. Bring it up when the user asks about a carrier, or when there are no rates at all.
- When `promotionError` is set, the promotion code didn't apply. Tell the user why.

## Book the quote

Booking, payment, and any later changes happen in the Mothership dashboard. Share `dashboardUrl` so the user can open the quote and book it.

When `purchase.ready` is false, `purchase.missing` lists what booking still needs. Each item's `field` names the `quote-create` input to fill, such as `pickup.phone` or `cargo[0].description`, and its `message` says what's wrong. Ask the user for those details, then call `quote-create` once more with everything. That makes a new quote with the details booking needs, so share its `dashboardUrl`.
