# Receiving a shipment

PO arrived from a vendor. Three skills chained: low-stock check before, receive-shipment write, snapshot after to confirm.

---

> You: "Vendor X shipment came in. What's running low for them?"

Reading current stock for Vendor X products…

| Product | On hand | ~Days of cover |
|---|---|---|
| Product A | 4 | ~9 days |
| Product B | 7 | ~12 days |
| Product C | 11 | ~15 days |

That's the bottom of your Vendor X shelf right now. What did the shipment include?

> You: "20 of A, 30 of B, 25 of C, 10 of D, all to the Warehouse"

Here's what I'll add at the Warehouse:

| Product | Current | Adding | New total |
|---|---|---|---|
| Product A | 4 | 20 | 24 |
| Product B | 7 | 30 | 37 |
| Product C | 11 | 25 | 36 |
| Product D | 18 | 10 | 28 |

To apply all 4, type: **yes — receive these 4**.

> You: "yes — receive these 4"

Re-reading current stock to make sure nothing changed mid-conversation…

Stock for Product A changed: was 4 a moment ago, is now 3 (one sold). I'll add 20 to whatever it is now: 3 → 23.

The other three are unchanged.

Apply? Type **yes** to proceed.

> You: "yes"

Applied:

| Product | Was | Now |
|---|---|---|
| Product A | 3 | 23 |
| Product B | 7 | 37 |
| Product C | 11 | 36 |
| Product D | 18 | 28 |

Run log saved. To undo, ask "undo the last receive."

> You: "snapshot for vendor X"

Vendor X — Warehouse, post-receive:

- 4 SKUs in stock (24 / 37 / 36 / 28)
- 0 SKUs at risk (none under 10)
- 0 SKUs out of stock

Looks healthy. Anything else?

> You: "nope, thanks"

---

— under the hood —

Three skills: `inventory.low-stock-alert` to scope before, `inventory.receive-shipment` to apply (with the safety check that catches the in-flight sale on Product A), and `inventory.snapshot` to confirm after. The mid-flow recheck is the compare-with-current-quantity guard — it caught a sale that happened between the preview and the confirm, and showed you the new totals before applying.
