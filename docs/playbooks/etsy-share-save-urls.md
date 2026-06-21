---
name: etsy-share-save-urls
description: Always use the shop-subdomain form mesmerdesing.etsy.com for Etsy links (Share & Save fee discount)
metadata:
  node_type: memory
  type: feedback
---

All MesmerDesign Etsy links must use the **shop-subdomain form** `https://mesmerdesing.etsy.com/...`, NOT `https://www.etsy.com/...`. Etsy's **Share & Save** program gives the shop a reduced transaction/Offsite-Ads fee when an off-site sale comes through the seller's own unique shop-subdomain link, so every outbound link from the blog should use it.

- Listing buy links: `https://mesmerdesing.etsy.com/listing/<id>/<slug>` (path is identical to the `www` form — just swap the host).
- Shop links: `https://mesmerdesing.etsy.com/shop/MesmerDesing` (host-swapped from `www`).
- **Exception — do NOT host-swap Etsy's own pages** like `https://www.etsy.com/legal/privacy/`. Those are Etsy corporate pages, not the shop; leave them on `www`.

**How to apply going forward:** when adding any product to `data/products.yaml` or writing any Etsy link in content/config, write the URL with the `mesmerdesing.etsy.com` host. If the user pastes a `www.etsy.com/listing/...` URL, convert the host before saving. The UTM partial (`layouts/partials/func/etsy-url.html`) appends tracking params to whatever URL it's given, so the subdomain works unchanged.

Applied repo-wide on 2026-06-21 (owner request): converted every shop/listing URL in `data/products.yaml`, `config/_default/params.toml`, contact/about/disclosure pages, and READMEs. See [[blog-post-playbook]] and [[mesmerdesign-blog-project]].
