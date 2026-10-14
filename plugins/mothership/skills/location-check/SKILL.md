---
name: location-check
description: Use when the user asks whether Mothership serves, picks up from, or delivers to an address, city or ZIP code, whether an address is residential or limited access, or whether a pickup and delivery qualify for same-day or LTL service. Checks the locations with location-search and explains what it confirms and what it can't.
---

# Check a location

## Look the locations up

1. Gather every location the answer needs. For a route, that's the pickup and the delivery. When the user says "my pickup address" without giving it, use the address from earlier in the conversation or ask for it.
2. Call `location-search` once, with all of them in `locations`.

## Read each location

Each entry in `locations` has a `status`.

- `resolved` matched a place. `formattedAddress`, `city`, `state` and `postalCode` say which one.
- `not_found` matched nothing. Ask the user to check the address or give more of it.
- `unavailable` means the lookup failed for that location. Say so and try again later. Don't guess.

When `candidateCount` is above 1, the match was ambiguous. Show the user `formattedAddress` and have them confirm it before you answer anything about that location.

## Residential and limited access

`accessClassification` needs a full street address. A ZIP code or city match comes back `not_applicable`, which isn't a classification. When the user asks whether a place is residential or limited access and gave only a ZIP or city, ask for the street address.

- `complete` gives both `isResidential` and `isLimitedAccess`.
- `partial` gives one of them, and the other is null. Say the other isn't known, and don't guess it.
- `unknown` means Mothership couldn't classify the address.

`quoteLocation.recommendedServices` lists services carriers usually need at the place, such as residential and liftgate for a home, or limited-access and liftgate for a site like a school. When the user plans to ship there, mention them so they can add them to the quote.

## Same-day and LTL service

`serviceability` gives one verdict for all the locations together. Trust it, and don't work out distances or zones yourself.

`serviceability.ltl.status` is `eligible` when every location is in the lower 48 states, `not_eligible` when one is outside them, and `unknown` when Mothership couldn't tell for a location, such as one that didn't resolve.

`serviceability.sameDay.status` is one of these:

- `eligible` means every location is in an active same-day zone and they're close enough together for same-day service.
- `needs_additional_location` comes only when you passed a single location and it's in the active zone named in `activeZone.name`. Same-day needs both ends of the route, so ask for the other one and check them together.
- `not_eligible` comes with a `reason`. `one_or_more_locations_outside_active_zone` means a location is outside every active zone. `locations_exceed_same_day_range` means the locations are too far apart.
- `unknown` means Mothership couldn't check every location, because one didn't resolve or its zone couldn't be looked up. There's no verdict, so don't guess one.

Each resolved location's `activeSameDayZone` names its same-day zone. Null means it's outside every zone, unless `serviceability.sameDay.status` is `unknown`, when it can also mean the zone couldn't be looked up.

## What it doesn't answer

`location-search` doesn't quote rates, transit times, pickup availability or delivery dates. A same-day `eligible` verdict means the locations are inside the same-day area. It doesn't promise a same-day rate, a pickup time or a delivery date. Never promise any of those from these results.

For prices and dates, follow the quote-create skill, and pass each location's `quoteLocation` to `quote-create` unchanged. When `quoteLocation` is null, Mothership can't quote that place, because it's outside the lower 48 states or didn't resolve to a full place.
