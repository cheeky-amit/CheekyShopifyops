# Shopify MCP — tool inventory

What the connected Shopify Admin MCP exposes today. Skills in this repo target this surface only.

> Verified by directly querying a connected MCP. Last refresh: see git log on this file.

## Direct tools (typed schemas)

### Shop context

| Tool | Purpose | Notes |
|---|---|---|
| `get-shop-info` | Read store name, domain, plan, currency, timezone, country | Used by every skill that needs context (plan-tier capabilities, currency for prices, timezone for scheduling) |
| `switch-shop` | Switch to a different connected store | Revokes current token; next call re-auths |

### Catalog — products

| Tool | Purpose | Notes |
|---|---|---|
| `search_products` | List/search products | Field filters: `title`, `vendor`, `product_type`, `handle`, `sku`, `barcode`, `tag`, `status`, `price` (range), `inventory_total` (range), `created_at`, `updated_at`, `published_at`, `gift_card`, `bundles`, `is_price_reduced`, `out_of_stock_somewhere`. Cap 50/call. Cursor-paginated. |
| `get-product` | Get one product by GID | Returns variants, images, tags, inventory, status |
| `create-product` | Create a product | Variants require explicit `options` array. To enable inventory tracking, set `inventoryItem.tracked: true` |
| `update-product` | Update title/description/status/images/variants | Use `removeMediaIds` + `images` together to swap images |
| `bulk-update-product-status` | Set status across many products | `productIds` (max 50) OR `collectionId` (first 50 only). Status: `ACTIVE` / `DRAFT` / `ARCHIVED`. Updates are individual — partial failures possible |

### Catalog — collections

| Tool | Purpose | Notes |
|---|---|---|
| `search_collections` | List/search collections | Filters: `title`, `handle`, `collection_type` (`custom`\|`smart`), `published_status`, `product_id`. Cap 50/call |
| `get-collection` | Get one collection by GID | Returns products, rules, image |
| `create-collection` | Create manual or smart collection | `productIds` and `ruleSet` are mutually exclusive |
| `update-collection` | Update title/description/image/rules/sortOrder | Passing `ruleSet` replaces all rules |
| `add-to-collection` | Add products to existing collection | Append-only |

### Inventory

| Tool | Purpose | Notes |
|---|---|---|
| `get-inventory-levels` | Get inventory across all variants/locations of a product | Returns `inventoryItemId` + `locationId` pairs |
| `set-inventory` | Set quantity at one (item × location) | **Compare-and-swap supported** via `compareQuantity` — required by this repo's safety contract. `reason` enum: `correction`, `received`, `restock`, `damaged`, `cycle_count_available`, `shrinkage`, etc. |

### Orders (read-only via direct tools)

| Tool | Purpose | Notes |
|---|---|---|
| `list-orders` | Recent orders summary | Free-text `query` accepted (Shopify order search syntax) |
| `get-order` | One order by GID or order number/name | Includes line items, fulfillment status, shipping, tracking |

> **No direct order mutation tools.** Refunds, cancellations, fulfillments → use `graphql_mutation` (subject to MCP block list). Refunds and gift card writes are **blocked**.

### Customers (read-only via direct tools)

| Tool | Purpose | Notes |
|---|---|---|
| `list-customers` | List/search customers | Filters: `first_name`, `last_name`, `email`, `phone`, `country`, `state`, `accepts_marketing`, `email_marketing_state`, `tag`, `orders_count` (range), `total_spent` (range), `created_at`, `updated_at`. **Name lookups must use `first_name:X OR last_name:X`** — bare strings match notes/tags/addresses |

> **No direct customer mutation tools.** Profile edits, marketing consent updates, segment writes → `graphql_mutation`.

### Promotions

| Tool | Purpose | Notes |
|---|---|---|
| `create-discount` | Percentage discount code | Scope by `collectionId` or `customerSegments`. Min purchase OR min quantity (mutually exclusive). `startsAt` / `endsAt` ISO 8601. Other discount shapes (fixed amount, BXGY, free shipping) require `graphql_mutation` |

### Analytics

| Tool | Purpose | Notes |
|---|---|---|
| `run-analytics-query` | ShopifyQL query | `FROM <table> SHOW <metrics> [GROUP BY] [WHERE] [TIMESERIES] [SINCE/UNTIL] [COMPARE TO]`. Tables: `sales`, `orders`, `products`, `inventory`, `customers`, `sessions`, `fulfillments`. Returns tabular data plus auto-chart |

### Storefront generation (new stores only)

| Tool | Purpose | Notes |
|---|---|---|
| `get-new-store-previews` | Generate up to 3 storefront previews | For new stores only — cannot be applied to an existing store. ~3 min generation time |
| `get-storefront-generation` | Poll preview generation status | Used by widget; rarely called directly |
| `claim-storefront-preview` | Signal preview claim | Only invoked by widget |

### GraphQL — universal escape hatch

| Tool | Purpose | Notes |
|---|---|---|
| `graphql_schema` | Inspect types/fields | **Required** before mutations. Start with `Mutation`, `QueryRoot`, or a type name |
| `search_docs_chunks` | Search shopify.dev | Use after schema lookup if you need usage examples |
| `validate_graphql_codeblocks` | Pre-flight validate | **Required** before `graphql_query` / `graphql_mutation` per repo contract |
| `graphql_query` | Read-only Admin GraphQL | Pagination via `pageInfo { hasNextPage endCursor }` + `after` |
| `graphql_mutation` | Mutate via Admin GraphQL | Subject to host-level confirm prompt. Some mutations blocked (see below) |

### Blocked mutations (don't attempt)

The MCP refuses these. Skills must document them as out-of-scope and direct merchants to Shopify admin:

- Refunds (any `refund*` mutation)
- Gift card writes
- Staff member management
- Theme deletion
- Theme publishing
- Theme file writes targeting the live (MAIN) theme

Theme file writes to **unpublished** themes are allowed.

## What's missing (for skill authors)

Things merchants might ask for that the Shopify MCP **does not** support today. Skills must surface this honestly rather than invent:

- Sending email or SMS (no transactional/marketing send tools — this is intentional; skills compose with email MCPs separately, see `meta/wiring`)
- Third-party fulfillment provider actions (no 3PL integration)
- Accounting / bookkeeping exports (no QuickBooks/Xero)
- App-installed surfaces (apps' private data is not exposed)
- Subscriptions billing (would need the subscription app's own API)

If you find a gap, file an issue tagged `mcp-gap`.
