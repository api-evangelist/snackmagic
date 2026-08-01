---
name: Browse a store catalog and place an order
description: Browse a Stadium Shop catalog, create an order, and check it out paying from Wallet or global points.
api: openapi/snackmagic-stadium-openapi.yml
operations: [getToken, getStores, getStoreProducts, createOrder, orderCheckout, orderDetails]
---

# Place an order against a Stadium Shop

Place an order against a Stadium Shop (SnackMagic brand) and hand fulfillment to Stadium.
Base URL `https://api.bystadium.com/api/v2` (sandbox `https://api.preprod.bystadium.com/api/v2`).

## Steps
1. **Get an access token** — `getToken` (POST `/oauth/token`). Set `Authorization: Bearer <token>`.
2. **List stores** — `getStores` (GET `/stores`) to get the `store_number`.
3. **Browse products** — `getStoreProducts` (GET `/stores/{store_number}/products`). Paginate
   with `page` and `per_page`; the response echoes `page` / `per_page`. Note each variant `id`
   (slash-delimited, e.g. `10/1/9`) and `product_type` (e.g. `Spree::Product`).
4. **Create the order** — `createOrder` (POST `/orders`) with `store_number`, `country_iso`,
   a shipping `address`, and `products[]` (each `id`, `quantity`, `product_type`). The
   response includes the order `number`, `platform_fee`, and `estimated_taxes`.
5. **Checkout** — `orderCheckout` (POST `/orders/{order_number}/checkout`) with
   `payment_method` = `use_wallet_money` or `use_global_point`.
6. **Confirm** — `orderDetails` (GET `/orders/{order_number}`) and optionally
   `orderShipmentStatus` for tracking.

## Rules
- Orders are paid from a **pre-purchased Wallet balance** or global Stadium Points — there is
  no card checkout. Ensure funds are deposited first.
- `401` = bad/expired token; `404` = order/store not found; `422` = validation error with a
  `{ errors: [ { source.pointer, detail } ] }` body. See `errors/snackmagic-problem-types.yml`.
- No idempotency support — treat `createOrder` + `orderCheckout` as a deliberate two-step flow.
