---
name: pinterest-pin-playbook
description: How to auto-generate the Pinterest bulk-pins CSV + vertical pin images for every new MesmerDesign blog post
metadata: 
  node_type: memory
  type: project
  originSessionId: 571823ac-32b8-4c01-8c67-df712be140b2
---

**Standing job: every time a new blog post is published, also produce its Pinterest pin assets.** Extends [[blog-post-playbook]]. The owner drives traffic from Pinterest → blog → Etsy, and wants this done automatically as part of publishing (asked 2026-06-21), not as a separate request.

### Setup already done (don't redo)
- Pinterest **Business** account "MesmerDesign" (@simonmhretab17). Website **claimed/verified** — the `<meta name="p:domain_verify" content="9f5c6db466399c39a9653800754915a2"/>` tag lives in `layouts/partials/head.html`.
- Pinterest reads **Rich Pins** from the posts' Open Graph tags (already present).

### What to generate per new post (id = the product id from data/products.yaml)
1. **Vertical 2:3 pin image** (Pinterest favors vertical; product images are square). From the full-res source PNG in `intake/source/<id>.png`, make a 1000×1500 JPG on a white canvas, product centered, never cropped:
   - `sips -s format jpeg -Z 900 -s formatOptions 92 intake/source/<id>.png --out /tmp/pin_tmp.jpg`
   - `sips --padToHeightWidth 1500 1000 --padColor FFFFFF -s format jpeg -s formatOptions 88 /tmp/pin_tmp.jpg --out static/img/pins/<id>.jpg`
   - **Commit + push** so the public URL `https://mesmerdesignshop.com/img/pins/<id>.jpg` is live BEFORE the owner uploads the CSV.
2. **Bulk-pins CSV** at repo root `pinterest-bulk-pins.csv` (gitignored — local only). **Include ONLY the new post(s) each batch** (re-uploading old rows creates duplicate pins). Exact header Pinterest's uploader expects:
   `Title,Media URL,Pinterest board,Thumbnail,Description,Link,Publish date,Keywords`
   - **Media URL** = `https://mesmerdesignshop.com/img/pins/<id>.jpg`
   - **Link** = the blog post URL on the **claimed domain** (`https://mesmerdesignshop.com/blog/<category>/<slug>/`) — NOT the Etsy URL directly (keeps Pinterest happy + earns the SEO backlink; the post links on to Etsy).
   - **Pinterest board** = must match an existing board name EXACTLY. Quote every field (descriptions have commas/emojis). Leave Thumbnail + Publish date blank (publish on upload; fill `YYYY-MM-DDTHH:MM:SS` to stagger).
   - **Title** ≤ ~100 chars, keyword-rich. **Description**: benefit-led + a few hashtags. **Keywords**: comma-separated search terms.

### Board ↔ category map (boards must pre-exist in Pinterest, names exact)
- `christmas-gifts` → **Christmas Gift Ideas**
- `jigsaw-puzzle-gifts` → **Jigsaw Puzzles for Adults**
- `graduation-gifts` → **Graduation Gifts**
- New categories: pick/announce a matching board name and have the owner create it once.

### How the owner uploads (their step)
Pinterest → **Instellingen (Settings) → "Meerdere pins tegelijk maken" → CSV-bestand uploaden** → drag `pinterest-bulk-pins.csv`. Pins are created within ~2 hours; failures come by email.

### Alternative (fully automatic, no CSV) — note, not currently used
The same Pinterest page can **auto-publish from an RSS feed**: connect `https://mesmerdesignshop.com/index.xml` once and new posts pin themselves. Downside: it uses the post's cover image (not the nice vertical pin) and pins to ONE board with no per-post routing. The owner prefers the **CSV** path for vertical images + board control. (Tailwind is the paid third-party option if they ever want RSS-style autopilot with vertical pins.)
