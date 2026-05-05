# First-order anniversary list

Customers approaching the one-year mark since their first order — a natural moment to say "thanks, come back." The list is small by design and tends to convert better than a generic blast.

## What you can say

> "Who's coming up on their one-year with us?"
> "First-order anniversary."
> "Two-year anniversary list."

## What you'll see back

The size of the group, the date window it covers, and a list of the people in it. Each row shows the customer (first name + last initial only), when their first order happened, what they've spent total, and how many orders they've placed since. Default window: first orders that fall inside one year, plus or minus 14 days from today.

## What it won't do

- Won't email anyone. There's no email tool in this bundle — this skill builds the list, you (or a separate email tool) send.
- Won't apply a discount automatically. If you want a "thank you" code scoped to this group, ask to create one.
- Won't include people who never opted into marketing. Subscribed customers only by default.
- Won't change anything — read-only.

## Privacy note

In list views, customer names are shown abbreviated to first name + last initial — e.g., "Customer A B." for "Alex Brown." That keeps the list scannable and respects your customers' privacy. To see one specific customer in full, ask for them by name, position, or email — for example, "show Customer A B." or "show #1." Email addresses are never shown in the summary view.

## Settings you can change

- **Anniversary** (default: 1 year). Any whole-year mark works — "two-year anniversary," "three-year."
- **Window** (default: ± 14 days). Tighter or wider, depending on how often you want to run this.
- **Subscribed only** (default: on). Off pulls everyone, but the skill warns you about emailing non-subscribers.

## Want to see under the hood

It looks up each customer's first-ever order date, keeps the ones whose first order falls inside the anniversary window, filters to subscribed customers, and abbreviates names for the summary view.

## Related

- [`retention.win-back-list`](../win-back-list/) — lapsed customers worth re-engaging.
- [`customers.high-value-list`](../../customers/high-value-list/) — top spenders, no anniversary filter.
- [`promotions.discount-create`](../../promotions/discount-create/) — make a "thank you" code for this group.
- [`meta.wiring`](../../meta/wiring/) — how to add an email tool alongside this bundle.
