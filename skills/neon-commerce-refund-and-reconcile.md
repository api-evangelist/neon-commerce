---
name: Refund and reconcile a Neon purchase
description: Find a purchase, inspect it, issue a refund, and reconcile refunds for an environment.
api: openapi/neon-commerce-purchase-openapi.yml
operations: [findPurchase, getPurchase, refundPurchase, getPurchaseRefunds]
---

# Refund and reconcile a Neon purchase

## Auth
Environment API key in `X-Api-Key`. Base URL `https://api.neonpay.com`. Refund access is gated — Neon typically handles refunds; confirm your account qualifies for `refundPurchase`.

## Steps
1. **Locate the purchase** — `findPurchase` (GET `/purchases/search`) by checkout ID or order number, or `getPurchases` (GET `/purchases`) with cursor pagination.
2. **Inspect** — `getPurchase` (GET `/purchases/{purchaseId}`) to confirm amounts and status before acting.
3. **Refund** — `refundPurchase` (POST `/purchases/{purchaseId}/refund`). On success Neon emits a `refund.processed` webhook.
4. **Reconcile** — `getPurchaseRefunds` (GET `/purchases/refunds`) to list refunds for the environment.

## Rules
- There is no idempotency-key header; do not blindly retry `refundPurchase` on timeout — re-fetch with `getPurchase` first to check refund state.
- Handle `404 PURCHASE_NOT_FOUND` from the `APIError` envelope.
- Subscribe to `dispute.opened` / `dispute.closed` webhooks to catch chargebacks alongside refunds.
