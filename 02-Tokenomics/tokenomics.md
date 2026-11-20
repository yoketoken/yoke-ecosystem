# YOKE Tokenomics — Draft v0.1
**Status:** Concept draft — not an offer, sale, investment solicitation, or final token specification  
**Date:** 20 November 2026  
**Language:** Indonesian / English

## 1. Executive summary / Ringkasan

YOKE Token is proposed as a utility token for access to premium features of **YOKE Stock Analysis Bot**, the initial and sole YOKE product in this roadmap. The primary utility is redeeming tokens for subscription credit. The project does not promise price appreciation, profit, dividends, interest, or guaranteed returns.

YOKE Token dirancang sebagai token utilitas untuk mengakses fitur premium **YOKE Stock Analysis Bot**. Fungsi utama token adalah ditukarkan menjadi kredit langganan. Proyek tidak menjanjikan kenaikan harga, keuntungan, dividen, bunga, atau imbal hasil.

## 2. Proposed token parameters / Parameter awal

| Parameter | Proposed value | Status |
|---|---|---|
| Token name | YOKE Token | Proposed |
| Ticker | YOKE | Proposed; subject to availability |
| Maximum supply | 100,000,000 YOKE | Proposed |
| Decimals | 18 | Proposed |
| Supply model | Fixed supply; no post-launch mint function | Design target |
| Network | EVM-compatible; BNB Smart Chain is a candidate | Not final |
| Product utility | YOKE Stock Analysis Bot subscriptions | Initial scope |
| Target market | Global, subject to jurisdictional compliance | Strategic target |
| Distribution | Gradual, milestone-based | Proposed |

The network is intentionally not finalized. The project must compare security, wallet support, user fees, ecosystem infrastructure, and legal implications before selecting a chain.

## 3. Utility design / Desain utilitas

### Phase 1 — Subscription redemption
- Users may redeem YOKE for all or part of an eligible Bot subscription.
- The subscription price remains displayed in the product's reference currency (for example, USD or IDR where legally appropriate).
- The amount of YOKE required is calculated using a transparent conversion methodology published by YOKE.
- A redemption is completed only after the required on-chain transaction is confirmed and the subscription is successfully activated.
- If activation fails after payment, a defined refund or account-credit process must apply.

### Phase 2 — Additional discount (not enabled at launch)
A separate loyalty discount may be considered only after real usage data exists. It must not be based on an arbitrary token threshold. Any tier, eligibility snapshot, duration, maximum discount, and funding source must be published before activation.

**Recommended launch rule:** do not combine a token-payment discount with additional balance-based discounts at launch. First validate that token redemption is useful, reliable, and economically sustainable.

## 4. Supply allocation / Alokasi suplai

| Allocation | Share | Tokens | Purpose |
|---|---:|---:|---|
| Ecosystem & user incentives | 35% | 35,000,000 | Product adoption, user incentives, future utility programs |
| Liquidity reserve | 20% | 20,000,000 | Potential market liquidity, subject to legal and market readiness |
| Development | 15% | 15,000,000 | Product, protocol, security, and infrastructure development |
| Treasury | 15% | 15,000,000 | Long-term operating reserve and approved ecosystem expenses |
| Founder & core team | 10% | 10,000,000 | Long-term contributor alignment |
| Partnerships | 5% | 5,000,000 | Strategic integrations and partnerships |
| **Total** | **100%** | **100,000,000** | |

Allocation is not the same as immediate circulation. Wallet addresses, vesting schedules, release conditions, and changes must be disclosed before any public distribution.

## 5. Proposed vesting and release policy / Jadwal pelepasan

These are planning targets and must be finalized before deployment:

- Founder & core team (10%): 12-month cliff, then linear vesting over 36 months.
- Development (15%): 6-month cliff, then linear release over 36 months against published development budgets.
- Partnerships (5%): milestone-based release; unused allocation remains locked.
- Ecosystem & user incentives (35%): gradual release over at least 48 months, with published campaign budgets and eligibility rules.
- Treasury (15%): multisignature custody and budget-based release; no discretionary single-key withdrawals.
- Liquidity reserve (20%): no automatic launch-day unlock. Release only under a documented liquidity policy and after legal/security review.

No allocation should be described as “circulating supply” until it is actually transferable and available to the market.

## 6. Redemption economics / Ekonomi penukaran

A major design risk is the conversion rate between YOKE and the subscription reference price.

Recommended safeguards:
1. Do not promise a fixed fiat value for YOKE.
2. Do not set a token amount for subscriptions before there is a defensible price-discovery method.
3. Before reliable market pricing exists, use a closed beta with test tokens or non-transferable credits rather than implying that a market price exists.
4. Once public-market pricing is considered, define the data source, averaging window, volatility limits, update frequency, rounding, minimum transaction size, and fallback behavior.
5. Show the user the exact token amount and quote expiry before they sign.
6. Never activate a subscription based only on a wallet balance check.

The project should not create artificial demand, guarantee liquidity, or promise buybacks.

## 7. Redemption token handling / Penanganan token yang ditukarkan

Preferred technical design:
- User sends tokens to a dedicated redemption contract.
- The backend verifies the chain, contract address, recipient, amount, transaction status, confirmations, and unique transaction hash.
- The subscription is activated only after successful verification.
- After successful fulfillment, redeemed tokens may be burned or moved according to a publicly documented policy.
- If fulfillment fails, a contract-controlled refund path or equivalent user-protection process must be available.

The exact burn/escrow mechanism must be independently reviewed. Cross-system blockchain and Telegram activation cannot be assumed to be atomic.

## 8. Distribution phases / Tahapan distribusi

**Phase 0 — Research and compliance**
- No public sale.
- Confirm legal classification, issuer obligations, target jurisdictions, tax treatment, and permitted distribution channels.
- Define product subscription packages and a real redemption flow.

**Phase 1 — Testnet and internal testing**
- Deploy test contracts only.
- Test wallet connection, signature verification, redemption, duplicate-claim prevention, refunds, and bot activation.
- Test tokens have no monetary value and must be clearly labelled as test assets.

**Phase 2 — Closed utility beta**
- Limited invited users, only after legal review.
- No promise of exchange listing or liquidity.
- Publish terms, risks, token contract address, and support/refund process.

**Phase 3 — Broader distribution**
- Only after product usage, security, legal, operational, and consumer-protection gates are passed.
- Any public distribution or trading arrangement requires separate review for each relevant jurisdiction.

## 9. Explicit exclusions / Hal yang tidak dijanjikan

YOKE Token does not represent:
- Equity or ownership in YOKE or its business.
- A claim on company revenue, profits, or assets.
- Guaranteed returns, dividends, interest, or capital appreciation.
- Guaranteed exchange listing, liquidity, or a stable market price.
- A right to vote on company matters unless a separate governance framework is formally adopted.

## 10. Risks / Risiko

Users may lose some or all of the value of tokens they acquire. Token prices can be volatile; liquidity may be absent; smart contracts, wallets, bridges, and third-party infrastructure can fail or be exploited. Laws may change, and access may be restricted in certain jurisdictions. The product itself may change or cease operation. The token's utility depends on the continued availability and lawful operation of YOKE Stock Analysis Bot.

## 11. Open decisions / Keputusan yang belum final

- Final legal issuer entity and jurisdiction.
- Final blockchain network and contract standard.
- Whether public transferability is necessary at initial launch.
- Final vesting contracts and custody arrangements.
- Subscription reference currency and conversion-price methodology.
- Whether redeemed tokens are burned or retained in a controlled redemption vault.
- Final legal review for Indonesia and each intended overseas market.
- Independent smart-contract audit and incident-response plan.

---
**Important:** This document is a planning draft, not legal, tax, investment, or financial advice. It is not a whitepaper approved by a regulator and must not be used as a public token-sale document without professional review.
