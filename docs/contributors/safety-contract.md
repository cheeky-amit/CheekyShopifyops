# Safety contract

Every skill in this repo must follow this contract. The `_system/safe-write` skill operationalizes it; domain skills compose it.

## The five steps

For any write — single or bulk:

### 1. Read current state

Always read first. Even for "obvious" writes. Two reasons:
- The merchant's mental model may be stale.
- We need a baseline for the diff and the rollback log.

```
Step 1 always begins:  "Let me check the current state…"
```

### 2. Show the diff

Compose a human-readable preview. The merchant sees:

- What will change (count of records, names where helpful)
- The current value → proposed value, per record
- A one-line reason per record (or per cohort)
- Estimated blast radius (e.g., "12 of your 240 products")

Never show the merchant raw GraphQL or tool names.

### 3. Confirm

Prompt the merchant explicitly. Wait for "yes / proceed / confirm" — accept variations but not silence and not "ok" alone if the change is bulk. For bulk writes (`risk: bulk-write`), require unambiguous confirmation.

If the merchant says "modify", iterate. If they say "no" / "stop", abort and write nothing.

### 4. Execute with compare-and-swap where supported

| Tool | CAS support | How |
|---|---|---|
| `set-inventory` | ✅ | Pass `compareQuantity` = quantity from `get-inventory-levels`. Update fails safely if stock changed. |
| `update-product` | ❌ | No version field exposed. Mitigate by re-reading immediately before the write and comparing the fields you're changing. |
| `bulk-update-product-status` | ❌ | Re-read each product's status immediately before; abort the cohort if mid-flight drift detected. |
| `update-collection` | ❌ | Same mitigation as `update-product`. |
| `create-discount` | n/a | Creation, not mutation. |
| `graphql_mutation` | varies | Many GraphQL mutations support optimistic concurrency via `updatedAt` or version stamps; check the input type. |

Use CAS where it exists. Where it doesn't, document the read-before-write window and keep it short.

### 5. Log before/after, document rollback

Every write skill produces a structured run log:

```
Run log: catalog.product-status-audit @ 2026-01-15T14:32:11Z
Store: example-shop.myshopify.com
Changes: 12

  gid://shopify/Product/000 [Product A]   ACTIVE → ARCHIVED   (no sales 90d, 0 stock)
  gid://shopify/Product/001 [Product B]   DRAFT  → ACTIVE     (in active collection, 24 stock)
  …
```

Skills surface this to the merchant as "save this somewhere if you want to undo." The skill itself does not persist the log to disk in this repo (no `.claude/state` files). The merchant's client may capture it.

Every write skill's `safety.rollback` field describes the inverse operation. Examples:

- `set-inventory` → re-call with the previous quantity and reason `correction`.
- `bulk-update-product-status` → re-call with the inverse `productIds` → previous-status mapping.
- `update-product` (variant prices) → re-call `update-product` with the previous prices.
- `update-collection` (rules) → re-call with the previous `ruleSet`.
- `create-discount` → cannot be "uncreated"; rollback is to set `endsAt` to now via `graphql_mutation` on `discountCodeBasic`.

## Blocked operations

The Shopify MCP refuses these. Skills must NOT attempt them. They are documented as out-of-scope and merchants are directed to the Shopify admin UI.

| Operation | Reason | Where to direct merchants |
|---|---|---|
| Refunds | MCP block | Orders → [order] → Refund in Shopify admin |
| Gift card writes | MCP block | Products → Gift cards in Shopify admin |
| Staff member management | MCP block | Settings → Users in Shopify admin |
| Theme deletion | MCP block | Online store → Themes in Shopify admin |
| Theme publishing | MCP block | Online store → Themes → Publish in Shopify admin |
| Live (MAIN) theme file writes | MCP block | Edit on a duplicate, then publish via admin |

If a merchant asks for one of these, the skill (or a helper skill) should:
1. Acknowledge the request in plain language.
2. Explain it's not safe to do via this surface.
3. Point to the exact Shopify admin path.

## Honesty when something is missing

If a workflow needs a capability the MCP doesn't expose, skills must say so. They must not:
- Invent a tool that doesn't exist.
- Silently fall back to a less-safe path without telling the merchant.
- Skip validation steps in the GraphQL workflow.

The skill's output should include "I can do X via the MCP, but for Y you'd need to do that step in Shopify admin (or another tool) — here's where."

## Privacy

Even during verification, real store data is treated as privileged:

- Read operations are fine.
- Their **outputs** never get written to repo files.
- Examples in `examples/` use generic placeholders only (see `docs/contributors/conventions.md` placeholder table).
- Commit messages, PR titles, issue bodies must not include real store data.

If a skill author accidentally captures real data in a draft, they search-and-replace before committing. Reviewers grep PRs for store-shaped strings (`*.myshopify.com`, `gid://shopify/Product/[1-9]`, `@gmail.com`, etc.) before merging.
