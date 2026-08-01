---
name: Send Stadium Shop points to a recipient
description: Send Stadium Shop points to a recipient email via a treat link, using the Stadium API (SnackMagic brand).
api: openapi/snackmagic-stadium-openapi.yml
operations: [getToken, getStores, sendPoints]
---

# Send Stadium Shop points

Use the Stadium API to send Stadium Shop points to a recipient so they can redeem
gifts, snacks, or swag on Stadium. SnackMagic is a Stadium brand; all endpoints live at
`https://api.bystadium.com/api/v2` (sandbox: `https://api.preprod.bystadium.com/api/v2`).

## Prerequisites
- A Stadium Shop set up with an approved catalog.
- A funded Wallet balance (points are drawn from pre-purchased funds).
- OAuth client credentials (`client_id` / `client_secret`).

## Steps
1. **Get an access token** — `getToken` (POST `/oauth/token`) with `client_id` and
   `client_secret`. Use the returned `token` as the `Authorization: Bearer <token>` header
   on every following call.
2. **Find your store** — `getStores` (GET `/stores`) to locate the `store_number` you will
   send points against.
3. **Send the points** — `sendPoints` (POST `/send_points`) with the store and recipient
   details. The response (`SendPointResponse`) includes the order `number`, an
   `invitation_link` (the treat link the recipient uses), and payment fields
   (`used_wallet_money`, `outstanding_balance`, `payment_state`).

## Rules
- Auth failures return `401` with `{ error: "Invalid token" }` — refresh the token.
- `404` means the store was not found; `422` means the send failed validation
  (e.g. insufficient Wallet funds). See `errors/snackmagic-problem-types.yml`.
- There is **no idempotency key** — do not blindly retry a `sendPoints` call that may have
  succeeded; re-check via the order number first.
