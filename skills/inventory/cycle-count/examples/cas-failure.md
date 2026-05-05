# Example — what happens if a sale happened during your count

You counted 18 of Product A at the Warehouse at 14:30. You came back to confirm at 14:32. Between those two minutes, an online order shipped one unit out of the Warehouse. The system now shows 23, not the 24 it showed when you started.

```
> You: "I counted 18 of Product A at the Warehouse."

  Product A · Size M · Warehouse
    System shows: 24
    You counted:  18    (a difference of 6)
    Reason:       cycle count

  Type "yes" to apply.

> You: "yes"

  Heads up — stock changed since you counted.
  System now shows 23, not 24. An order probably shipped between your
  count and your confirmation.

  No change applied yet. Two options:
    1) Recount and try again (safest).
    2) Apply 17 instead of 18 — meaning "the floor showed 18, then a sale
       removed 1, so the new on-hand is 17."

  What do you want to do?
```

The skill never silently retries with the new starting number — that would defeat the safety check. You decide.

— under the hood —
  The write carries a safety check that says "only apply if stock is still 24."
  When the system reports it's now 23, the write declines, and the skill walks
  you through the choice instead of overwriting the sale.
