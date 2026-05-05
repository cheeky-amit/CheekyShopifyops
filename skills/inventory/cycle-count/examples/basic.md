# Example — cycle count

```
> You: "I counted 18 of Product A at the Warehouse."

  Product A · Size M · Warehouse
    System shows: 24
    You counted:  18    (a difference of 6)
    Reason:       cycle count

  Type "yes" to apply.

> You: "yes"

  Done. Updated to 18 at the Warehouse.
  If you want to undo, say "set Product A at Warehouse back to 24."

— under the hood —
  Read current stock → show the change → wait for confirmation → write the new
  number with a safety check (so any sale that happened during your count can't
  get silently overwritten).
```

When a product has multiple variants and you didn't say which:

```
> You: "I counted 18 of Product A."

  Product A has multiple variants. Which one did you count?
    • Size S    (currently 12)
    • Size M    (currently 4)
    • Size L    (currently 0)

  And if more than one location carries it, mention the location too.
```

When the difference is large and negative:

```
> You: "I counted 12 of Product B at Warehouse" (system shows 24)

  Product B · Warehouse
    System shows: 24
    You counted:  12    (a difference of 12)
    Reason:       cycle count

  That's a -12 difference. If the cause is theft, damage, or loss rather
  than a counting mistake, "shrinkage" is a more honest reason.

  Want to switch?
    • "yes, mark as shrinkage"
    • "no, keep it as cycle count"
```
