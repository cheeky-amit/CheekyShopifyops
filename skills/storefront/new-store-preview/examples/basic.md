# Example — new-store preview intake

```
> You: "I want to start a coffee mug shop."

  Quick check first: these previews can only be claimed as brand-new
  stores — they can't be applied to one you already have. Are you
  starting a new brand?

> You: "Yes, brand new."

  Three quick questions to make the previews feel right:

  1) What exactly will you sell? (e.g., "handmade stoneware coffee mugs,
     mostly speckled glazes, with hand-painted details")

> You: "Handmade stoneware mugs, speckled glazes, hand-painted."

  2) Who's the customer? (e.g., "people in their late 20s and 30s in
     cities who care about supporting small artists")

> You: "Late 20s urban, into supporting small makers."

  3) What's the visual style? (mood, palette, references — e.g., "earthy
     and warm but not too rustic, modern with handmade vibes")

> You: "Earthy, warm, modern, no neon."

  Generating 3 storefront previews for:
    Product:    stoneware mugs, handmade, speckled glaze, hand-painted
    Audience:   urban 25-35, supports small makers
    Style:      earthy, warm, modern, no neon

  This usually takes about 3 minutes. The previews will appear here when
  ready. Each one comes with a signup link — clicking it creates a
  brand-new Shopify store with that theme and matching products
  pre-installed.

  Want a different direction while we wait? Just say so.

[the host renders the preview options once generation completes]
```

— under the hood —

It confirms the new-brand constraint, gathers the three descriptors, compresses them into the keyword form the generator expects, and kicks off generation. The host renders the previews inline when the run finishes.
