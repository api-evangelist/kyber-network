---
name: swap-with-aggregator
description: Get the best on-chain swap rate for a token pair from the KyberSwap Aggregator and build the unsigned transaction calldata to execute it.
api: KyberSwap Aggregator API
spec: openapi/kyber-network-aggregator-openapi.yml
base_url: https://aggregator-api.kyberswap.com
operations:
  - get-route
  - post-route-encoded
generated: '2026-07-19'
method: generated
source: openapi/kyber-network-aggregator-openapi.yml
---

# Swap with the KyberSwap Aggregator

Two-step flow: **quote first, then build**. The API never signs or broadcasts —
it returns unsigned calldata for the user's own wallet.

## Before you start

- **No authentication.** There are no API keys, tokens or secrets.
- Always send `x-client-id: <your app or company name>`. Without a consistent
  client id you are rate limited more aggressively. Default limit is **3 rps**.
- `{chain}` is a chain *name* in the path — `ethereum`, `bsc`, `arbitrum`,
  `polygon`, `optimism`, `avalanche`, `base`, `linea`, `mantle`, `sonic`,
  `berachain`, `ronin`, `unichain`, `hyperevm`, `plasma`, `etherlink`, `monad`,
  `megaeth`.
- Amounts are in **wei**, as decimal strings.

## Step 1 — Get the route (`get-route`)

```
GET /{chain}/api/v1/routes?tokenIn=&tokenOut=&amountIn=
x-client-id: MyApp
```

Required: `tokenIn`, `tokenOut`, `amountIn`.

Returns `routeSummary` and `routerAddress`. **Do not cache the route for more
than 5–10 seconds** — quotes reflect live on-chain liquidity and go stale fast.
If you hand a stale `routeSummary` to step 2 the build will fail or the swap
will revert.

## Step 2 — Build the transaction (`post-route-encoded`)

```
POST /{chain}/api/v1/route/build
x-client-id: MyApp
```

Required body: `routeSummary` (verbatim from step 1), `sender`, `recipient`.

Returns encoded `data` plus the `routerAddress`. Send that `data` to
`routerAddress` from the user's wallet. Use the **same `x-client-id`** on both
calls.

## Approvals

The router needs an ERC-20 allowance for `tokenIn`. For tokens supporting
EIP-2612 you can pass a `permit` parameter instead of sending a separate
approval transaction — see the provider's Permit guide.

## Legacy endpoint

`get-route-encode` (`GET /{chain}/route/encode`) is the pre-v1 Legacy API. It
still works but is superseded by the two-step v1 flow above, which the provider
documents as more performant. Prefer v1 for new integrations.

## Errors

Envelope is `{ "code": <int>, "message": "<string>" }` — **not** RFC 9457
problem+json. Expect `400` with codes in the 4001–4011 range for request
validation, and `422` / code `4221` for an unprocessable route. `429` means you
exceeded the rate limit; back off exponentially. Full registry:
`errors/kyber-network-error-codes.yml`.

## Safety

Never proceed to broadcast without showing the user the expected output, price
impact and slippage from `routeSummary`. There is no idempotency key on this
API — a retried build is harmless (it is read-only), but a rebroadcast
transaction is not. Replay safety comes from the wallet's on-chain nonce.
