# Merchant profile schema (`merchant_ai.*`)

The merchant onboarding skills (`onboarding.first-run`, `onboarding.refresh-profile`) save a small profile per store under shop-level metafields in namespace `merchant_ai`. `_system.shop-context` reads them on every skill invocation.

This page documents the shape of those metafields, the rules that govern them, and the GraphQL operations to read, write, and delete them. Treat it as the contract between onboarding and every other skill.

## Storage

- **Owner type:** `Shop` (shop-level, not product / customer / order).
- **Namespace:** `merchant_ai`.
- **Type:** `json` for all four keys. The metafield value is a JSON-stringified object; on read, parse with safe JSON parsing.
- **Visibility:** by default, app-scoped. Whether to mark these as `private` depends on the app permissions setup — see ADR 0006.

## Keys

| Key | Purpose |
|---|---|
| `operator_profile` | Who's running the store and how they want Claude to behave. |
| `store_profile` | Stage, model, discipline signals, last-refreshed timestamp. |
| `rituals` | Recurring asks like a Monday brief. |
| `onboarding_state` | Completion / decline timestamps and version. |

## Shapes

### `merchant_ai.operator_profile`

```jsonc
{
  "name":           "<string>",            // free-form, e.g. "Operator A"
  "role":           "<string>",            // free-form, but conventional values include
                                           //   founder | operations | marketing | finance | cx | merchandiser
  "voice":          "terse" | "conversational" | "explained",
  "write_defaults": "a" | "b" | "c"
}
```

`write_defaults` codes:
- `a` — always show a preview and wait for "yes" even on small changes.
- `b` — show a preview, but if the merchant says "do it" assume yes.
- `c` — for big or bulk changes, always confirm. For small ones, just do it.

Validation:
- `voice` MUST be one of the three enum values. On write, validate; on read, treat unknown values as `conversational`.
- `write_defaults` MUST be `a`, `b`, or `c`. On read, treat unknown values as `a`.
- `name` and `role` are free-form. The skill should not embed business logic in `role`; downstream skills read the `category` of an active skill, not `role`, when they need to disambiguate.

### `merchant_ai.store_profile`

```jsonc
{
  "stage":          "just-launched" | "under-10k-mo" | "10k-100k" | "100k-1m" | "1m+",
  "model":          "<string>",        // one-line summary, e.g. "DTC apparel, US-only"
  "discipline":     { /* object */ },  // signal flags — see below
  "last_refreshed": "<iso-8601 string>"
}
```

`discipline` is an object — content shape is intentionally loose so we can add signals without bumping the schema version. Conventional flags:
- `tracks_inventory: bool` — true if products are tracked.
- `single_location: bool` — true if there's one location.
- `has_collections: bool` — true if any collections exist.
- (additional signals welcome — keep all values primitive: bool / number / string)

Validation:
- `stage` MUST be one of the five enum values. On read, treat unknown values as null and ask the merchant to refresh.
- `last_refreshed` MUST be an ISO-8601 timestamp.
- `model` is **display-only**. It's a free-form one-line summary intended for show-back to the merchant in the profile preview and weekly brief. Other skills MUST NOT branch on `model` content; if a downstream skill needs structured signal (e.g. country, currency, channel), read it from `discipline` (extend with new flags as needed) or from `get-shop-info`.

### `merchant_ai.rituals`

```jsonc
{
  "monday_brief":     true | false,
  "weekly_brief_day": "Monday" | "Tuesday" | "Wednesday" | "Thursday" | "Friday" | "Saturday" | "Sunday" | null
}
```

Validation:
- If `monday_brief` is true, `weekly_brief_day` should be `"Monday"`.
- If `monday_brief` is false, `weekly_brief_day` may be a different day or `null`.
- Unknown day strings → treat as `null` on read.

### `merchant_ai.onboarding_state`

```jsonc
{
  "completed_at": "<iso-8601 string>" | null,
  "declined_at":  "<iso-8601 string>" | null,
  "version":      "<string>"   // schema version, e.g. "1.0"
}
```

Validation:
- At least one of `completed_at` / `declined_at` is set whenever this metafield exists. (If both are null, treat as "not yet onboarded" identically to the metafield being absent.)
- `version` is a free-form string; current schema is `"1.0"`. Future versions are reverse-compatible — readers that see a newer version SHOULD still consume known fields and ignore unknown ones.

## Failure modes

| Mode | Behavior |
|---|---|
| Write blocked (host or permissions) | First-run / refresh fall back to in-conversation profile only. Tell the merchant; do not retry silently. |
| Switch-shop | Profile is store-scoped; `_system.shop-context` re-reads on the new store. Treat the new store as "no profile yet" if the metafields are absent there. |
| `last_refreshed` > 6 months old | `needs_onboarding` stays false; caller may surface a one-line "want to refresh your profile?" hint. Do not force. |
| Declined within 30 days | `onboarding_skipped: true`; do not auto-trigger first-run. Use defaults. |
| Declined > 30 days ago | `needs_onboarding: true`; offer onboarding again, but don't force — merchant can skip again. |
| Malformed JSON in one metafield | Treat that metafield as null. Other metafields still apply. Do not error the entire shop-context call. |
| Unknown enum value | Coerce to a safe default and continue (see per-field rules above). |

## How to read

One GraphQL query, executed via `_system.graphql-helper`:

```graphql
query ShopProfile {
  shop {
    id
    name
    primaryDomain { url }
    email
    currencyCode
    ianaTimezone
    billingAddress { country }
    plan { displayName }
    metafield_operator: metafield(namespace: "merchant_ai", key: "operator_profile") { value }
    metafield_store:    metafield(namespace: "merchant_ai", key: "store_profile") { value }
    metafield_rituals:  metafield(namespace: "merchant_ai", key: "rituals") { value }
    metafield_onboarding: metafield(namespace: "merchant_ai", key: "onboarding_state") { value }
  }
}
```

Each `value` is a JSON string (or null). Parse safely; on parse failure treat that field as null.

## How to write

Validate via `_system.graphql-helper` then call `metafieldsSet`. Upsert any subset of keys per call:

```graphql
mutation SaveProfile($metafields: [MetafieldsSetInput!]!) {
  metafieldsSet(metafields: $metafields) {
    metafields { id namespace key value }
    userErrors { field message code }
  }
}
```

Variables (example, all four keys):

```jsonc
{
  "metafields": [
    {
      "ownerId":   "gid://shopify/Shop/000",
      "namespace": "merchant_ai",
      "key":       "operator_profile",
      "type":      "json",
      "value":     "{\"name\":\"Operator A\",\"role\":\"founder\",\"voice\":\"conversational\",\"write_defaults\":\"a\"}"
    },
    {
      "ownerId":   "gid://shopify/Shop/000",
      "namespace": "merchant_ai",
      "key":       "store_profile",
      "type":      "json",
      "value":     "{\"stage\":\"10k-100k\",\"model\":\"DTC apparel, US-only\",\"discipline\":{\"tracks_inventory\":true,\"single_location\":true,\"has_collections\":true},\"last_refreshed\":\"2026-05-05T14:00:00Z\"}"
    },
    {
      "ownerId":   "gid://shopify/Shop/000",
      "namespace": "merchant_ai",
      "key":       "rituals",
      "type":      "json",
      "value":     "{\"monday_brief\":true,\"weekly_brief_day\":\"Monday\"}"
    },
    {
      "ownerId":   "gid://shopify/Shop/000",
      "namespace": "merchant_ai",
      "key":       "onboarding_state",
      "type":      "json",
      "value":     "{\"completed_at\":\"2026-05-05T14:00:00Z\",\"declined_at\":null,\"version\":\"1.0\"}"
    }
  ]
}
```

The host will prompt the merchant to confirm the mutation. That's a second confirmation on top of the skill's own preview/confirm step.

## How to delete (forget)

Validate and call `metafieldsDelete` for each of the four keys:

```graphql
mutation ForgetProfile($metafields: [MetafieldIdentifierInput!]!) {
  metafieldsDelete(metafields: $metafields) {
    deletedMetafields { ownerId namespace key }
    userErrors { field message code }
  }
}
```

Variables (delete all four):

```jsonc
{
  "metafields": [
    { "ownerId": "gid://shopify/Shop/000", "namespace": "merchant_ai", "key": "operator_profile" },
    { "ownerId": "gid://shopify/Shop/000", "namespace": "merchant_ai", "key": "store_profile" },
    { "ownerId": "gid://shopify/Shop/000", "namespace": "merchant_ai", "key": "rituals" },
    { "ownerId": "gid://shopify/Shop/000", "namespace": "merchant_ai", "key": "onboarding_state" }
  ]
}
```

Like writes, the host prompts for mutation confirmation.

## Schema versioning

`onboarding_state.version` lets future onboarding flows evolve without breaking existing stores:

- New optional fields → keep existing version. Readers ignore unknown fields.
- Removed fields → bump major (e.g. `1.0` → `2.0`). Provide a migration on first refresh after upgrade.
- Renamed fields → bump major; first-run on a new store starts fresh, refresh-profile migrates.

Current version: `1.0`.

## See also

- [`docs/contributors/decisions/0006-onboarding-and-profile.md`](decisions/0006-onboarding-and-profile.md) — the ADR.
- `skills/_system/shop-context/` — primitive that reads this profile on every call.
- `skills/onboarding/first-run/` — primary writer.
- `skills/onboarding/refresh-profile/` — editor + delete path.
