# Example — group has more than 50 products

```
> You: "Set everything in the Spring 2025 collection to draft."

  example-shop · bulk status sweep

  Group:           collection "Spring 2025"
  Match count:     87
  Already draft:   4
  Will change:     83
  Target status:   draft

  Heads up: 83 products need to change, but a single run is capped at 50.
  This keeps the blast radius small — bigger sweeps need to run again with a
  narrower group.

  Two ways forward:
    1) Do the first 50 now (alphabetical by title), then re-run for the
       remaining 33.
    2) Narrow the group, e.g. "Spring 2025, Vendor A only," and run separate
       sweeps per slice.

  What do you want to do?

> You: "Just do it in two passes."

  example-shop · bulk status sweep · pass 1 of 2

  First 50 products in "Spring 2025" (alphabetical by title)

   1.  Product A
   …
  50.  Product AX

  To apply pass 1, type exactly:
    yes — set these 50 to draft

(... after you confirm, the 50 apply; the run log saves; the skill reminds
     you to run pass 2 for the remaining 33 ...)
```

The skill never silently truncates. Surfacing the overflow lets you decide whether to run in passes, slice the group further, or stop entirely.

— under the hood —
  Same flow as a normal sweep, but if the count is above 50, the run pauses
  and asks how you want to handle the overflow before any write happens.
