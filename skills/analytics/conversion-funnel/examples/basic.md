# Example — conversion funnel

```
> You: "Conversion funnel last 30 days."

  example-shop · conversion funnel · last 30 days

                              Sessions    Step rate    Cumulative
    Sessions                     4,210
    → cart add                     840      20%          20%
    → reached checkout             420      50%          10%
    → completed checkout           210      50%           5%

  Overall conversion: 5.0%
  Drop-off pattern:   half drop off at cart → checkout (often price or
                      shipping shock); the other half drop off at checkout →
                      complete (often payment friction).

  Worth investigating: shipping costs, payment options.

— under the hood —
  Pulls session, cart-add, checkout-reached, and checkout-completed counts
  for the window, computes the drop-off at each step, and adds a one-line
  read on the worst step.
```

Split by device — usually the most actionable view:

```
> You: "Funnel by device last 30 days."

  example-shop · conversion funnel · last 30 days · split by device

    desktop                       Sessions    Step    Cumulative
      Sessions                       1,540
      → cart add                       420     27%     27%
      → reached checkout               240     57%     16%
      → completed checkout             140     58%      9.1%

    mobile                        Sessions    Step    Cumulative
      Sessions                       2,520
      → cart add                       380     15%     15%
      → reached checkout               160     42%      6.3%
      → completed checkout              64     40%      2.5%

    tablet                        Sessions    Step    Cumulative
      Sessions                         150
      → cart add                        40     27%     27%
      → reached checkout                20     50%     13%
      → completed checkout               6     30%      4.0%

  Mobile is your weak point — converting at less than a third of desktop's
  rate despite having most of the traffic. Worth checking mobile checkout
  experience: form length, payment options visible without scrolling, the
  shipping cost reveal.
```
