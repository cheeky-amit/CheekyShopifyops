# Launching a sale

Pick what to discount with revenue context, then scope it safely. Four skills chained: shop snapshot, product performance, collection sale, discount audit.

---

> You: "I want to run a Memorial Day sale. Where should I start?"

Quick read on the store first.

**Shop snapshot**
- Plan: Shopify, USD, 1 location.
- Catalog: ~120 products, 14 collections.
- Last 30 days: 142 orders, AOV $XX.XX, 39% returning.

**Top products, last 30 days** (by revenue)
1. Product A — $X,XXX (28% of revenue)
2. Product B — $X,XXX (14%)
3. Product C — $XXX (9%)
4. Product D — $XXX (7%)
5. (rest tail off)

Of your 14 collections, the "Summer Essentials" collection has 22 products and accounts for ~31% of last-30-day revenue. That's usually a good place for a seasonal sale.

> You: "let's do 20% off Summer Essentials, code MEMORIAL20, runs Friday through Monday"

Here's the plan:

- **Code:** MEMORIAL20
- **Type:** 20% off, automatic at checkout when products in scope are present
- **Scope:** Collection — Summer Essentials (22 products, ~31% of last-30-day revenue)
- **Active:** Fri 12:00 a.m. → Mon 11:59 p.m. local time
- **Usage limit:** none (one-per-customer off)
- **Stacks with:** other automatic discounts off

Estimated revenue at risk: ~$X,XXX (based on last 30 days at the 20% level).

To create this code, type: **yes — create MEMORIAL20**.

> You: "yes — create MEMORIAL20"

Created. Code MEMORIAL20 is set to activate Friday at midnight.

Want me to verify it later?

> You: "yes, audit discounts on Friday"

Got it. I'll re-check Friday morning.

---

(Friday morning)

> You: "audit my discounts"

Active discount codes right now:

| Code | Type | Scope | Active | Usage |
|---|---|---|---|---|
| WELCOME10 | 10% off | first order | always-on | 14 uses last week |
| MEMORIAL20 | 20% off | collection: Summer Essentials | starts today, 4-day window | 0 uses |
| (one expired code, hidden) | | | | |

MEMORIAL20 is live as configured. Tracking it through the weekend.

---

— under the hood —

Four skills: `storefront.shop-snapshot` for the read, `analytics.product-performance` for the top sellers, `promotions.collection-sale` to scope and create the code, `promotions.discount-audit` to verify it went live. The discount creation followed the standard preview → confirm flow — nothing was created until you typed "yes — create MEMORIAL20."
