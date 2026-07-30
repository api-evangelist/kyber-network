---
name: place-limit-order
description: Create a gasless KyberSwap limit order as a Maker by fetching the EIP-712 message, signing it with the user's wallet, and submitting it to the orderbook.
api: KyberSwap Limit Order API
spec: openapi/kyber-network-limit-order-openapi.yml
base_url: https://limit-order.kyberswap.com
operations:
  - post-write-api-v1-orders-sign-message
  - post-write-api-v1-orders
  - get-read-ks-api-v1-orders
generated: '2026-07-19'
method: generated
source: openapi/kyber-network-limit-order-openapi.yml
---

# Place a KyberSwap limit order

Orders are relayed **off-chain** and settled **on-chain**. Creating an order
costs no gas — the Maker only signs an EIP-712 message.

## Before you start

- **No authentication.** No API keys. Default limit is **10 rps**.
- Send `x-client-id: <your app or company name>`.
- Amounts (`makingAmount`, `takingAmount`) are **wei**, as strings.
- `expiredAt` is a **unix timestamp** and must be in the future.
- The Maker must hold enough `makerAsset` **and** have granted the Limit Order
  contract sufficient allowance — the allowance must cover *all* the Maker's
  active orders, not just this one.

## Step 1 — Get the unsigned message (`post-write-api-v1-orders-sign-message`)

```
POST /write/api/v1/orders/sign-message
```

Required: `chainId`, `makerAsset`, `takerAsset`, `maker`, `makingAmount`,
`takingAmount`, `expiredAt`.
Optional: `receiver` (defaults to `maker`), `allowedSenders` (restrict who may
fill).

Returns `data` containing `types`, `domain`, `primaryType` and `message` — a
complete EIP-712 payload — plus the `salt` inside `message`.

## Step 2 — Sign with the user's wallet

Sign the returned payload with **`eth_signTypedData_v4`**. The signature must be
produced by the `maker` address; a mismatch is rejected as code `4004`. Never
handle or request the user's private key — hand the payload to their wallet.

## Step 3 — Submit the order (`post-write-api-v1-orders`)

```
POST /write/api/v1/orders
```

Body is the same order fields as step 1, extended with `salt` (from the step-1
response) and `signature` (from step 2). Returns `data.id`, the new order id.

## Step 4 — Confirm (`get-read-ks-api-v1-orders`)

```
GET /read-ks/api/v1/orders
```

Query the Maker's orders to confirm the order is live. Valid `status` filters
are exactly `active`, `open`, `cancelled`, `filled`, `expired` — anything else
returns code `4101`. `get-read-ks-api-v1-orders-active-making-amount` reports
how much of the Maker's balance is already committed to active orders, which is
what to check before placing another order against the same asset.

## Errors

| Code | Meaning | Fix |
|---|---|---|
| 4001 | Invalid chain ID | Chain not supported |
| 4002 / 4003 | Invalid maker/taker asset | Not a valid ERC-20 on that chain; native tokens are rejected |
| 4004 | Invalid signature | Signature does not match the Maker address |
| 4005 | Order expired | `expiredAt` is in the past |
| 4006 | Making amount too low | Below the minimum accepted value |
| 4007 | Insufficient allowance | Raise the allowance to cover all active orders |

Envelope is `{ "code", "message" }` (plus `errorEntities` on validation
errors). Full registry: `errors/kyber-network-error-codes.yml`.

## Safety

There is **no idempotency key**. If step 3 times out, do **not** blindly retry —
query `get-read-ks-api-v1-orders` first to see whether the order landed, or you
risk placing a duplicate order against the same balance.
