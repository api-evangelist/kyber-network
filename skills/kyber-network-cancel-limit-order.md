---
name: cancel-limit-order
description: Cancel KyberSwap limit orders either gaslessly via an EIP-712 signature or on-chain via a hard cancel, including cancelling all of a Maker's orders at once.
api: KyberSwap Limit Order API
spec: openapi/kyber-network-limit-order-openapi.yml
base_url: https://limit-order.kyberswap.com
operations:
  - post-write-api-v1-orders-cancel-sign
  - post-write-api-v1-orders-cancel
  - post-read-ks-api-v1-encode-cancel-batch-orders
  - post-read-ks-api-v1-encode-increase-nonce
generated: '2026-07-19'
method: generated
source: openapi/kyber-network-limit-order-openapi.yml
---

# Cancel a KyberSwap limit order

Two mechanisms. Choose deliberately — they have different cost and finality.

| | Gasless cancel | Hard cancel |
|---|---|---|
| Cost | Free | Costs gas |
| Mechanism | Operator stops co-signing | On-chain transaction |
| Finality | Effective once the operator signature lapses | Immediate and irreversible |
| Use when | Routine order management | You need a guarantee the order cannot fill |

## Gasless cancel

### Step 1 — Get the unsigned cancel message (`post-write-api-v1-orders-cancel-sign`)

```
POST /write/api/v1/orders/cancel-sign
```

Required: `chainId`, `maker`, `orderIds[]`. Returns an EIP-712 payload with
`primaryType` `CancelOrder`.

### Step 2 — Sign it

Sign with `eth_signTypedData_v4` from the `maker` address. A mismatch returns
code `4202`.

### Step 3 — Submit (`post-write-api-v1-orders-cancel`)

```
POST /write/api/v1/orders/cancel
Origin: https://kyberswap.com
```

Required: `chainId`, `maker`, `orderIds[]`, `signature`.

**The `Origin` header is required** on this call — omitting it returns HTTP
`401`. This is the one place in the Limit Order API where a header acts as an
authentication check.

Returns `data.orders[]`, each with `id`, `chainId` and
`operatorSignatureExpiredAt`. That timestamp matters: the cancel takes full
effect once the outstanding operator signature expires. The provider documents
a **90-second** window for gasless cancellation, so an order can still be
filled by a Taker holding a fresh operator signature until it lapses.

## Hard cancel (on-chain)

### Cancel specific orders (`post-read-ks-api-v1-encode-cancel-batch-orders`)

```
POST /read-ks/api/v1/encode/cancel-batch-orders
```

Returns encoded calldata. Send it to the Limit Order contract from the Maker's
wallet and pay gas. Immediate and irreversible.

### Cancel everything (`post-read-ks-api-v1-encode-increase-nonce`)

```
POST /read-ks/api/v1/encode/increase-nonce
```

Returns calldata that bumps the Maker's nonce, invalidating **every** open order
for that maker in one transaction. Use this for an emergency stop. Confirm the
user understands the blast radius before broadcasting — there is no per-order
selectivity and no undo.

Get the contract address to send to with
`get-read-ks-api-v1-configs-contract-address`.

## Errors

| Code | Meaning |
|---|---|
| 4100 | Order not found |
| 4200 | Order already cancelled |
| 4201 | Order already filled — a filled order cannot be cancelled |
| 4202 | Invalid cancel signature |

A `4201` is terminal: the order settled before your cancel landed. Report the
fill to the user rather than retrying. Full registry:
`errors/kyber-network-error-codes.yml`.
