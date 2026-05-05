# Example — first-run, merchant skips

```
Merchant: "set me up"

Step 0 — load context
  shop-context: needs_onboarding: true, all profile metafields null.

Step 1 — auto-discovery
  ...short read-back... (as in basic.md)

Step 2 — questions
  Claude asks the four questions.

Merchant: "skip"

Step 4 (skip handling) — save declined marker only
  Construct: onboarding_state = {
    completed_at: null,
    declined_at:  2026-05-05T14:02:00Z,
    version:      "1.0"
  }

  → graphql_helper validates SaveDecline mutation
  → graphql_mutation metafieldsSet
     - One metafield only: ownerId=<Shop GID>, namespace=merchant_ai,
       key=onboarding_state, type=json, value=<JSON-stringified above>
  Host prompts; merchant approves.

  Response:
    metafields: [{ id: gid://shopify/Metafield/000, namespace: merchant_ai,
                   key: onboarding_state, value: "..." }]
    userErrors: []

Final reply
  Claude: "OK — using defaults (always preview, always confirm).
           You can run 'set me up' any time."

Effect on subsequent skill calls
  shop-context now returns:
    onboarding_state:   { completed_at: null,
                          declined_at:  2026-05-05T14:02:00Z,
                          version:      "1.0" }
    needs_onboarding:   false
    onboarding_skipped: true

  Domain skills proceed with defaults: voice='conversational',
  write_defaults='a' (always preview, always confirm).
  Onboarding will not auto-trigger again for 30 days.
```
