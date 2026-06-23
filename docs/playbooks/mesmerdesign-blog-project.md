---
name: mesmerdesign-blog-project
description: What the MesmerDesign repo is — a Hugo SEO/GEO blog driving traffic to a new Etsy shop
metadata: 
  node_type: memory
  type: project
  originSessionId: 3fd94845-ea5f-461f-9af0-7edcd9d7210a
---

This repo is a **Hugo static-site blog** (deploys to GitHub Pages via `.github/workflows/deploy.yml`) whose purpose is to drive organic + AI-search traffic to the user's **brand-new Etsy print-on-demand shop "MesmerDesing"** (https://mesmerdesing.etsy.com/ — note the misspelling is the real shop slug). The shop had 0 sales / 0 reviews at launch (June 2026), so it gets no discovery on Etsy itself; the blog is the acquisition channel.

**Strategy:** human-style buyer-intent gift guides ("Christian gifts for mom", "business gifts", "funny gifts for coworkers") optimized for both **SEO and GEO** (AI answer engines). Designed to scale to **1000s of posts**.

Key architecture (also in README.md):
- Brand color **#750303** (deep maroon); warm off-white bg.
- Posts are page bundles under `content/blog/<category>/<slug>/index.md`; category `_index.md` = pillar page. Cross-cutting taxonomies: recipients/occasions/themes/product_types auto-generate landing pages.
- **All Etsy listings live in `data/products.yaml`** (keyed by id, prices as `"49,00"` no € sign); rendered via `product`/`product-grid`/`cta` shortcodes that auto-append UTM tags. URLs there are currently **placeholders pointing at the shop homepage** — must be replaced with real listing URLs, plus square images in `static/img/products/<id>.jpg`.
- GEO: per-page JSON-LD (BlogPosting/ItemList/FAQPage/BreadcrumbList/Organization+WebSite), `llms.txt`, robots.txt allows AI crawlers, answer-first `{{< answer >}}` blocks.
- Custom domain chosen (placeholder `www.mesmerdesign.com` in `static/CNAME`, `baseURL`, robots Sitemap line — not yet a real owned domain).

**Gotchas learned:** non-`.Inner` shortcodes must use plain `{{< x >}}` (NOT self-closing `/>}}`) in this Hugo version (v0.163 extended). JSON-LD `jsonify` inside `<script>` needs `| safeJS` or it double-escapes. Permalink token is `:slugorcontentbasename` (`:slugorfilename` removed).

User picked **Hugo over my Astro recommendation**; authoring left as "recommend best" → Markdown archetypes now + optional Sveltia CMS later; user is open to me drafting posts in bulk. The user is a designer/marketer/software-engineer building this solo.

**LIVE as of 2026-06-19** at https://mesmerdesignshop.com (custom domain, HTTPS, deploys via GitHub Actions on push to main). Repo: github.com/Esey-M/MesmerDesign.

**User is non-technical and gets overwhelmed by the tooling.** They briefly wanted to ditch Hugo for plain HTML; I clarified they never touch Hugo — **the agreed workflow is: user brings product images + the Etsy listing URL, and I write + publish the blog post for them.** Always keep it that simple; don't make them learn Hugo. After that clarification they chose to **keep Hugo**.

**Design = "Bold & modern"** (their pick): full-bleed maroon gradient hero, Space Grotesk (display) + Inter (body) via Google Fonts, dark high-contrast category cards, dark footer, "Quick answer" callout. In [assets/css/main.css](assets/css/main.css).

**GitHub Pages gotcha (resolved):** setting the custom domain via the Pages UI flipped `build_type` to `legacy`, so GitHub served a **Jekyll** build of the repo (all Hugo pages 404'd, only README-as-homepage showed). Fix: `PUT /repos/Esey-M/MesmerDesign/pages -d '{"build_type":"workflow"}'` then re-dispatch the workflow. If Hugo pages ever 404 again, check build_type is still `workflow`. The deploy workflow builds with the config baseURL (NOT configure-pages base_url, which resolves to the github.io subpath and breaks asset links). A token can be pulled from the osxkeychain git credential helper for API calls (`git credential fill`).
