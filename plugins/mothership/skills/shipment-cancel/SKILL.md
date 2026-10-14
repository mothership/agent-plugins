---
name: shipment-cancel
description: Use when the user wants to cancel a Mothership shipment, or asks whether a shipment can still be cancelled or what cancelling would cost or refund. Checks the shipment with shipment-cancel, shows the fee and refund estimate, asks why, offers a reschedule or edit when that fits, and cancels only after the user says to.
---

# Cancel a Mothership shipment

Cancelling can't be undone, and it charges any cancellation fee. `shipment-cancel` takes two calls. The first only checks. The second cancels.

## Check first

1. Get the shipment's identifier from the user, such as a Mothership reference number like MS123456, a PRO, a BOL, or their own reference.
2. Call `shipment-cancel` with only `identifier`. This call changes nothing.

If the identifier matches more than one shipment, ask the user for the Mothership reference number. If the shipment isn't found, ask them to check the identifier. If the check fails for any other reason, nothing changed. Say you couldn't get the cancellation terms, and never guess a fee.

Check as soon as the user wants to cancel, before you ask why, even when they've already given a reason. The fee and refund don't depend on the reason.

## Show the terms

`outcome` says where the shipment stands.

- `cancellable` means nothing has changed yet. Tell the user the shipment's `referenceNumber`, or its `shipmentId` when that's null, along with the fee and the refund estimate.
- `not-cancellable` means the shipment stays as it is. Explain `notCancellableReason`. The dashboard applies the same rules, so don't send the user there to cancel it. When the reason says the shipment isn't eligible for self-service cancellation, point the user to the Mothership help center at https://help.mothership.com.
- `cancelled` comes only from the second call.

`cancellationFeeUsd` is in US dollars, and 0 means cancelling is free. Write amounts like $150.00. `feeReason` says why there's a fee when Mothership has an explanation for it. It can be null even when there's a fee, so don't make up a reason.

`refundEstimate` has three amounts. `refundUsd` goes back to the payments that paid for the shipment. `creditUsd` goes back to the account as credit. `totalShipmentCostsUsd` is the shipment charges the cancellation returns or drops, paid or unpaid. Quote each amount as it is and the fee on its own, without adding or subtracting them. When `refundEstimate` is null, say the refund estimate isn't available, not that the refund is $0.

## Ask why

Once the user has seen the terms, ask one short question about why they're cancelling, unless they already said. Match their answer to one of the reasons `confirmation.reason` accepts.

When the reason is `cargo-not-ready`, `incorrect-cargo-info`, `shipment-corrections-needed` or `missing-accessorial`, tell the user they may be able to reschedule or edit the shipment instead, from the shipment in the Mothership dashboard at https://dashboard.mothership.com. Cancel only if they still want to.

## Cancel

Call `shipment-cancel` again only after the user has seen the fee and refund estimate and told you to cancel. A reason alone isn't a yes.

- Pass `identifier` as the check's `referenceNumber`, or its `shipmentId` when `referenceNumber` is null.
- In `confirmation`, pass the check's `confirmationToken` as `token`, its `cancellationFeeUsd` as `acceptedFeeUsd`, and the user's reason as `reason`.

A token works once, until `confirmationExpiresAt`, and a confirmation that fails still uses it up. Never reuse a token or a fee from an earlier check. If the token has expired, or the user comes back to it later, check again and show the terms again.

When `outcome` is `cancelled`, tell the user the shipment is cancelled, with the fee and the refund estimate. When `cancellationFeeUsd` is null, don't quote a fee.

The second call returns `not-cancellable` instead when the shipment can't be cancelled anymore, such as after pickup. Nothing changed, so explain `notCancellableReason`.

## When a confirmation fails

Nothing is cancelled when the fee or refund estimate changed since the check, or when the token expired. The error says which. An error can also come after an earlier confirmation already went through.

When the error says the shipment can't be cancelled here, an earlier cancellation of it set a higher fee than the one the user accepted. Checking again would show the same terms, and the next confirmation would fail the same way, so don't check again. Tell the user nothing was cancelled, and that Mothership support can help through the help center at https://help.mothership.com.

For any other error, call `shipment-cancel` again with only `identifier` before you tell the user anything.

- `cancellable` means it wasn't cancelled. Show the user the new terms and ask again before you confirm with the new token.
- `not-cancellable` can mean the shipment is already cancelled, and then `notCancellableReason` says so. To confirm, call `shipment-get` with the same identifier. A `status.phase` of `cancelled` means the shipment is cancelled. Don't quote a fee, because neither result says what was charged.

## Who can cancel

Only the user who booked the shipment, or a teammate who can manage shipments, can cancel it, here or in the dashboard.
