# Example — paginated read of all draft products

```
Caller: catalog.product-status-audit
Need:   all DRAFT products in the store
Cap:    500

1. cursor = null, records = []
2. search_products(search_query: "status:draft", first: 50, after: null)
   → 50 records, hasNextPage: true, endCursor: "..."
   → records.length: 50
3. cursor = endCursor; progress_fn(50, true) → "loading… 50 so far"
4. search_products(... after: cursor)
   → 50 records, hasNextPage: true, endCursor: "..."
   → records.length: 100
5. (... continues)
6. After page 6: 312 records, hasNextPage: false
   → return { records: [...312 items],
              total_fetched: 312,
              hit_cap: false,
              hit_end: true,
              last_cursor: "..." }

# If instead the store had 600 drafts:
6'. After page 10: 500 records, records.length >= cap
   → return { records: [...first 500 items],
              total_fetched: 500,
              hit_cap: true,
              hit_end: false,
              last_cursor: "..." }
   → Caller surfaces to merchant: "I scanned the first 500 of ~600 drafts — narrow the cohort to see the rest."
```
