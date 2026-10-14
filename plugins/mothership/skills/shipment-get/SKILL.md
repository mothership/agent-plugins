---
name: shipment-get
description: Use when the user asks about a Mothership shipment's status, where it is, when it will be picked up or delivered, whether pickup or delivery happened, its carrier, PRO or BOL number, what's on it, its tracking history, or its documents. Looks the shipment up with shipment-get and explains the result clearly and accurately.
---

# Get a Mothership shipment

## Look up the shipment

1. Start with the identifier the user gave you, exactly as they wrote it. `shipment-get` accepts a Mothership reference number such as MS123456, a PRO, pickup number, BOL, the shipper's own reference, or the shipment ID. If the user hasn't mentioned one yet, ask for it.
2. Call `shipment-get` with `identifier`.
3. If the result is "Shipment not found", ask the user to double-check the identifier and try again.
4. If the identifier matches more than one shipment, ask the user for the Mothership reference number.

Hosts that support it show the result as a shipment card. When the card is showing, answer the question and leave the full detail to the card.

## Answer what was asked

Lead with the direct answer in one to three sentences. Offer more detail when the user wants it.

- Use `status.name` for the status. When `status.attention` is set, explain its `message`, because something needs the user. When `status.phase` is `cancelled`, `status.cancellationReason` says why.
- Carrier, SCAC, PRO, BOL and pickup numbers are under `carrier`. `carrier.name` can be a label such as "Verified Carrier" rather than a company name. Service level, delivery commitment and transit time are under `service`. Coverage is under `coverage`.
- `service.type` says what kind of shipment it is. `best-value`, `fastest` and `lowest` are LTL. `same-day`, `next-day` and `limited-access-regional` are Mothership's local service, and `flatbed` and `reefer` are truckload. The LTL guidance below applies only to LTL shipments.
- `invoicedTotalUsd` is the total invoiced in US dollars, net of credits, written like $609.75. When it's null, say the invoiced total isn't available. Null can mean nothing is invoiced yet or the amount is unavailable, so don't pick one. Never present null as $0.
- `cargo` lists every line, with per-unit weight and dimensions. Multiply `weightLbsEach` by `quantity` for a line's weight.
- Share document links from `documents[].url`, which is null when there's no file yet, and the shipment's dashboard page from `trackUrl`. `pickup.signedBy` and `delivery.signedBy` say who signed.

## Times and windows

- Each time has `at`, an ISO instant for reasoning and comparisons, and `local`, the same moment in the stop's own time zone for showing the user, such as "Wed, Oct 7, 2026 2:15 PM PDT". If you convert to the user's time zone, say so.
- `pickup.window` and `delivery.window` hold the scheduled service window. `arrivedAt` and `completedAt` record what happened. Until `completedAt` is set, describe the stop by its window.
- `facilityHours` is the location's business hours, which are separate from the service window.

## Arrival times and location

- For LTL freight, the scheduled window is the carrier's arrival commitment. When the user asks for a live ETA, an exact arrival time, or where the driver is, give the scheduled window and explain that LTL carriers commit to a window.
- `lastTrackingUpdateAt` is when the carrier's tracking last refreshed, or the newest tracking update when there's no refresh. It doesn't prove anyone contacted the carrier then. It's null before the first update.
- `lastKnownLocation` is the newest reported position. In transit with no carrier report, it's the live position, and its `reportedAt` is null. `locationHistory` lists up to 5 carrier reports, newest first. When both are empty, say no location update is available yet.

## When pickup is still being confirmed

Start from `status.name` and `status.attention`. When `status.phase` is `cancelled`, `status.attention` is set, or a recent activity item has a `scheduleChange`, explain that first.

When `status.phase` is `awaiting-pickup` and `carrier.name` is null, no carrier is assigned yet and Mothership is still finding one. Say so, and don't describe the pickup as booked.

When the phase is still `awaiting-pickup` and `pickup.arrivedAt` falls inside the current `pickup.window`, the driver arrived and the carrier hasn't confirmed the pickup yet. Say so.

When `status.phase` is `awaiting-pickup`, `carrier.name` is set, the pickup window has started, and the driver hasn't arrived in that window:

- Reassure the user that the pickup is booked on the carrier's schedule.
- LTL carriers usually deliver in the morning and pick up in the late afternoon, so the driver can arrive any time until the location closes. Pickup confirmation often appears in the evening, once the carrier's local terminal finishes its stops.
- Suggest having the freight ready and easy to reach at the dock.

If the user says the driver didn't arrive or couldn't complete the pickup, share `trackUrl`. The shipment's page lets them report a missed pickup when the shipment qualifies. If it doesn't, point them to the Mothership help center at https://help.mothership.com.

## Tracking history

`activity.items` lists the 5 newest tracking updates, newest first, and `activity.total` counts every update. Items with `needsAction` may need the user, such as an added charge or a reschedule. An item's `details`, when set, says more, such as services added to a stop, billable waiting time, or a failed delivery attempt. For older updates, share `trackUrl`, where the dashboard shows the full history.

## Changes and other requests

To cancel the shipment, follow the shipment-cancel skill. It shows the fee and refund estimate before anything changes.

An LTL shipment that's already in progress can't be expedited, even for an extra charge. Never promise to speed it up. Suggest re-booking on a faster service or changing the appointment in the dashboard instead.

The Mothership dashboard at https://dashboard.mothership.com may offer other changes, depending on the shipment:

- changing an appointment
- re-booking on a faster service
- reporting a missed pickup
- filing a claim

For anything else, the Mothership help center is at https://help.mothership.com.
