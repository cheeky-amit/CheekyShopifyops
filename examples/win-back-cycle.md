# Win-back cycle

Build a re-engagement list of lapsed buyers with a discount code scoped to just them. Three skills chained: cohort snapshot, win-back list, discount create.

Note up front: this bundle builds the list and the code. **Sending the email itself needs another tool** (Klaviyo, Mailchimp, etc.). See [`../skills/meta/wiring/`](../skills/meta/wiring/) for how to combine.

---

> You: "Who are my lapsed VIPs?"

Pulling customers who used to buy regularly, haven't in a while, and are still on your email list.

**Cohort snapshot — lapsed VIPs**
- Definition: 2+ past orders, last order 6+ months ago, subscribed to email.
- Size: 84 customers.
- Average lifetime spend: $XXX.
- Average past order count: 3.4.
- Average lapse: 8 months.

**Top 10 by lifetime value:**

| # | Customer | Lifetime spend | Orders | Last bought |
|---|---|---|---|---|
| 1 | Customer A B. | $X,XXX | 6 | 7 months ago |
| 2 | Customer C D. | $X,XXX | 4 | 9 months ago |
| 3 | Customer E F. | $XXX | 3 | 6 months ago |
| 4 | Customer G H. | $XXX | 5 | 11 months ago |
| 5 | Customer I J. | $XXX | 3 | 7 months ago |
| 6 | Customer K L. | $XXX | 3 | 8 months ago |
| 7 | Customer M N. | $XXX | 4 | 10 months ago |
| 8 | Customer O P. | $XXX | 3 | 6 months ago |
| 9 | Customer Q R. | $XXX | 2 | 9 months ago |
| 10 | Customer S T. | $XXX | 4 | 12 months ago |

(Names abbreviated. Ask for one by number to see full details.)

> You: "Build a 15% code scoped to just them"

Here's the plan:

- **Code:** WELCOMEBACK15
- **Type:** 15% off, one-per-customer
- **Scope:** customer segment — "lapsed VIPs" (84 customers, the cohort above)
- **Active:** today → 30 days from today
- **Usage limit:** one redemption per customer

To create this code, type: **yes — create WELCOMEBACK15**.

> You: "yes — create WELCOMEBACK15"

Created. WELCOMEBACK15 is live and scoped to the 84-customer segment.

> You: "great. how do I email them?"

Sending the email itself needs another tool — this bundle doesn't send. The clean handoff is:

1. Export the cohort to your email tool (Klaviyo, Mailchimp, Resend, etc.).
2. Build the email there with WELCOMEBACK15 in the body.
3. Send from there.

If you want to wire that up — exporting the cohort automatically, or composing both bundles in one project — see [`../skills/meta/wiring/`](../skills/meta/wiring/) for the patterns.

---

— under the hood —

Three skills: `customers.cohort-snapshot` for the read, `retention.win-back-list` to define the lapsed-VIPs cohort and pull the top spenders, `promotions.discount-create` to issue the scoped code. The list-building is read-only; the code creation went through preview → confirm. The email send is intentionally not in scope — that's where the wiring guide takes over.
