# Example — refresh, change role and write-defaults

```
Merchant: "update my profile — my role is ops manager now and stop confirming every small change"

Step 0 — load context
  shop-context returns the existing profile:
    operator: { name: "Operator A", role: "founder",
                voice: "conversational", write_defaults: "a" }
    store:    { stage: "10k-100k", model: "...", discipline: {...},
                last_refreshed: "2026-04-01T10:00:00Z" }
    rituals:  { monday_brief: true, weekly_brief_day: "Monday" }
    onboarding_state: { completed_at: "2026-04-01T10:00:00Z",
                        declined_at:  null, version: "1.0" }

Step 1 — show current profile
  Claude:
    "Here's what we have on file:
       Operator A · founder · conversational · always preview / always confirm
       Store: 10k-100k · DTC apparel, US-only · last refreshed 2026-04-01
       Rituals: Monday brief on
     What would you like to change?"

Merchant: "role: ops manager. write defaults: b."

Step 3 — apply edits to local copies
  operator.role            : founder              → ops manager
  operator.write_defaults  : a                    → b

Step 4 — preview
  Claude:
    "Changes:
       operator.role:           founder → ops manager
       operator.write_defaults: a → b   (preview, but if you say 'do it' assume yes)
     Save?
     Heads-up: Shopify will ask you to confirm the save once more after you say yes."

Merchant: "yes"

Step 5 — execute
  → graphql_helper validates SaveProfile mutation
  → graphql_mutation metafieldsSet (only operator_profile key changed; others omitted)
     ownerId  = <Shop GID>
     namespace= merchant_ai
     key      = operator_profile
     type     = json
     value    = { name: "Operator A", role: "ops manager",
                  voice: "conversational", write_defaults: "b" }
  Host prompts; merchant approves.

  Response:
    metafields: [{ id: gid://shopify/Metafield/000, namespace: merchant_ai,
                   key: operator_profile, value: "..." }]
    userErrors: []

Step 6 — confirm
  Claude:
    "Saved. Run log:
       Run: onboarding.refresh-profile @ 2026-05-05T14:10:00Z
       Store: example-shop
       Changes:
         operator.role:            founder → ops manager
         operator.write_defaults:  a → b
     Save this if you might want to undo."
```

# Example — forget my profile

```
Merchant: "forget my profile"

Step 0 — load context: profile present (any state).

Step 1 — confirm
  Claude:
    "This will delete everything we've saved about you and the store
     (operator, store profile, rituals, onboarding state).
     Are you sure? (yes / no)"

Merchant: "yes"

Step 2 — execute
  → graphql_helper validates ForgetProfile mutation
  → graphql_mutation metafieldsDelete with 4 entries:
     [{ ownerId: <Shop GID>, namespace: merchant_ai, key: operator_profile },
      { ownerId: <Shop GID>, namespace: merchant_ai, key: store_profile },
      { ownerId: <Shop GID>, namespace: merchant_ai, key: rituals },
      { ownerId: <Shop GID>, namespace: merchant_ai, key: onboarding_state }]
  Host prompts; merchant approves.

  Response:
    deletedMetafields: [
      { ownerId: <Shop GID>, namespace: merchant_ai, key: operator_profile },
      { ownerId: <Shop GID>, namespace: merchant_ai, key: store_profile },
      { ownerId: <Shop GID>, namespace: merchant_ai, key: rituals },
      { ownerId: <Shop GID>, namespace: merchant_ai, key: onboarding_state }
    ]
    userErrors: []

Step 3 — confirm
  Claude:
    "Profile deleted. Next ask will trigger first-run again
     — type 'skip' if you want to keep using defaults."
```
