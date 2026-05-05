# ADR 0005 — Skill inventory (v1)

**Status:** Accepted (v1 seed)

## Decision

Ship 35 skills in v1, organized as below. The inventory is reduced from the suggested ~70 by cutting things the MCP cannot safely or honestly support, and by consolidating overlapping ideas into composable primitives.

## v1 inventory

### Primitives — 5 (internal)

| ID | Purpose |
|---|---|
| `_system.shop-context` | Standard shop profile load (currency, plan, timezone, country). Almost every other skill calls this first. |
| `_system.graphql-helper` | Wraps `graphql_schema → search_docs_chunks → validate_graphql_codeblocks → execute`. |
| `_system.safe-write` | Preview / diff / confirm / before-after log / rollback scaffolding. Required by every write skill. |
| `_system.cohort-builder` | Reusable patterns for product/customer cohorts using Shopify search syntax. |
| `_system.data-extraction` | Pagination-aware bulk read with the 50-per-call cap baked in. |

### Catalog — 6

| ID | Purpose |
|---|---|
| `catalog.product-status-audit` | Find products in the wrong status (live without stock, draft with sales, archived but linked). |
| `catalog.missing-content` | Find products missing description, image, alt text, vendor, type, or tags. |
| `catalog.tag-cleanup` | Audit and consolidate tag taxonomy. |
| `catalog.collection-coverage` | Find products not in any collection / orphaned collections. |
| `catalog.duplicate-detector` | Detect likely duplicate products (similar titles, same SKU, same barcode). |
| `catalog.bulk-status-sweep` | Plan + execute archiving of slow/no-sales products. |

### Inventory — 4

| ID | Purpose |
|---|---|
| `inventory.snapshot` | Pull current inventory across all variants/locations. |
| `inventory.low-stock-alert` | Identify products below thresholds (per-product or global). |
| `inventory.cycle-count` | Reconcile counted stock vs system using `compareQuantity`. |
| `inventory.receive-shipment` | Add received quantities with `reason: 'received'`, logged. |

### Orders — 3 (read-only; refunds blocked)

| ID | Purpose |
|---|---|
| `orders.daily-pulse` | Yesterday/today summary: count, AOV, fulfillment status. |
| `orders.fulfillment-watchlist` | Stuck unfulfilled orders by age. |
| `orders.search` | Find orders by status, date, customer, value. |

### Customers — 3 (read-only via direct tools; consent updates via GraphQL)

| ID | Purpose |
|---|---|
| `customers.cohort-snapshot` | VIP / new / lapsed cohort profile. |
| `customers.high-value-list` | Top spenders / repeat buyers. |
| `customers.subscriber-audit` | Marketing consent state across the base. |

### Promotions — 3

| ID | Purpose |
|---|---|
| `promotions.discount-create` | Plan + create percentage discount with safe scoping. |
| `promotions.discount-audit` | List active discounts, expirations, redemption (via GraphQL `discountNodes`). |
| `promotions.collection-sale` | Create discount scoped to a collection with start/end dates. |

### Analytics — 5

| ID | Purpose |
|---|---|
| `analytics.daily-pulse` | Sales/orders/sessions/AOV with comparisons. |
| `analytics.product-performance` | Top/bottom by gross sales, sell-through rate. |
| `analytics.referrer-mix` | Sales/sessions by referrer source/name. |
| `analytics.conversion-funnel` | Sessions → cart → checkout → completion. |
| `analytics.cohort-retention` | Returning vs new customer rate over time. |

### Retention — 2

| ID | Purpose |
|---|---|
| `retention.win-back-list` | Lapsed customer cohort with last-order date. |
| `retention.first-order-anniversary` | Customers near their first-order anniversary (cohort, for marketing). |

### Storefront — 2

| ID | Purpose |
|---|---|
| `storefront.new-store-preview` | Wraps `get-new-store-previews` with a merchant-friendly intake. |
| `storefront.shop-snapshot` | Shop profile + plan/limits awareness (composes `_system.shop-context`). |

### Reporting — 1

| ID | Purpose |
|---|---|
| `reporting.weekly-merchant-brief` | End-of-week summary: sales, top movers, low stock, stuck orders, anomalies. Composite. |

### Meta — 1

| ID | Purpose |
|---|---|
| `meta.wiring` | How to add other MCPs to this project, or drop these skills into another project that already has other MCPs. Documentation-only. |

**Total: 35 skills.**

## Cuts (from the suggested ~70) and rationale

| Cut | Reason |
|---|---|
| Refunds workflow | MCP-blocked. Direct merchants to Shopify admin. |
| Gift card management | MCP-blocked. |
| Theme publishing / live theme writes | MCP-blocked. |
| Staff / permissions management | MCP-blocked. |
| Subscriptions billing workflows | Requires the subscription app's own API; out of MCP scope. |
| Email / SMS sending | No transactional/marketing send tools in Shopify MCP. Composes with email MCPs separately — see `meta.wiring`. |
| Tax / legal / compliance integrations | Most need external systems (Avalara, accountants); out of scope. |
| Per-role "founder dashboard" / "CFO board pack" / "CMO mix report" skills | Replaced by `reporting.weekly-merchant-brief` + analytics primitives. Roles live in frontmatter, not skill folders. |
| Three separate cohort skills (product / customer / order) | Replaced by `_system.cohort-builder` + cohort use embedded in domain skills. |
| Multiple discount-shape skills (BXGY, fixed amount, free ship) | v1 supports percentage via direct tool. Other shapes deferred to v2; would need full GraphQL workflow. |
| Multi-store consolidation skill | Deferred. `switch-shop` exists but cross-store reporting is a v2 concern. |
| B2B / Markets / pricelists | Deferred to v2 — deeper GraphQL surface, less common merchant ask. |
| Blog / page CMS skills | Deferred to v2 — possible via GraphQL but lower priority than catalog/inventory/orders. |
| Translations / i18n skills | Deferred to v2 — possible via GraphQL `translatableResources`. |

## Additions (vs the suggested list)

- **`_system.cohort-builder`** — was implicit; making it explicit prevents three role-flavored cohort skills from drifting apart.
- **`_system.data-extraction`** — pagination is mentioned in tool descriptions but it's worth a primitive so every skill handles the 50-cap correctly.
- **`storefront.shop-snapshot`** — small but every merchant asks "what's my plan / what currency am I in" — easy win.

## Phase ordering for v1 build

Build in this order so each phase unlocks the next:

| Phase | Skills | Why now |
|---|---|---|
| **0 — Reference** (this PR) | `catalog.product-status-audit` (full) | One end-to-end skill establishes the pattern reviewers verify against. |
| **1 — Primitives** | All 5 primitives | Required by everything below. |
| **2 — Read-mostly domain** | All 3 orders, all 3 customers, all 5 analytics, `inventory.snapshot`, `inventory.low-stock-alert`, `catalog.missing-content`, `catalog.collection-coverage`, `catalog.duplicate-detector`, `storefront.shop-snapshot` | Lowest risk, fastest to verify. |
| **3 — Single-record writes** | `inventory.cycle-count`, `inventory.receive-shipment`, `promotions.discount-create`, `catalog.tag-cleanup` | Exercises `safe-write` per-record. |
| **4 — Bulk writes** | `catalog.bulk-status-sweep`, `promotions.collection-sale`, `promotions.discount-audit` | Highest-blast-radius skills last. |
| **5 — Composites + meta** | `reporting.weekly-merchant-brief`, `retention.win-back-list`, `retention.first-order-anniversary`, `storefront.new-store-preview`, `meta.wiring` | Compose lower-phase skills. |

Phase 0 is the **reference skill** for the checkpoint. Phases 1–5 are post-approval.

## When to revisit

After v1 ships and we have real merchant feedback:
- Promote v2 candidates (B2B, blogs, translations, BXGY discounts) per usage data.
- Cut underused v1 skills if they don't get invoked.
- Add new skills only when a real merchant asks twice.
