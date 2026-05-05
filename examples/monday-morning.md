# Monday morning

End-of-week pulse. Three skills chained: shop pulse, stuck-orders watchlist, weekly brief.

---

> You: "weekly brief"

Pulling last week and comparing to the prior week.

**Last week (Apr 28 – May 4)**

- Revenue: $XX,XXX (up 12% vs prior 7 days)
- Orders: 142 (up 8%)
- AOV: $XX.XX (up 4%)
- New vs returning: 61% / 39% (returning share up 3 pts)

**Top movers**
- Product A — +28% week-over-week
- Product B — +14%
- Product C — −22% (worth a look)

**Watch list**
- 4 paid orders are unfulfilled and older than 72 hours.
- 6 SKUs are running low based on last 30 days of velocity.
- 2 products are live but currently out of stock.

Want to dig into any of these?

> You: "show me the stuck orders"

Stuck orders, oldest first:

| # | Order | Age | Customer | Total | Lines |
|---|---|---|---|---|---|
| 1 | ORDER-1042 | 6 days | Customer A B. | $XX.XX | 3 |
| 2 | ORDER-1051 | 5 days | Customer C D. | $XX.XX | 1 |
| 3 | ORDER-1058 | 4 days | Customer E F. | $XX.XX | 2 |
| 4 | ORDER-1063 | 3 days, 4 hrs | Customer G H. | $XX.XX | 1 |

(Names are abbreviated. Ask for one by number to see full details.)

> You: "what's running low?"

Top 6 by sell-through risk (using last 30 days of velocity):

- Product A — 4 left, ~9 days of cover at current pace
- Product B — 7 left, ~12 days
- Product C — 11 left, ~15 days
- Product D — 6 left, ~17 days
- Product E — 9 left, ~20 days
- Product F — 14 left, ~22 days

Want a draft purchase-order list, or just leave it as a watchlist?

> You: "just the watchlist for now, thanks"

Got it. I'll re-check next Monday.

---

— under the hood —

Three skills running in sequence: `reporting.weekly-merchant-brief` rolls up the highlights, `orders.fulfillment-watchlist` surfaces the stuck queue, `inventory.low-stock-alert` ranks by sell-through. Everything here is read-only.
