---
name: Set up a Neon storefront catalog
description: Create items, offers, and offer groups and configure the storefront theme for a Neon Shop.
api: openapi/neon-commerce-storefront-openapi.yml
operations: [createStorefrontItems, createStorefrontOffers, createStorefrontOfferGroups, getStorefrontOffers, updateTheme]
---

# Set up a Neon storefront catalog

## Auth
Environment API key in `X-Api-Key` (`pk_sandbox_` / `pk_`). Each key is scoped to one storefront environment. Base URL `https://api.neonpay.com`.

## Steps
1. **Create items** — `createStorefrontItems` (POST `/storefront/items`). Items are the fulfillment units granted by offers. Bulk create is transactional: if any item fails, all changes are reverted and the error is returned.
2. **Create offers** — `createStorefrontOffers` (POST `/storefront/offers`). An offer is what players purchase; keyed by `sku`; references items. Also transactional.
3. **Group offers** — `createStorefrontOfferGroups` (POST `/storefront/offer-groups`) to arrange offers in the storefront (keyed by `code`).
4. **Theme the shop** — `updateTheme` (PUT `/storefront/theme`) to programmatically set the storefront look and feel.
5. **Verify** — `getStorefrontOffers` (GET `/storefront/offers`) with cursor pagination (`limit`, `startingAfter`, `endingBefore`); read `data[]` and `links.next`.

## Rules
- Bulk creates are all-or-nothing — inspect `APIError.errors[]` on `400 INVALID_ITEM_ERROR` to find the failing entity.
- Upload images first via the Assets API (`createInventoryImage`) and reference them from offers/items.
