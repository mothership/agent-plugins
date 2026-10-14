---
name: freight-class
description: Use when the user asks what freight class their freight is, which NMFC item or code covers what they ship, what an NMFC code means or what class it sets, or how density sets a class. Finds NMFC items with nmfc-search and nmfc-get, calculates density and class from weight and dimensions with freight-class-calculate, and hands off to quote-create for rates.
---

# Find a freight class

An NMFC item sets the LTL freight class for most commodities. Some items set one class. Others set a class by the freight's density, or by what the freight is, such as assembled or knocked down. Density alone gives an estimate when no item is known.

Three tools answer these questions:

- `nmfc-search` finds the NMFC item that covers what the user describes.
- `nmfc-get` reads one NMFC item by its code, with every class it sets and what decides each one.
- `freight-class-calculate` works out density and the class for that density from weight and dimensions. It doesn't look up NMFC items.

## Pick the tool

- When the user describes what they ship, such as "office chairs" or "steel pipe fittings", call `nmfc-search`.
- When the user gives an NMFC code, such as 156600 or 156600-03, or you have one from a shipment, call `nmfc-get`. Don't search for a code.
- When the user has only weights and dimensions, or asks what class a density gives, call `freight-class-calculate`.
- When the user describes the freight and gives its measurements, call `nmfc-search` with `dimensions`. Items classed by density then come back with the class those measurements settle on.

## Gather measurements

Density needs four numbers for each distinct handling unit, such as one pallet:

- the weight of one unit in pounds, packaging included
- its length, width and height in inches

Ask for whichever the user hasn't given, and never fill one in yourself. Pass `dimensions` to `nmfc-search` or `nmfc-get` only when you have all four, and don't calculate from a weight without all three dimensions. A standard pallet is 48 x 40 inches, but its height and weight have to come from the user. When the user gives one weight for several units, confirm the weight of one unit first.

## Search by description

Call `nmfc-search` with `query`, the commodity in the user's words. Material, form, packaging and use all help, so "plastic chairs, knocked down, in boxes" finds a closer item than "chairs". Searches are rate limited, so send one specific query rather than many variations.

Read `outcome`:

- `match` names the item that fits in `match.itemNumber`, with `confidence` and `reasoning`. Its entry in `items` has its class. When the confidence is low, call it a likely match and show the user the next closest items too.
- `clarify` means the answer to a question picks the item. Ask `clarify.question` and offer the `options`. Then search again with the user's answer added to the query.
- `no-match` means no item fits. Ask for a more specific description, such as the material, the form, or what it's used for. You can show the closest `items` for the user to choose from.

When `noMatch.matchingFailed` is true, matching gave no verdict, so it doesn't mean no item fits. `noMatch.reasoning` says why. When it says matching is unavailable, search again shortly. Otherwise ask for a more specific description, or let the user choose from the closest items. Never tell the user no item covers their freight in that case.

Never guess an NMFC item, and never pick one from `items` yourself. Only a `match`, or an item the user chooses, names their item.

## Read an item's classes

`classBasis` says what sets an item's class, in both tools:

- `fixed`: the item has one class, `freightClass`.
- `density`: density sets the class. `freightClassFromDimensions` is the class at the dimensions you passed. Without dimensions, ask for them, or give the user the item's `densityClasses` from `nmfc-get`, the class for each density range.
- `criteria`: what the freight is sets the class, such as assembled or knocked down, or a size or value range. `nmfc-get` lists each criterion and its class. Ask the user which one applies. When a search `match` has a `suggestedFreightClass`, the description already settled it, and `classReasoning` says why. When the criteria are size ranges and you passed dimensions, `freightClassFromDimensions` gives the class.
- `criteria-and-density`: a criterion, such as packaging, picks a density scale. Ask which criterion applies. That criterion's `freightClass` is the class at the dimensions you passed, and its `densityClasses` give the class for each density range.
- Null means the item lists no class. Say so.

When the search result doesn't settle the class, call `nmfc-get` with the item's number for its criteria and density ranges. Then give the user the item number, what it covers, the class, and what decided it.

A code with a sub number, such as 156600-03, returns the whole item and `subItem`, the sub-item that number names, with its class. For an item classed by criteria, that's the criterion the code picks. For an item classed by density, it's one density range, and the carrier classes on the density it measures, so check it against `freightClassFromDimensions` when you have the dimensions. When `subItem.freightClass` is null, the sub-item heads a density scale, so use that criterion in `criteria`.

`nmfc-get` fails when no item has that number, or when the item doesn't list the code's sub number. The code may be mistyped or out of date, so ask the user to check it, or ask what they ship and search.

When `isCanceled` is true, the NMFC canceled the item and its classes no longer apply. Tell the user, and don't give them its class. When `canceledReference` names the item that replaced it, look that one up. Otherwise ask what they ship and search.

Mention what the flags say:

- `isHazardous`: the item covers hazardous materials, and a quote needs the hazmat details quote-create asks for.
- `isNotOtherwiseIndicated`: an NOI item, a catch-all for goods no more specific item names. A more specific description may find a closer item.
- `requiresSpecialHandling`: the NMFC marks the item for special handling.

## Calculate from density

Call `freight-class-calculate` with one `cargo` line per distinct unit, each with `weightLbs`, `lengthIn`, `widthIn` and `heightIn`. Identical units share one class, so five identical pallets are one line. The results come back in the same order as the lines.

Each result has `densityLbsPerCuFt` and `freightClass`. Give the user the class and the density for each unit. A class from density alone is an estimate. When an NMFC item covers the freight, the item's class applies.

## What the class means

An NMFC class comes from the published tariff. The carrier can inspect the freight and reclassify it, which changes the price.

## Get rates

For rates, follow the quote-create skill. Carriers price on density from the same weights and dimensions, so `quote-create` doesn't need a class. Once the user confirms their NMFC item and its class, pass the code as the cargo line's `nmfcCode` and the class as its `freightClass`, and carriers price on that class instead of density. Don't pass a class you calculated from density alone.
