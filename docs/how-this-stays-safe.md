# How this stays safe

A plain-English version of the contract. If you want the engineering specifics, see [`contributors/safety-contract.md`](contributors/safety-contract.md).

## Nothing changes without showing you first

Every skill that would change something in your store first shows you exactly what will change. You see the current value, the proposed value, and a one-line reason — in plain words, not raw data. Nothing happens until you say "yes."

Real example, from a cycle count:

> You: "I counted 18 of Product A at the Warehouse."
>
> Reading current state…
>
> Product A — Warehouse
> System says: 24
> You counted: 18
> Difference: -6 (likely shrinkage)
>
> Apply this change? Type "yes" to confirm.

If you say nothing or say "no," nothing happens.

## Bulk changes need a clear yes

For changes that touch many records at once — like archiving a batch of products or applying a tag cleanup across dozens of items — typing "yes" alone is not enough. You have to confirm with the count: "yes — archive these 28."

That extra count is on purpose. It forces the change to match what you saw in the preview. If the count doesn't match, the skill stops and asks again instead of guessing.

## Inventory writes have a safety check

Stock adjustments use a "compare with current quantity" guard. The skill reads the current stock, shows you the change, and on confirm it tells Shopify "only apply if stock is still what we just read."

If a sale happened between when you counted and when you confirmed, the write fails safely instead of overwriting that sale. You'll see something like:

> Stock changed since we read it. System says 23 now (was 24 when we started). Your count was 18.
>
> Recount, or apply with the in-flight sale taken into account?

You decide what to do next — never the skill silently.

## Some things are blocked entirely

The Shopify side refuses these — and the skills don't try to route around them:

| What | Why blocked | Where to do it |
|---|---|---|
| Refunds | Shopify | Orders → [order] → Refund in Shopify admin |
| Gift card writes | Shopify | Products → Gift cards in Shopify admin |
| Theme publishing | Shopify | Online store → Themes → Publish |
| Live theme writes | Shopify | Edit on a duplicate, then publish |
| Staff management | Shopify | Settings → Users |

If you ask for one of these, the skill tells you and points you at the right Shopify admin path.

## Every change comes with an undo path

After any write, the skill produces a run log: which records changed, before → after. The log includes the inverse — the exact way to put things back. Just ask "undo the last X" and the skill walks the log in reverse, going through the same preview-and-confirm flow as the original change.

## What's NOT included in this safety promise

- Email or SMS sends (they happen via a different tool).
- Anything you do directly in your Shopify admin.
- Anything you do on someone else's store before connecting yours.
