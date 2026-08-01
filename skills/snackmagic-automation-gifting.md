---
name: Trigger a webhook-automation gift order
description: Place a webhook-automation gift order for a batch of recipient emails and poll its status.
api: openapi/snackmagic-stadium-openapi.yml
operations: [createAutomationOrder, checkOrderStatus]
---

# Trigger a webhook-automation gift order

Send gifts to a batch of recipients through a preconfigured Stadium webhook automation
(SnackMagic brand). These endpoints authenticate with a static automation **`api_key`
header** — not the OAuth bearer token used by the rest of the API.

## Prerequisites
- A webhook automation configured on your Stadium Shop, which yields an `api_key`.

## Steps
1. **Create the automation order** — `createAutomationOrder` (POST `/automations/orders`)
   with the `api_key` header and a body of `contact_emails[]` (plus optional
   `recipient_message`, `sender_name`, `budget` in points). The response returns an
   `identifier` and per-email `data[]` (`status`: success | pending | enqueued | failed).
   Processing can take up to 30 minutes.
2. **Check status** — `checkOrderStatus` (GET `/automations/order_status`) to poll the order
   by its `identifier`; the response lists per-recipient `status` and order details.

## Rules
- Authenticate with the `api_key` **header**, not a Bearer token.
- `404` with `{ errors }` means the automation (bad api_key) or order identifier was not
  found; `422` is a transient "try again later" — retry after a delay. See
  `errors/snackmagic-problem-types.yml`.
- If `budget`, `sender_name`, or `recipient_message` are omitted, the automation's configured
  defaults are used.
