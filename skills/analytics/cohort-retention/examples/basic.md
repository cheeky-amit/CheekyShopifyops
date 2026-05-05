# Example — new vs returning

```
> You: "New vs returning customers last 90 days."

  example-shop · new vs returning · last 90 days

    Returning rate (period):       34%   (+4 pp vs prior 90 days)
    Returning rate (last week):    36%

    New customers (90 days):       420
    Returning customers (90 days): 218

  [time-series chart: new (dotted) vs returning (solid), weekly]

  Trend: returning rate has crept up 4 points over the period — your
  repeat-purchase engine is working better than three months ago. Want to
  know who's coming back? Ask "snapshot of repeat buyers."

— under the hood —
  Pulls orders for the window, splits into first-ever vs repeat purchases,
  computes the returning rate per period, and adds a one-line read.
```

Small store — too little history:

```
> You: "Customer retention."

  example-shop · new vs returning · last 90 days

    Heads up: your store has 38 days of order history. That's too little for
    a stable retention rate — one or two customers coming back can swing
    the number several points. Come back to this in a couple of months for
    a number you can trust.

    For now: 14 customers have bought, 2 of them more than once.
```

Subscription skew:

```
> You: "Returning customer rate."

  example-shop · new vs returning · last 90 days

    Returning rate (period):    72%   (+1 pp vs prior 90 days)

    Heads up — that 72% is unusually high. Do you run subscription
    products? Each renewal counts as a "returning" purchase here, which
    can make the rate look stronger than your one-time-purchase business
    actually is. If yes, the underlying non-subscription rate is closer
    to 28%.
```
