# Apply a cycle-count adjustment

Reconcile what you counted on the floor against what the system thinks — safely, without overwriting any sale that happened mid-count.

## What you can say

> "I counted 18 of Product A at the Warehouse."
> "Cycle count for SKU-A: 18."
> "Set Product A at Store to 12, reason cycle count."

## What you'll see back

The skill reads the system's current count for that variant at that location, shows you the difference between what's on file and what you counted (e.g. "system 24, you counted 18, that's a difference of -6"), and asks you to confirm. After the change applies, you get a clear "done" with the new number and how to undo it.

## When it will ask before doing anything

Before any number changes, you'll see exactly which product, which variant, which location, the current stock, the new stock, and the reason. You confirm by typing "yes." Nothing happens until then.

There's a second safety check baked in: **if a sale happened between when you counted and when you confirm, we won't silently overwrite that sale.** The skill will tell you stock changed, refuse to apply your number as-is, and ask whether to recount or to apply with the in-flight sale taken into account. (Example: you counted 18, but an order shipped between your count and your confirmation — your "18" might now mean "17 on the floor.")

## What it won't do

- Won't apply a cycle count if stock changed since you read it. You'll be asked to recount or accept the in-flight change.
- Won't change anything other than stock at one location for one variant. Title, price, etc. stay untouched.
- Won't merge counts across locations — each location is its own count.
- Won't refund or cancel orders — refunds are blocked at Shopify; do those in your admin.

## Settings you can change

- **Reason** (default: cycle count). Options include shrinkage, damage, correction. The skill suggests "shrinkage" if your delta is large and negative, since "we lost stock" is more honest than "we miscounted by 12."

## Want to see under the hood

It reads current stock for the variant at the location, shows you the change, asks for confirmation, and then writes the new number with a safety check that says "only apply if stock is still what we just read." If a sale happened in between, the write declines instead of clobbering the sale, and you decide what to do next. The run log carries the previous number so undo is one ask.

## Related

- [`inventory.snapshot`](../snapshot/) — see current stock before counting.
- [`inventory.receive-shipment`](../receive-shipment/) — add stock when a delivery arrives.
- [`inventory.low-stock-alert`](../low-stock-alert/) — find what's running low first.
