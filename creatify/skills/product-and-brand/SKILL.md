---
name: product-and-brand
description: "Use to target a brand space and set up or find products in Creatify before product-specific creative work. Covers brand_list, brand_switch, product_list, product_create, product_get, product_update, product_delete, product_choose_logo, product_get_brand_info. Start here when a product/brand can't be found or an ad needs product grounding."
---

# Products & Brands

## Purpose

Creative work for a specific product (ads, promos) should be grounded in the
right **brand space** and **product**. Set this up before generating so the
output matches the user's brand.

## Brand spaces — one at a time

The connection works in **one brand space at a time**. Products, folders, and
brand assets are filed per brand space.

- **`brand_list`** — list brand spaces (`id`, `name`, `is_default`, `is_current`).
- **`brand_switch`** — switch the active brand space by `id`.

If a product/brand the user names can't be found, it usually lives in another
brand space — `brand_list` and `brand_switch` before concluding it's missing.

## Products

- **`product_list`** / **`product_get`** — find and read products in the current
  brand space.
- **`product_create`** — create a product (read the schema for inputs, e.g. a URL
  to scrape brand/product info from).
- **`product_update`** / **`product_delete`** — edit or remove a product.
- **`product_get_brand_info`** — pull brand info (used to ground creative in the
  brand's identity). On a cold cache it may return
  `{"status": "brand_info_in_progress", ...}` while the analysis runs in the
  background — not a failure; call it again in ~30s to get the cached result.
- **`product_choose_logo`** — pick the product/brand logo.

## Flow

1. Confirm the active brand space (`brand_list`; `brand_switch` if needed).
2. Find the product (`product_list` / `product_get`) or create it
   (`product_create`).
3. Hand the product/brand context to the creative flow (`creative-agent`, or a
   direct generation skill).

Use ids returned by these tools; do not guess product/brand ids.
