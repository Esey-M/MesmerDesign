# 📥 Intake — how to send Claude your listings

Drop files in this folder and tell Claude "process the intake." Two ways:

## ⚡ Fastest: bulk (all 55 listings at once)

**Step 1 — Export your listings from Etsy (one click, gives almost everything):**
Etsy.com → **Shop Manager → Settings → Options → Download Data → Download CSV**
(choose "Currently for sale listings"). Save the file here as **`intake/listings.csv`**.

That CSV contains, for every listing: **Title, Description, Price, Tags, and Image URLs.**
Claude uses it to auto-download your product images and build the whole catalog —
**you do nothing with images.** ✅

**Step 2 — Send your listing URLs (the one thing the export lacks):**
Paste all your listing links into **`intake/urls.txt`** — one per line, **any order**.
The link contains the product name, so Claude auto-matches each URL to the right product.

> Quick way to grab them: open your shop, click each listing, copy the address-bar URL.
> Or copy them from Shop Manager → Listings. Messy/extra text is fine — Claude sorts it.

That's it. Claude turns the two files into your product catalog + draft blog posts.

---

## 🧩 One at a time (adding a single new product later)

Just message Claude with this little block (and attach/point to the image):

```
URL:      https://www.etsy.com/listing/.....
TITLE:    Give It to God Sweatshirt
PRICE:    49,00
FOR:      mom, her            (optional — who it's for)
OCCASION: birthday, christmas (optional)
NOTES:    anything to emphasize (optional)
```

Claude writes the full post and publishes it in ~2 minutes.

---

## What Claude does with it
1. Adds/updates each product in `data/products.yaml` (single source of truth).
2. Downloads + optimizes images into `static/img/products/`.
3. Drafts SEO/GEO-optimized gift-guide posts that feature the products.
4. Commits → your site rebuilds and goes live automatically.
