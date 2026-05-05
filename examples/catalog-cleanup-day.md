# Catalog cleanup day

Quarterly housekeeping for your products. Four skills chained: missing-content audit, tag cleanup, duplicate detector, status audit.

---

> You: "Catalog cleanup. Show me what needs attention."

Running a read-only sweep across your catalog…

**Missing content** (8 products)
- 3 products have no description.
- 4 products are missing alt text on their images.
- 1 product has no images at all.

**Tag taxonomy** (looks messy)
- 14 tag pairs that look like duplicates (e.g. "summer" vs "Summer", "tee" vs "t-shirt").
- 6 tags used on only 1 product (likely typos).

**Duplicates** (5 likely pairs)
- Product A and Product A v2 — same vendor, near-identical title.
- Product B and Product B-old — Product B-old has 0 sales in 90 days.
- (3 more)

**Status drift** (28 products)
- 28 are ACTIVE but have 0 stock and no sales in the last 30 days.

Where do you want to start?

> You: "tags first"

Looking at the 14 duplicate-looking tag pairs. Here's the proposed cleanup:

| Keep | Merge in | Affects |
|---|---|---|
| summer | Summer | 41 products |
| t-shirt | tee, T-Shirt | 23 products |
| sale-2025 | Sale-2025, sale 2025 | 18 products |
| (11 more) | | |

To apply all 14 merges, type: **yes — merge these 14**.

To skip some, tell me which ones to keep separate.

> You: "yes — merge these 14"

Applying tag merge across affected products… done.

Run log saved. To undo, ask "undo the last tag cleanup."

Next: duplicates?

> You: "yes"

5 likely duplicate pairs. For each, I'm suggesting which one to keep based on sales and inventory:

| Keep | Archive | Why |
|---|---|---|
| Product A | Product A v2 | A v2 has 0 sales in 90 days, 0 stock |
| Product B | Product B-old | B-old has 0 sales in 90 days |
| Product D | Product D-draft | D-draft is in DRAFT status |
| Product E | Product E (copy) | (copy) is unpublished |
| Product F | Product F-test | F-test has "test" in title |

To archive all 5, type: **yes — archive these 5**.

> You: "yes — archive these 5"

Done. Run log saved. To undo, ask "undo the last archive."

> You: "let's leave the status drift for next month"

No problem. The 28 ACTIVE-but-out-of-stock products stay as-is. Ask "audit my products" next month and I'll surface them again.

Anything else today?

> You: "no, that's good"

Cleanup summary saved.

---

— under the hood —

Four skills: `catalog.missing-content`, `catalog.tag-cleanup`, `catalog.duplicate-detector`, `catalog.product-status-audit`. The first surfaces issues read-only; the others propose preview-then-confirm changes. Bulk changes here always required the count-confirm form ("yes — change these 14"). Every applied change is logged with a reverse path.
