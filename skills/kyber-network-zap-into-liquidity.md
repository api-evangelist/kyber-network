---
name: zap-into-liquidity
description: Enter, exit or migrate a concentrated liquidity position in a single transaction using the KyberSwap Zap as a Service (ZaaS) API, starting from any single token.
api: KyberSwap Zap as a Service (ZaaS) API
spec: openapi/kyber-network-zaas-openapi.yml
base_url: https://zap-api.kyberswap.com
operations:
  - Service_GetInRoute
  - Service_GetOutRoute
  - Service_GetMigrateRoute
generated: '2026-07-19'
method: generated
source: openapi/kyber-network-zaas-openapi.yml
---

# Zap into and out of liquidity positions

A "zap" collapses swap + add-liquidity into one transaction. ZaaS is powered by
the Aggregator underneath, which is why it can minimise price impact and fall
back to additional swaps when needed.

## Before you start

- **No authentication.** Default limit is **1 rps** — the tightest of the three
  KyberSwap APIs. Throttle accordingly and cache nothing longer than the quote
  is valid.
- Send `x-client-id: <your app or company name>`. Contact
  `business@kyber.network` to have a client id whitelisted for a higher tier.
- HTTP base path is chain-scoped: `https://zap-api.kyberswap.com/{chain}`.
- A gRPC surface exists at `zap-api.kyberswap.com:443` (proto3 package
  `zap.v1`, captured verbatim in `grpc/kyber-network-zaas.proto`). Over gRPC
  the chain is a numeric `X-Chain-ID` header rather than a path segment.

## Enter a position (`Service_GetInRoute`)

```
GET /api/v1/in/route
```

Quotes a zap-in from a single input token into a new or existing concentrated
liquidity position. Returns the route plus the data needed to build the
transaction.

## Exit a position (`Service_GetOutRoute`)

```
GET /api/v1/out/route
```

Quotes removing liquidity from a position and receiving a **single** output
token.

## Migrate between pools (`Service_GetMigrateRoute`)

```
GET /api/v1/migrate/route
```

Quotes moving an existing position from one pool to another in one
transaction — no manual withdraw-then-redeposit.

## Build, decode and execute

Each `Get*Route` has a matching `Build*Route` and (for in/out/migrate) a
`Decode*Route` on the gRPC service. The quote → build → sign → broadcast shape
is the same as the Aggregator: **the API returns unsigned data and never holds
keys.** The `Decode*Route` calls are useful for showing the user what a piece
of calldata will actually do before they sign it.

## Approvals and permits

Rather than a separate approval transaction you can use permit signatures:

- **EIP-2612** for ERC-20 input tokens.
- **EIP-4494** for an existing position NFT, via the
  `KSZapRouterPositionPermit` contract.

Both let approve + zap settle in a single transaction.

## Safety

Zaps are multi-leg and touch several protocols at once. Always surface the
expected position range, the amount of each token that will actually be
deployed, and any leftover dust to the user before signing. Confirm the target
pool's DEX is on the supported list for that chain — see the provider's
supported chains/DEXes reference. There is no idempotency key; a failed build
is safe to re-request, a broadcast transaction is not.
