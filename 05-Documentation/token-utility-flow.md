# YOKE Token Utility & Redemption Flow
**Draft v0.1 — 20 November 2026**

## Purpose
Define how a user connects a wallet, redeems YOKE Token, and receives premium access to YOKE Stock Analysis Bot.

## User flow
1. User opens the official YOKE account portal or Bot command.
2. User selects **Connect Wallet**.
3. Backend creates a one-time nonce and a human-readable signing message containing the app domain, wallet address, chain, nonce, and expiry.
4. User signs the message with the wallet. This is a proof of wallet control; it must not request the user's seed phrase or private key.
5. Backend verifies the signature and consumes the nonce to prevent replay.
6. User chooses a subscription package and receives a quote showing token contract, chain, amount, recipient contract, quote expiry, and transaction fee estimate.
7. User signs and submits the on-chain redemption transaction.
8. Backend monitors the correct chain and verifies:
   - expected chain ID;
   - exact YOKE token contract address;
   - redemption contract recipient;
   - exact or minimum accepted amount;
   - successful transaction receipt and event;
   - sufficient confirmations;
   - transaction hash has not been processed before.
9. Backend creates a unique redemption record and activates the corresponding subscription.
10. After fulfillment, the redemption contract finalizes token handling according to the published burn/escrow policy.

## Backend state machine
`CREATED → AWAITING_PAYMENT → PAYMENT_CONFIRMED → FULFILLMENT_PENDING → FULFILLED`

Failure states:
- `EXPIRED`: quote expired before payment.
- `PAYMENT_FAILED`: transaction reverted or did not meet requirements.
- `REFUND_PENDING`: payment confirmed but service could not be fulfilled.
- `REFUNDED`: refund completed.
- `MANUAL_REVIEW`: mismatch, chain reorganization, suspicious activity, or operational exception.

## Security requirements
- Never request or store seed phrases/private keys.
- Verify signed nonce messages server-side.
- Use short-lived, single-use nonces.
- Enforce unique transaction-hash processing and idempotent subscription activation.
- Verify chain ID and contract addresses from server-side configuration, not user-submitted values alone.
- Wait for an appropriate confirmation threshold and account for chain reorganizations.
- Keep a tamper-evident audit log.
- Protect backend signing keys in a secrets manager or hardware-backed custody where possible.
- Use role separation and multisignature controls for treasury/admin functions.
- Rate-limit wallet linking and redemption endpoints.
- Provide support and refund procedures.

## Telegram account linking
A wallet is linked to a YOKE account, not merely to a Telegram username string. Store a stable internal user ID and the Telegram user ID required for bot delivery. Users must be able to unlink or replace a wallet through a secure recovery process.

## Important implementation note
Blockchain settlement and Telegram entitlement activation are separate systems. They are not automatically atomic. Build retry-safe processing, reconciliation, monitoring, and a documented refund/account-credit path before real payments are accepted.
