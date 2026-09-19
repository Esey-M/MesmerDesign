# AGENTS.md — MesmerDesign

Instructions for AI coding assistants (Claude Code, Codex, Cursor, Copilot, Gemini, etc.)
working in this repo. Read this before changing content or templates. `README.md` covers
the general Hugo setup; this file covers the house rules and workflows learned while
building the site.

---

## 1. What this project is

- A **Hugo static-site blog** (Hugo v0.163 **extended**) live at **https://mesmerdesignshop.com**.
  Repo: `github.com/Esey-M/MesmerDesign` (public). Deploys via GitHub Actions
  (`.github/workflows/deploy.yml`) on every push to `main`.
- Purpose: drive organic + AI-search traffic to the owner's **print-on-demand Etsy shop
  "MesmerDesing"** (the misspelling is the real shop slug). The shop launched June 2026 with
  no sales/reviews, so the blog is the main acquisition channel.
- Strategy: human-style, buyer-intent gift guides optimized for **SEO and GEO** (AI answer
  engines), built to scale to thousands of posts. Traffic path: **Pinterest → blog → Etsy**.
- Secondary revenue: **Amazon Associates** links for complementary items the shop doesn't make.
- **Workflow with the owner:** they send a product (Etsy listing URL + image) and the assistant
  writes and publishes the post end to end. Keep it that simple — don't ask them to learn Hugo.

### Architecture

- Brand color **#750303** (deep maroon), warm off-white background. Design is "bold & modern":
  maroon gradient hero, Space Grotesk (display) + Inter (body), dark category cards, dark
  footer, "Quick answer" callout. Styles in `assets/css/main.css`.
- Posts are page bundles: `content/blog/<category>/<slug>/index.md` (+ `cover.jpg`).
  Category `_index.md` = pillar page. Taxonomies `recipients`, `occasions`, `themes`,
  `product_types` auto-generate landing pages.
- **All Etsy products live in `data/products.yaml`** (keyed by id). **All Amazon companion
  products live in `data/amazon.yaml`.** Never hard-code product links in posts.
- Shortcodes in `layouts/shortcodes/`: `answer`, `product`, `product-grid`, `cta`, `faq`,
  `disclosure`, `amazon`, `amazon-grid`, `current-year`.
- SEO/GEO is automatic per page: JSON-LD (BlogPosting, ItemList, FAQPage, BreadcrumbList,
  Organization + WebSite), `llms.txt`, `robots.txt` allowing AI crawlers.

### Technical gotchas

- **Shortcodes without inner content use plain `{{< x >}}` — never self-closing `{{< x />}}`**
  (build errors). `answer` and `product` are paired (take inner content). Note: the shortcode
  table in `README.md` shows `/>` — that is wrong; follow this file.
- JSON-LD `jsonify` inside `<script>` needs `| safeJS` or it double-escapes.
- Permalink token is `:slugorcontentbasename` (`:slugorfilename` was removed from Hugo).
- **GitHub Pages must stay on `build_type: workflow`.** Setting the custom domain in the Pages
  UI once flipped it to `legacy`, so GitHub served a Jekyll build and every Hugo page 404'd.
  Fix: `PUT /repos/Esey-M/MesmerDesign/pages` with `{"build_type":"workflow"}`, then re-run
  the workflow. The workflow builds with the config `baseURL` (not configure-pages'
  `base_url`, which resolves to the github.io subpath and breaks assets).

---

## 2. Hard rules (always apply)

### Wording: never "handmade"
Products are **print-on-demand / made-to-order**, printed by a supplier. Never write
"handmade", "hand-made" or "handcrafted" anywhere (posts, product copy, about, disclosures) —
it's inaccurate and conflicts with Etsy's handmade rules. Use **"made-to-order"**, "printed to
order", or "print-on-demand". "We design" is fine.

### Prices: USD "under $X" — never exact, never €
- `data/products.yaml` prices are **EU prices incl. 20% VAT**, formatted `"21,77"` (comma
  decimal, no € sign). Most buyers are US and pay no VAT.
- `layouts/partials/func/us-price.html` converts automatically using `[pricing]` in
  `config/_default/params.toml` (`usMultiplier = 0.9`, `roundTo = 5`), rounding **up** to a
  $5 ceiling: 21,77→$20, 29,03→$30, 23,17→$25, 33,53→$35, 49→$45, 64,34→$60.
- **In prose, always write "under $X"** — never an exact figure, never euros.

### Images: never crop product images
- Use Hugo **`.Fit`**, never `.Fill`. CSS uses `object-fit: contain`, never `cover`, on
  product imagery. This applies to heroes, cards, related cards — every template. The owner
  has rejected cropped mug designs more than once.

### Etsy links: use the shop subdomain (Share & Save)
- Every shop/listing link uses **`https://mesmerdesing.etsy.com/...`**, never `www.etsy.com`
  — Etsy charges a lower fee on off-site sales through the seller's own subdomain link.
- Listing: `https://mesmerdesing.etsy.com/listing/<id>/<slug>` (same path, host swapped).
  If given a `www.etsy.com/listing/...` URL, swap the host before saving.
- Shop home: `https://mesmerdesing.etsy.com/` — **do not append `/shop/MesmerDesing`**
  (produces a doubled path that 404s).
- Exception: Etsy's own corporate pages (e.g. `https://www.etsy.com/legal/privacy/`) stay on `www`.
- UTM tags are appended automatically by `layouts/partials/func/etsy-url.html`.

### SEO titles: add `seoTitle` when `title` > ~45 chars
- House style uses long, punchy titles as the H1. Search engines truncate `<title>` past ~60
  chars, so any post whose `title` exceeds ~45 chars **must** set a short, keyword-first
  `seoTitle` (e.g. "Custom Dog Portrait from Photo: Royal Dog King").
- `layouts/partials/seo.html` uses `seoTitle` if present and appends " | MesmerDesign" only
  when the result fits in 60 chars. `og:title`/`twitter:title` use the clean title (also what
  Pinterest Rich Pins show).
- Audit: `find public -name index.html -not -path "*/page/1/*" -exec grep -ho "<title>[^<]*</title>" {} \; | sed 's/<[^>]*>//g' | awk 'length($0)>60'`
  (`page/1/` alias pages legitimately have a URL as title — ignore them.)

### IndexNow: only changed URLs
- The deploy workflow's IndexNow step diffs `content/` between the previous and current
  commit (`--diff-filter=AMR`) and maps changed files to URLs via `/urlmap.json` (generated by
  `layouts/index.urlmap.json`). Permalink logic lives only in `hugo.toml` — don't duplicate it.
- **Never resubmit the whole sitemap** to "index faster" — Bing throttles/ignores sites that
  do. Runs with no diff base submit nothing. The `workflow_dispatch` input
  `indexnow_full_resubmit: true` is only for a domain move or sitewide URL change.

### Amazon Associates
- Tag **`mesmerdesign-20`**. Approved 2026-09-05; needs **3 qualifying sales by 2027-03-04**
  or access is withdrawn.
- Amazon links are **additive** — for complementary items (frames/hanging hardware for wall
  art, cardstock/laminators for printables, puzzle mats, gift wrap, books). Never replace Etsy links.
- Add items to `data/amazon.yaml` (`title`, `note`, `search`, and optionally `asin` or `url`).
  Render with `{{< amazon id="x" >}}` or `{{< amazon-grid ids="a, b, c" >}}` (non-paired —
  never self-close). The tag is appended by `layouts/partials/func/amazon-url.html` —
  **never hand-write a tag in a post**.
- With no `asin`/`url`, the link falls back to a **tagged Amazon search** built from `search`
  (still earns). An `asin` or a full product URL is rebuilt as canonical `/dp/<ASIN>?tag=`.
- **Never** put an Amazon price or an Amazon-hosted image URL on the site (breaches the terms).
- The affiliate disclosure is automatic: `layouts/partials/disclosure-inline.html` detects
  `{{< amazon` in a post (or `amazon_links: true` in front matter). Site-wide disclosure is at
  `/legal/disclosure/`; `/legal/privacy/` covers the Amazon cookie.
- **Sourcing ASINs:** Amazon blocks direct page fetches, but a web search restricted to
  `amazon.com` (brand + type + key spec) returns real `/dp/<ASIN>` URLs with titles. Pick a
  known brand whose **title actually matches the entry's promise** (past misses: 24×32 frame
  for 24×36, stickers for "museum putty", book cover instead of padded sleeve). Update
  `search:` to match the linked brand. **Never invent an ASIN** — if nothing fits, leave it
  empty. Stock, price and rating can't be verified; never claim a listing is confirmed live.

---

## 3. Playbook: publish a post from a product

**Inputs from the owner:** the Etsy listing URL and the product image **as a file on disk**
(a path, or dropped into `intake/`). An image pasted inline in chat is not on disk — ask for
a file. Optional: price, description, who it's for.

1. **Look at the image** to confirm it's the right product (`intake/image.png` gets reused).
2. **Pick a kebab-case `id`** (e.g. `thank-you-part-of-my-story-mug`) and make two JPGs (macOS `sips`):
   - Card: `sips -s format jpeg -Z 1000 -s formatOptions 82 <img> --out static/img/products/<id>.jpg`
   - Cover: `sips -s format jpeg -Z 1600 -s formatOptions 88 <img> --out content/blog/<category>/<slug>/cover.jpg`
3. **Add to `data/products.yaml`**: `title`, `url` (subdomain host!), `price` (`"21,77"`),
   `image` (`/img/products/<id>.jpg`), `themes[]`, `recipients[]`, `occasions[]`, `product_types[]`.
4. **Choose a category** from `content/blog/`. If a new one fits better, create
   `content/blog/<cat>/_index.md` (title, linkTitle, description, keywords, 2-paragraph intro)
   **and** add a `[[main]]` entry in `config/_default/menus.toml` with
   `parent = "gift-guides"` and `[main.params] group = "By recipient" | "By occasion" | "By style"`.
5. **Write** `content/blog/<category>/<slug>/index.md` (archetype: `archetypes/post.md`).
   Front matter: `title`, `seoTitle` (if title > ~45 chars), `description` (150–160 chars,
   benefit + keyword), `date`/`lastmod` (today), `draft: false`,
   `author: "The MesmerDesign Team"`, taxonomies, `keywords[]`, `featured_products: [<id>]`,
   `faq:` list of 3–4 `{q, a}`.
   Body pattern (templates: `content/blog/personalized-gifts/personalized-birthday-mug-with-name/index.md`,
   `content/blog/teacher-appreciation-gifts/thank-you-for-being-part-of-my-story-mug/index.md`):
   - `{{</* answer */>}}` quotable one-paragraph answer `{{</* /answer */>}}`
   - `## Top pick` → `{{</* product-grid ids="<id>" */>}}`
   - 1–2 human paragraphs on why it's a good gift
   - `## The pick` → `{{</* product id="<id>" */>}}short blurb{{</* /product */>}}`
   - a "perfect for" bullet list
   - `{{</* cta text="Shop …" id="<id>" */>}}`
   - `{{</* faq */>}}`
6. **Tidy:** move the source image to `intake/source/<id>.png` (gitignored; keeps full-res for pins).
7. **Build:** `hugo --gc --minify` must finish with 0 errors. Confirm the post HTML exists and
   its buy link contains `utm_campaign=<id>`.
8. **Publish:** commit (author `Esey-M`) and push to `main`.
9. **Confirm live** after ~1–2 min: `https://mesmerdesignshop.com/blog/<category>/<slug>/`
   returns 200. Don't poll `api.github.com` repeatedly (60 req/hr unauthenticated).
10. **Make the Pinterest pin** (section 4) — part of every publish, not a separate request.

A product can appear in many posts (round-ups via `featured_products` or
`{{</* product-grid theme="…" */>}}`).

---

## 4. Playbook: Pinterest pins (every new post)

Pinterest Business account "MesmerDesign"; the domain is claimed (the `p:domain_verify` meta
is in `layouts/partials/head.html`); Rich Pins read the existing Open Graph tags.

### Pin image — product posts
Vertical 1000×1500 JPG, white canvas, product centered, never cropped:

```bash
sips -s format jpeg -Z 900 -s formatOptions 92 intake/source/<id>.png --out /tmp/pin_tmp.jpg
sips --padToHeightWidth 1500 1000 --padColor FFFFFF -s format jpeg -s formatOptions 88 /tmp/pin_tmp.jpg --out static/img/pins/<id>.jpg
```

If `intake/source/<id>.png` is missing, use `static/img/products/<id>.jpg` (1000px long edge,
so no upscaling). **Commit + push** so `https://mesmerdesignshop.com/img/pins/<id>.jpg` is live
before the CSV is uploaded.

### Pin image — guide/round-up posts
No single product image, so the pin is designed: 1000×1500, the post's `cover.jpg` filling the
top 1040px, a 16px `#750303` rule, then the title in bold maroon (auto-sized/wrapped) with
`mesmerdesignshop.com` beneath, on `#fbf7f1`. Built with PIL +
`/System/Library/Fonts/Supplemental/Arial Bold.ttf`. File named after the **post slug**.
Render a 3–4 pin contact sheet and look at it before committing.

### Bulk-pins CSV
`pinterest-bulk-pins.csv` at repo root (gitignored, local only). **Only the new posts in each
batch** — re-uploading old rows creates duplicate pins. Exact header:

```
Title,Media URL,Pinterest board,Thumbnail,Description,Link,Publish date,Keywords
```

- Media URL = `https://mesmerdesignshop.com/img/pins/<file>.jpg`
- **Link = the blog post URL**, never the Etsy URL (products without a post can't be pinned).
- Board must match an existing board name **exactly**. Quote every field. Leave Thumbnail and
  Publish date blank (or `YYYY-MM-DDTHH:MM:SS` to stagger).
- Title ≤ ~100 chars, keyword-rich. Description benefit-led + a few hashtags. Keywords comma-separated.
- If a product has its own post and also appears in round-ups, pin its **dedicated** post.
- The owner uploads it via Pinterest Settings → "Create multiple pins at once" ("Meerdere pins
  tegelijk maken") → upload CSV.

### Board map (category → board)
Board follows the pin's main occasion/theme, not strictly the category. For a new category,
pick a board name and tell the owner to create it once.

| Category | Board |
|---|---|
| christmas-gifts, stocking-stuffers | Christmas Gift Ideas |
| jigsaw-puzzle-gifts | Jigsaw Puzzles for Adults |
| graduation-gifts | Graduation Gifts |
| birthday-occasion posts (any category) | Birthday Gift Ideas |
| new-job-gifts | New Job Gift Ideas |
| wall-art, framing-and-display | Maximalist Wall Art |
| custom-pet-portraits, pet-parent-gifts | Custom Pet Portraits |
| christian-gifts | Christian Gift Ideas |
| business-gifts | Business & Client Gifts |
| handwriting-practice | Handwriting Practice |
| kids-printables, craft-and-print-supplies | Kids Learning Printables |
| party-printables | Baby Shower Games |
| personalized-gifts | Personalized Gift Ideas |
| funny-gifts | Funny Gift Ideas |
| gifts-for-her (incl. mental-health/cottagecore mugs posted in funny-gifts) | Gifts for Her |
| gifts-for-him | Gifts for Him |
| teacher-appreciation-gifts | Teacher Appreciation Gifts |
| book-lover-gifts | Book Lover Gifts |
| gift-wrapping | Gift Wrapping Ideas |
| cozy-gifts | Cozy Gift Ideas |
| housewarming-gifts | Housewarming Gifts |
| side-hustle-guides | Side Hustle Ideas |

### Products with no post yet (so no pin)
walk-by-faith-sweatshirt, doing-better-than-you-think-mama-mug, teach-breathe-repeat-mug,
i-can-do-hard-things-mug, cat-hair-is-my-glitter-shirt, my-dog-is-my-personality-shirt,
savings-tracker-spreadsheet (as of 2026-07-15 — check before relying on this).

(Not used: Pinterest's RSS auto-publish from `/index.xml` — it uses the cover image and a
single board. The owner prefers the CSV for vertical images and board routing.)

---

## 5. Product-line notes

### Custom Pet Portraits (`content/blog/custom-pet-portraits/`)
- Royal "king" cat/dog portraits in a crimson Renaissance style, made from the buyer's photo.
- Each animal has two products/posts that cross-link: a **printed poster** (~under $30) and a
  **digital download** (300 DPI, ~under $20). Ids: `custom-{cat,dog}-portrait-{poster,digital}`.
- **Intentional quirk — don't "fix":** the cat digital listing reuses an old listing, so its URL
  is `mesmerdesing.etsy.com/listing/4519687776/tigrinya-alphabet-poster-with-audio-qr`. Etsy keeps
  the original slug after edits; it's correct. Other listing ids: cat poster 4527541580,
  dog poster 4527549700, dog digital 4527562142.
