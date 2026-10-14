---
name: freight-class
description: Use when the user asks what freight class or density their freight is, or how to work out a class from weight and dimensions. Calculates density and class with freight-class-calculate, explains that the class comes from density alone, and hands off to quote-create for rates.
---

# Estimate a freight class

`freight-class-calculate` works out each handling unit's density in pounds per cubic foot, and the LTL freight class for that density. It uses density only. It doesn't look up NMFC items, so it can't say which NMFC item covers a commodity or what class that item sets.

## Gather each unit

The calculation needs four numbers for each distinct handling unit, such as one pallet:

- the weight of one unit in pounds, packaging included
- its length, width and height in inches

Ask for whichever the user hasn't given, and never fill one in yourself. Don't calculate from a weight without all three dimensions, or from dimensions without a weight. A standard pallet is 48 x 40 inches, but its height and weight have to come from the user. When the user gives one weight for several units, confirm the weight of one unit before you calculate.

## Calculate

Call `freight-class-calculate` with one `cargo` line per distinct unit, each with `weightLbs`, `lengthIn`, `widthIn` and `heightIn`. Identical units share one class, so five identical pallets are one line. The results come back in the same order as the lines.

Each result has `densityLbsPerCuFt` and `freightClass`. Give the user the class and the density for each unit.

## What the class means

The class is an estimate from density. Many commodities have an NMFC item that sets their class, and the carrier can inspect the freight and reclassify it, which changes the price.

Never guess an NMFC item number, or say which one covers the user's freight. When the user needs one, say you can't look up NMFC items here.

## Get rates

For rates, follow the quote-create skill. Carriers price on density from the same weights and dimensions, so `quote-create` doesn't need the class you calculated. Pass `freightClass` to `quote-create` only when the user knows their class, such as from their NMFC item, because carriers then price on that class instead of density.
