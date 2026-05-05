# Detect duplicate products

Find likely duplicates by SKU, barcode, near-identical title, or auto-suffixed handle.

> "Find duplicate products."
> "Duplicate SKUs."
> "Are there duplicate listings?"

## Signals

- **SKU** — same SKU across products.
- **Barcode** — same barcode across products.
- **Title-fuzzy** — near-identical titles after normalization (case, punctuation, whitespace).
- **Handle** — auto-suffixed handles (e.g. `product-d` and `product-d-1`) typically signal an import that didn't deduplicate.

## Won't auto-resolve

The skill won't merge or archive duplicates on its own — too easy to drop the wrong one. It surfaces clusters; the merchant chooses what to do.
