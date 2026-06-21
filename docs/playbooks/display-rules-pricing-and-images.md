---
name: display-rules-pricing-and-images
description: "How to display prices (USD \"under $X\") and product images (never crop) on the MesmerDesign site"
metadata: 
  node_type: memory
  type: feedback
  originSessionId: 3fd94845-ea5f-461f-9af0-7edcd9d7210a
---

Two hard display rules for MesmerDesign content (set by the owner 2026-06-19):

**1. Pricing — show USD "Under $X", never an exact price, never €.**
- `data/products.yaml` prices are the **EU price including 20% VAT**. Most buyers are US (no VAT) so they pay ~20% less.
- Conversion is automatic via `layouts/partials/func/us-price.html` using `[pricing]` in `config/_default/params.toml` (`usMultiplier = 0.9` ≈ remove VAT + EUR→USD; `roundTo = 5`). It returns a rounded-UP ceiling: e.g. 21,77→$20, 29,03→$30, 23,17→$25, 33,53→$35, 49→$45, 64,34→$60.
- Product cards/grids and ItemList schema already use it (USD). **In blog prose, always write "under $X" (rounded up to a $5 ceiling), never an exact figure and never euros.** Disclosure notes prices are approximate US (VAT excluded); Etsy shows exact total at checkout.
- **Why:** majority of clients are US-based; EU listed prices include VAT they don't pay, and the owner doesn't want exact prices that can go stale.

**2. Product images — NEVER crop. Always show the full image.**
- Use Hugo `.Fit` (fits within bounds, preserves whole image) — **never `.Fill`** (which crops). Applies to post hero, post cards, related cards, everywhere.
- CSS: card images use `object-fit: contain` on a soft tinted panel (`.card .card-img`), hero uses `.post-hero` (max-height, centered). Product mug designs get cut off with cropping, which the owner explicitly rejected — twice.
- **How to apply:** any new image-display template must use `.Fit` + `object-fit: contain`, never `.Fill`/`cover` on product imagery.

See [[blog-post-playbook]] and [[mesmerdesign-blog-project]].
