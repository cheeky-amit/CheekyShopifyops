# Receive a shipment into stock

Add received quantities to one or more variants at a location, with reason `received` and an optional PO reference for the log.

> "I just received 24 of each size of Product A at the Warehouse, PO #12345."
> "Receive shipment."

## What it does

1. Reads current stock for each line.
2. Shows the diff (current → current + received).
3. You confirm.
4. Writes per-row with `compareQuantity` so a parallel sale on any one line can't be silently overwritten.
5. Logs with PO reference for audit.

## Cap

50 line items per run. Bigger shipments split into multiple runs.

## What this skill won't do

- Send a PO to a supplier — no supplier MCP. See `meta.wiring`.
- Track shipping/freight/landed cost — out of MCP scope.
