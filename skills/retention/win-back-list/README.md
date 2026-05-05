# Lapsed customers worth winning back

Pull a list of past buyers who've gone quiet — people who used to come back, haven't in a while, and are still on your email list — so you have somewhere to start when you want to re-engage.

## What you can say

> "Build a win-back list."
> "Lapsed customers."
> "Who used to buy and stopped?"

## What you'll see back

A short summary of the group (how many people, their average lifetime spend, average past order count, average lapse time) followed by the top spenders in the group, ranked by lifetime value. Each row shows the customer (first name + last initial only), what they've spent total, how many orders, and when they last bought. Default view: top 30 by spend.

## What it won't do

- Won't email anyone. There's no email tool in this bundle — this skill builds the list, you (or a separate email tool) send.
- Won't apply a discount automatically. If you want a discount code scoped to this group, ask to create one and reference the win-back segment.
- Won't include people who never opted into marketing. Subscribed customers only by default — you can change that, but the skill flags it.
- Won't change anything — read-only.

## Privacy note

In list views, customer names are shown abbreviated to first name + last initial — e.g., "Customer A B." for "Alex Brown." That keeps the list scannable and respects your customers' privacy. To see one specific customer in full, ask for them by name, position, or email — for example, "show Customer A B." or "show #1." Email addresses are never shown in the summary view.

## Settings you can change

- **Lapse threshold** (default: 6 months). Recent enough that the brand is still familiar, old enough to count as gone.
- **Past orders minimum** (default: 2). Filters one-time buyers — they're a different problem than retention.
- **Subscribed only** (default: on). Off pulls everyone, but the skill warns you about emailing non-subscribers.

## Want to see under the hood

It picks customers whose last order is older than the lapse threshold, who have at least the minimum past orders, and who are subscribed to email; it ranks them by lifetime spend; and it abbreviates names for the summary view.

## Related

- [`customers.high-value-list`](../../customers/high-value-list/) — top spenders, no lapse filter.
- [`customers.subscriber-audit`](../../customers/subscriber-audit/) — who's on your list at all.
- [`retention.first-order-anniversary`](../first-order-anniversary/) — customers approaching their one-year mark.
- [`promotions.discount-create`](../../promotions/discount-create/) — make a discount code scoped to this group.
- [`meta.wiring`](../../meta/wiring/) — how to add an email tool alongside this bundle.
