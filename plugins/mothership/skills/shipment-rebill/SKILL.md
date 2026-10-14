---
name: shipment-rebill
description: Use when the user asks about a Mothership rebill or an extra charge billed after a shipment, such as a reweigh, reclass, detention or delivery attempt, what a charge is for, when it will be charged, how to dispute it, or where a dispute stands. Looks rebills up with shipment-rebill-list and shipment-rebill-get, explains the charges and the review window, and sends accepting and disputing to the dashboard.
---

# Explain a Mothership rebill

A rebill passes on what a carrier billed after the shipment beyond the quoted price, such as a reweigh, detention or a delivery attempt. Rebill reference numbers look like MSSR123456.

## Find the rebill

- For a rebill reference number, call `shipment-rebill-get` with it as `shipmentRebillIdOrReferenceNumber`.
- For a shipment, call `shipment-rebill-list` with its Mothership reference number, such as MS123456, as `shipmentIdentifier`. If the user gave a PRO, BOL or their own reference, call `shipment-get` first and pass its `referenceNumber`, or its `shipmentId` when that's null.
- For rebills waiting on the user, call `shipment-rebill-list` with `statuses` set to `scheduled` and `failed`. For disputes where Mothership needs more from the user, set `disputeStatuses` to `info-needed`.

`shipment-rebill-list` returns `total` matches, newest first, with up to 10 line items each. When `hasMore` is true, page with `offset`. When `total` is 0, say no rebills match. For what each charge is and what a dispute needs, call `shipment-rebill-get` with the rebill's `shipmentRebillId`.

## Answer what was asked

Lead with the direct answer in one or two sentences, such as the status, the amount, or when the review window ends. Offer the line items when the user wants more.

- Amounts are in cents. Write them in dollars, like $125.00. `totalAmountCents` is the rebill's total. In `shipment-rebill-get`, that's `amountCents` for the line items plus `adminFeeCents`, the carrier bill processing fee.
- Each line item's `explanation` says what the charge is, and `avoidanceGuidance` says how to avoid it on later shipments. Both are Markdown. Share them when the user asks why they were charged or how to avoid it.
- When the results don't answer the question, say so and point the user to the Mothership help center at https://help.mothership.com.

## Status and the review window

`status` says where the rebill stands.

- `scheduled` is waiting on the user. They can accept or dispute it until `disputableUntil`, when it's charged. Give that date and time and name the time zone. When `disputableUntil` is null, the rebill has no review window, so don't make one up.
- `processed` means Mothership finished processing it. It doesn't mean the payment went through.
- `failed` means Mothership couldn't charge it automatically, so the user pays the open invoice. `invoiceId` from `shipment-rebill-get` is that invoice, and `invoice-get` shows it.
- `waived` and `voided` mean the user pays nothing.

## Disputes

A line item's `dispute` is null when it was never disputed. Otherwise `dispute.status` says where it stands.

- `under-review` means Mothership is reviewing it, and `with-carrier` means it went to the carrier.
- `info-needed` means Mothership needs more from the user. Share `infoNeededDetails`.
- `resolved-approved` and `resolved-waived-approved` mean the user isn't charged for that line item. `resolved-charged` means the carrier upheld the charge.
- `withdrawn` means the user withdrew it and accepted the charge. `voided` means it closed because the rebill was waived or voided.

Report a dispute's status as the tool returns it. Never predict an outcome, and never say a charge will be removed while a dispute is open.

When the user is thinking about disputing, share what it needs first. `evidenceRequiredForDispute` says what a dispute needs to succeed, and `disputeRequiresDocuments` says whether it must include supporting documents.

## Accept or dispute in the dashboard

These tools only read rebills. The user accepts, disputes or adds documents from the shipment's page in the Mothership dashboard, or under Billing at https://dashboard.mothership.com. `shipment-get` returns `trackUrl`, which opens the shipment's page. For a `scheduled` rebill, remind the user when the review window ends.
