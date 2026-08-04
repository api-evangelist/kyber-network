# Kyber Network

<!-- API-EVANGELIST-PROVENANCE:BEGIN -->
> ### About this repository
>
> **This is not our API.** This repository is an independent, third-party profile of a company's
> **publicly available** API surface, maintained by [API Evangelist](https://apievangelist.com).
> API Evangelist does not operate, host, resell, or support this company's APIs, and is not
> affiliated with or endorsed by the company unless stated on the profile.
>
> **Where the information came from.** Everything here is assembled from material a member of the
> public can reach with a browser and no credentials — the company's own website, developer portal
> and documentation, the specifications it publishes for public use (OpenAPI, AsyncAPI, JSON Schema,
> `apis.json`, `llms.txt` and similar), its public repositories, and its public status, pricing and
> changelog pages. **Nothing here is obtained by breaching a system, defeating an access control, or
> using credentials of any kind.**
>
> **The rating is an independent assessment.** The Kin Score and Agent Readiness rating are
> independently calculated scores of a company's *public* API artifacts, produced by API Evangelist
> against a published rubric. They are not certifications, endorsements, security assessments, or
> audits, and they score published artifacts — not the quality, safety, or security of the software.
>
> **Corrections, re-scores, and removal are free.** No partnership, contract, or purchase is
> required, and you do not need to justify the request.
>
> - **Something wrong?** Open an issue on this repository, or email
>   [info@apievangelist.com](mailto:info@apievangelist.com).
> - **Published something new?** Ask for a re-score and we will re-run the rating.
> - **Want the listing taken down?** Say so and we will honor it. The profile is reduced to your
>   company name, a factual description, and a link to your own site, and the company is recorded as
>   **unrated** — never scored zero for having asked.
>
> **Response times.** Acknowledgement within **one business day**; removal or restriction within
> **two business days**; corrections and re-scores within **five business days**.
>
> **On a security or compliance team?** Email
> [info@apievangelist.com](mailto:info@apievangelist.com) with *security* in the subject line and
> you will get a person, not a form. We will tell you exactly which public URLs this profile was
> built from so your team can see the same surface we did, and we will take the listing down on
> request while you work through it.
>
> Full detail: **[Where this data comes from](https://apievangelist.com/about/where-our-data-comes-from)**
<!-- API-EVANGELIST-PROVENANCE:END -->

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
