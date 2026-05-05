# Receive a shipment into stock

Add a delivery's quantities to one or more variants at a location, with the reason "received" and an optional purchase order reference for the audit trail.

## What you can say

> "I just received 24 of each size of Product A at the Warehouse, PO #12345."
> "Receive shipment."
> "Received PO #12345."

## What you'll see back

A line-by-line preview: each variant, the location, the current stock, and the new stock after the receipt. You'll see the total number of lines and the reason. Nothing changes from the preview alone.

## When it will ask before doing anything

You'll see exactly how much will be added to each variant at each location, with current and new totals, before any number changes. Type "yes" to apply. Each line carries its own safety check — if a sale happens on one line between your read and your write, only that line is held back; the rest still apply, and you'll be told which one to recount.

## What it won't do

- Won't process more than 50 lines per run. Bigger shipments split into multiple runs.
- Won't send a purchase order to a supplier — there's no supplier connection in this skill.
- Won't track shipping, freight, or landed cost — out of scope here.
- Won't refund or cancel anything — refunds are blocked at Shopify; do those in your admin.

## Settings you can change

- **Purchase order reference** (optional). A short label like "PO #12345" that goes into the audit trail.
- **Location** (required if more than one). Which warehouse or store the shipment landed at.

## Want to see under the hood

For each line: read current stock → preview "current → current + received" → wait for confirmation → write the new number with the per-line safety check. Any line whose stock changed mid-flight is skipped (not silently merged) and reported back so you can recount that one.

## Related

- [`inventory.snapshot`](../snapshot/) — confirm where things landed.
- [`inventory.cycle-count`](../cycle-count/) — adjust after a count, not a receipt.
- [`inventory.low-stock-alert`](../low-stock-alert/) — what to reorder next.
