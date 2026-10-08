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
- Carrier, SCAC, PRO, BOL and pickup numbers are under `carrier`. Service level, delivery commitment and transit time are under `service`. Coverage is under `coverage`.
- `invoicedTotalUsd` is the total invoiced in US dollars, net of credits, written like $609.75. When it's null, say the invoiced total isn't available. Null can mean nothing is invoiced yet or the amount is unavailable, so don't pick one. Never present null as $0.
- `cargo` lists every line, with per-unit weight and dimensions. Multiply `weightLbsEach` by `quantity` for a line's weight.
- Share document links from `documents[].url`, and the shipment's dashboard page from `trackUrl`.

## Times and windows

- Each time has `at`, an ISO instant for reasoning and comparisons, and `local`, the same moment in the stop's own time zone for showing the user, such as "Wed, Oct 7, 2026 2:15 PM PDT". If you convert to the user's time zone, say so.
- `pickup.window` and `delivery.window` hold the scheduled service window. `arrivedAt` and `completedAt` record what happened. Until `completedAt` is set, describe the stop by its window.
- `facilityHours` is the location's business hours, which are separate from the service window.

## Arrival times and location

- For LTL freight, the scheduled window is the carrier's arrival commitment. When the user asks for a live ETA, an exact arrival time, or where the driver is, give the scheduled window and explain that LTL carriers commit to a window.
- `lastTrackingUpdateAt` is the newest tracking signal for the shipment. It doesn't prove the carrier was contacted at that time.
- `lastKnownLocation` and `locationHistory` show where the carrier last reported the freight. When they're empty, say the carrier hasn't shared a location update.

## When pickup is still being confirmed

Start from `status.name` and `status.attention`. When the status shows the shipment is cancelled, rescheduled, or needs attention, explain that status.

When `status.phase` is `awaiting-pickup` and `carrier.name` is null, no carrier is assigned yet and Mothership is still finding one. Say so, and don't describe the pickup as booked.

When `status.phase` is `awaiting-pickup`, `carrier.name` is set, the pickup window has started, and `pickup.completedAt` isn't set yet:

- Reassure the user that the pickup is booked on the carrier's schedule.
- LTL carriers usually deliver in the morning and pick up in the late afternoon, so the driver can arrive any time until the location closes. Pickup confirmation often appears in the evening, once the carrier's local terminal finishes its stops.
- Suggest having the freight ready and easy to reach at the dock.

If the user says the driver didn't arrive or couldn't complete the pickup, they can report it from the shipment in the Mothership dashboard at https://dashboard.mothership.com.

## Tracking history

`activity.items` lists the 5 newest tracking updates, newest first, and `activity.total` counts every update. Items with `needsAction` may need the user, such as an added charge or a reschedule. For older updates, share `trackUrl`, where the dashboard shows the full history.

## Changes and other requests

The Mothership dashboard at https://dashboard.mothership.com handles changes to a shipment:

- changing an appointment
- re-booking on a faster service
- cancelling
- reporting a missed pickup
- filing a claim

For anything else, the Mothership help center is at https://help.mothership.com.
