---
name: Sell a game item with Neon Checkout
description: Create a standalone Neon checkout, redirect the player to pay, and confirm the completed purchase via webhook or lookup.
api: openapi/neon-commerce-checkout-openapi.yml
operations: [createCheckout, getCheckout, subscribeToWebhooks, getPurchase]
---

# Sell a game item with Neon Checkout

Use this flow only if you do NOT have a Neon storefront (storefronts create checkouts for you).

## Auth
All server calls use the Environment API key in the `X-Api-Key` header. Sandbox keys are prefixed `pk_sandbox_`, production `pk_`. Base URL: `https://api.neonpay.com`.

## Steps
1. **Subscribe to events once** — `subscribeToWebhooks` (POST `/purchases/webhooks`) with your listener URL and shared secret. You will receive `purchase.completed` and `payment.failed`.
2. **Create the checkout** — `createCheckout` (POST `/checkout`) with the item(s), `successUrl`/`cancelUrl`, store URL, locale, player country, and account ID. Response returns `id`, `token`, and `redirectUrl`.
3. **Redirect the player** to `redirectUrl` in a browser. (For in-page UI, load `@neonpay/js` and pass the checkout `token`.)
4. **Confirm the result**:
   - On success Neon sends `purchase.completed`; on failure `payment.failed` (carries a decline `code` — see `errors/neon-commerce-decline-codes.yml`).
   - Or poll `getCheckout` (GET `/checkout/{checkoutId}`) for status.
5. **Fetch final purchase** — `getPurchase` (GET `/purchases/{purchaseId}`) using the purchase ID from the webhook or the success URL query params, to read finalized taxes/totals and fulfill.

## Rules
- Verify every webhook: HMAC-SHA256 over the raw body using your shared secret, compared to the `x-neon-digest` header.
- Errors return the `APIError` envelope (`statusCode`, stable `code`, `message`) — branch on `code`, not the message.
- Test with card `4242 4242 4242 4242`, CVC `123` in sandbox (see `sandbox/neon-commerce-sandbox.yml`).
