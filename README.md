# Kyber Network

Kyber Network is the team behind KyberSwap, a multi-chain DeFi liquidity hub and DEX aggregator that routes trades across indexed decentralized exchanges to source the best available on-chain rate.

## APIs

| API | Base URL | Purpose |
|---|---|---|
| [Aggregator](https://docs.kyberswap.com/developer-guide/aggregator-api) | `https://aggregator-api.kyberswap.com` | Instant token swaps with dynamic trade routing |
| [Limit Order](https://docs.kyberswap.com/developer-guide/limit-order-api) | `https://limit-order.kyberswap.com` | Gasless price-conditional trades, off-chain relay with on-chain settlement |
| [Zap as a Service (ZaaS)](https://docs.kyberswap.com/developer-guide/zap-as-a-service-zaas-api) | `https://zap-api.kyberswap.com` | Single-transaction concentrated liquidity entry, exit and migration (HTTP + gRPC) |

All three APIs are public and unauthenticated — no API keys, tokens or secrets. Callers identify themselves with an `x-client-id` header, which tiers rate limits (3 / 10 / 1 rps respectively).

## Agent readiness

KyberSwap is unusually agent-ready for the DeFi category:

- **[MCP server](https://github.com/KyberNetwork/kyberswap-mcp)** — first-party, 13 tools, never holds private keys
- **[Agent Skills](https://github.com/KyberNetwork/kyberswap-skills)** — 16 published skills for local coding agents
- **[llms.txt](https://docs.kyberswap.com/llms.txt)** — complete documentation index
- **[RFC 9727 API catalog](https://kyberswap.com/.well-known/api-catalog)** — machine-readable index of all three services
- **Content Signals** declared in `robots.txt` on both the app and docs hosts

## Artifacts

OpenAPI specs were harvested from the OpenAPI 3.0.3 documents KyberSwap embeds directly in its own API reference pages; the ZaaS proto is captured verbatim from the gRPC reference.

Backed by: pantera-capital — https://kyber.network/
