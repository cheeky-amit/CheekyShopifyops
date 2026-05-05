# Bulk archive (or activate) a group of products

When you already know which products should change status — by vendor, tag, or collection — sweep them all to the same status in one safe pass.

## What you can say

> "Archive all out-of-stock products from Vendor A."
> "Set the Spring 2025 collection to draft."
> "Bulk-archive everything tagged 'discontinued'."

## What you'll see back

A numbered list of every product that matches your group, with the count, the current state, and the target state. You'll see how many already have the target status (skipped) and how many will actually change.

## When it will ask before doing anything

This is a bulk write. Confirmation has to be explicit and include the count: "yes — archive these 28." A simple "yes" or "ok" is rejected on purpose, because muscle-memory shouldn't change 28 products. If your group is bigger than 50, the skill stops, tells you, and asks how to slice it (run in passes, or narrow further).

## What it won't do

- Won't process more than 50 products per run. The 50-cap is a safety belt: bigger sweeps need to run again with a narrower group, so the blast radius of one "yes" stays small.
- Won't delete products. Status changes only.
- Won't change titles, descriptions, prices, or tags — only status.

## Settings you can change

- **Group of products** (required). Vendor, tag, collection, status, stock — any combination.
- **Target status** (required). Active, draft, or archived.

## Want to see under the hood

It searches your catalog with the group you specified, lists every match, asks for explicit confirmation including the count, re-checks each product right before writing (so any drift since the preview is caught), and writes status changes in one batched call. The run log includes the inverse so undo is one ask.

## How this is different from product-status-audit

- [`catalog.product-status-audit`](../product-status-audit/) finds problems across multiple directions (some to archive, some to activate, some to draft) and proposes per-product fixes.
- This skill applies a single target status to a group you already chose.

## Related

- [`catalog.product-status-audit`](../product-status-audit/) — multi-direction status fixes.
- [`catalog.duplicate-detector`](../duplicate-detector/) — find the duplicates first.
- [`catalog.tag-cleanup`](../tag-cleanup/) — for tag changes, not status.
