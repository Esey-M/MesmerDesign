# MesmerDesign — Gift Guides Blog

A fast, SEO- and GEO-optimized [Hugo](https://gohugo.io) site that publishes human-style
gift guides and funnels readers to the [MesmerDesign Etsy shop](https://www.etsy.com/shop/MesmerDesing).
Built to scale to **thousands** of posts and deploy free on **GitHub Pages**.

---

## Quick start

```bash
# Requires Hugo EXTENDED (image processing). Install:
brew install hugo                 # macOS
# or see https://gohugo.io/installation/

# Run locally with drafts:
hugo server -D                    # http://localhost:1313

# Production build:
hugo --gc --minify
```

## Add a new gift guide (the everyday workflow)

```bash
hugo new content blog/<category>/<post-slug>/index.md
# e.g.
hugo new content blog/gifts-for-her/birthday-gifts-for-mom-under-50/index.md
```

This scaffolds the post from `archetypes/post.md` with all SEO/GEO fields prefilled. Then:

1. Fill in `title`, `description` (150–160 chars, keyword + benefit), and the taxonomy
   facets (`themes`, `recipients`, `occasions`, `product_types`).
2. List the Etsy products you're featuring in `featured_products` (ids from
   `data/products.yaml`). This also generates `ItemList` product schema.
3. Drop a **`cover.jpg`** (≈1200×675) next to `index.md` for the hero + social image.
4. Write the body. Open with `{{< answer >}}…{{< /answer >}}` (the quotable summary AI
   engines lift), then use the shortcodes below.
5. Set `draft: false` and commit. Pushing to `main` auto-deploys.

### Shortcodes

| Shortcode | Purpose |
|---|---|
| `{{< answer >}}…{{< /answer >}}` | Answer-first callout (GEO / featured snippets). |
| `{{< product id="custom-photo-mug" >}}desc{{< /product >}}` | One inline product card with tracked CTA. |
| `{{< product-grid ids="a,b,c" />}}` | Grid of products by id. |
| `{{< product-grid theme="faith" limit="6" />}}` | Grid auto-built from a facet. |
| `{{< cta text="Shop now" id="..." />}}` | A standalone "Shop on Etsy" button. |
| `{{< faq />}}` | Renders the `faq:` front-matter list + emits `FAQPage` schema. |
| `{{< disclosure >}}…{{< /disclosure >}}` | Inline disclosure note. |

## Managing Etsy products

All listings live in **`data/products.yaml`**, keyed by id. To add/update a product:

```yaml
my-product-id:
  title: "Exact Product Title"
  url: "https://www.etsy.com/listing/1234567890/..."   # clean URL; UTM added automatically
  price: "29,03"                                         # number only, no € sign
  image: "/img/products/my-product-id.jpg"              # square image in static/img/products/
  themes: [faith]
  recipients: [mom]
  product_types: [mug]
  occasions: [birthday]
```

Change a price or link **once here** and it updates everywhere. UTM tags
(`utm_source=blog&utm_medium=referral&utm_campaign=<id>&utm_content=<post>`) are appended
automatically so you can see which guides drive sales in Etsy Stats / GA.

> Add real listing URLs and square product images (`static/img/products/<id>.jpg`) — the
> seeded entries currently point to the shop homepage as placeholders.

## SEO / GEO features (built in)

- Per-page `<title>`, meta description, canonical, OpenGraph + Twitter cards.
- JSON-LD: `Organization` + `WebSite` (home), `BreadcrumbList` (all), `BlogPosting`,
  `ItemList` (featured products), `FAQPage`.
- `sitemap.xml`, RSS feeds, `static/robots.txt` (explicitly allows AI crawlers: GPTBot,
  PerplexityBot, ClaudeBot, Google-Extended, …).
- `llms.txt` at the site root — a curated map for LLM crawlers.
- Optimized WebP images, lazy loading, minimal CSS, ~zero JS → fast Core Web Vitals.
- Pillar → cluster structure: category `_index.md` = pillar; posts inside = cluster.

## Content structure

```
content/blog/<category>/_index.md            # pillar/hub page (broad keyword)
content/blog/<category>/<post>/index.md      # cluster post (page bundle + its images)
```

Cross-cutting taxonomies (`recipients`, `occasions`, `themes`, `product_types`) auto-
generate landing pages at `/recipients/mom/` etc. — thousands of internal links for free.

## Deploy to GitHub Pages

1. Push this repo to GitHub.
2. **Settings → Pages → Build and deployment → Source: GitHub Actions.**
3. The workflow in `.github/workflows/deploy.yml` builds and deploys on every push to
   `main`.

### Custom domain

1. **`static/CNAME`** holds the custom domain (`mesmerdesignshop.com`).
2. At your registrar, point DNS at GitHub Pages:
   - `CNAME` record: `www` → `<your-github-username>.github.io`
   - For the apex/root, add the four GitHub Pages `A` records (and `AAAA`), per
     [GitHub's docs](https://docs.github.com/pages/configuring-a-custom-domain-for-your-github-pages-site).
3. In **Settings → Pages**, set the custom domain and enable **Enforce HTTPS**.
4. Update `baseURL` in `config/_default/hugo.toml`, the `Sitemap:` line in
   `static/robots.txt`, and `etsyShopURL` is already set in `config/_default/params.toml`.

## To-do before launch

- [ ] Replace placeholder `url`s in `data/products.yaml` with real Etsy listing URLs.
- [ ] Add square product images to `static/img/products/`.
- [ ] Add `static/img/og-default.png` (1200×630) and `static/img/logo.png`.
- [ ] Set your real custom domain (CNAME, baseURL, robots Sitemap line).
- [ ] (Optional) Wire up a free Git-based CMS (Sveltia/Decap) for browser editing.
```
