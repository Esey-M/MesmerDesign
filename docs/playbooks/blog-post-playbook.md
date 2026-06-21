---
name: blog-post-playbook
description: Exact step-by-step procedure to add a new MesmerDesign blog post from a product
metadata: 
  node_type: memory
  type: project
  originSessionId: 3fd94845-ea5f-461f-9af0-7edcd9d7210a
---

**When the user sends a product (Etsy listing URL + image), do ALL of this to publish a post.** Repo: `/Users/simonmhretab/Mesmer-Tigrinya/MesmerDesign` (Hugo, deploys to https://mesmerdesignshop.com via GitHub Actions on push to `main`). See [[mesmerdesign-blog-project]]. **Wording rule: never "handmade" → use "made-to-order"** [[no-handmade-pod-wording]].

### What to get from the user
- **Etsy listing URL** (needed for the buy button) and **image as a FILE PATH** (e.g. `/Users/.../image.png`) or dropped in `intake/`. An **inline-pasted image in chat is NOT on disk** — ask for a path/file. Optional: price, description, who-it's-for. If no price, check their shop; prices use format `"21,77"` (no € sign, comma decimal).

### Steps
1. **Verify the image** with Read (confirm it's the right product; `image.png` gets reused/overwritten each time).
2. **Pick an `id`** (kebab-case, e.g. `thank-you-part-of-my-story-mug`) and **make two optimized JPGs** with macOS `sips`:
   - Product card: `sips -s format jpeg -Z 1000 -s formatOptions 82 <img> --out static/img/products/<id>.jpg`
   - Post cover: `sips -s format jpeg -Z 1600 -s formatOptions 88 <img> --out content/blog/<category>/<slug>/cover.jpg`
3. **Add to `data/products.yaml`** (keyed by id): `title, url (clean Etsy URL — UTM added automatically), price ("21,77"), image (/img/products/<id>.jpg), themes[], recipients[], occasions[], product_types[]`.
4. **Category**: place post in an existing `content/blog/<category>/` section. Categories so far: gifts-for-him, gifts-for-her, christian-gifts, business-gifts, funny-gifts, personalized-gifts, tigrinya-eritrean-gifts, teacher-appreciation-gifts. **If a new category fits better**, create `content/blog/<cat>/_index.md` (pillar: title, linkTitle, description, keywords, 2-para intro) AND add a nav item in `config/_default/menus.toml`.
5. **Write the post** at `content/blog/<category>/<slug>/index.md`. Front matter: `title` (catchy, keyword-rich), `description` (150–160 chars, benefit + keyword), `date`/`lastmod` (today), `draft: false`, `author: "The MesmerDesign Team"`, taxonomies (themes/recipients/occasions/product_types), `keywords[]`, `featured_products: [<id>]`, and a `faq:` list of `{q, a}` (3–4). Body pattern (copy the two live examples as templates — `content/blog/personalized-gifts/personalized-birthday-mug-with-name/index.md` and `content/blog/teacher-appreciation-gifts/thank-you-for-being-part-of-my-story-mug/index.md`):
   - `{{</* answer */>}}…quotable 1-para answer…{{</* /answer */>}}`
   - `## Top pick` → `{{</* product-grid ids="<id>" */>}}`
   - 1–2 human paragraphs on why it's a good gift
   - `## The pick` → `{{</* product id="<id>" */>}}short blurb{{</* /product */>}}`
   - a "perfect for" bullet list
   - `{{</* cta text="Shop …" id="<id>" */>}}`
   - `{{</* faq */>}}`
   - **SHORTCODE GOTCHA:** shortcodes without inner content use plain `{{</* x */>}}` — NOT self-closing `/>` (build errors otherwise). `product` and `answer` take inner content (paired).
6. **Tidy**: `mv <source image> intake/source/<id>.png` (intake/source is gitignored).
7. **Build & verify**: `hugo --gc --minify` — must be 0 errors. Confirm post HTML built and the buy link contains `utm_campaign=<id>`.
8. **Publish**: `git add -A` → `git -c user.name="Esey-M" commit -m "..."` (end body with `Co-Authored-By: Claude Opus 4.8 <noreply@anthropic.com>`) → `git push`.
9. **Confirm live** (~1–2 min): poll `https://mesmerdesignshop.com/blog/<category>/<slug>/` for HTTP 200 (background bash loop; don't spam api.github.com — unauth limit 60/hr).
10. **Pinterest pin** (do for every post — see [[pinterest-pin-playbook]]): make a vertical 2:3 pin image at `static/img/pins/<id>.jpg` (commit+push it), then add the new post(s) to a local `pinterest-bulk-pins.csv` the owner drags into Pinterest's "Meerdere pins tegelijk maken" CSV uploader.

### Reusable building blocks already in the repo
- Single source of products: `data/products.yaml`. Shortcodes: `product`, `product-grid`, `cta`, `faq`, `answer`, `disclosure` in `layouts/shortcodes/`. Tracked-URL builder: `layouts/partials/func/etsy-url.html` (auto-appends UTM). Post hero already uses `.Fit` (full image, never cropped). JSON-LD/SEO is automatic per page. New post archetype: `archetypes/post.md`.
- A product can be reused across many posts (round-ups via `featured_products` or `{{< product-grid theme="…" >}}`).
