# YOKE Technical Architecture
## Wallet, Blockchain & Product Integration

**Project:** YOKE
**Version:** 0.1
**Status:** Technical Architecture Draft
**Date:** 20 November 2026

---

## 1. Purpose

This document defines the preliminary technical architecture of the YOKE ecosystem.

The initial architecture connects:

1. YOKE Token.
2. YOKE Stock Analysis Bot.
3. Telegram Mini App.
4. WalletConnect through Reown AppKit.
5. BNB Smart Chain.
6. YOKE Payment Backend.
7. Subscription Management System.

The objective is to enable users to redeem YOKE Tokens for eligible premium subscriptions through a secure and verifiable blockchain payment process.

This document is a technical design draft, not a production deployment specification.

## 2. Architecture Decisions

| Component | Preliminary Decision |
|---|---|
| Blockchain | BNB Smart Chain |
| Mainnet Chain ID | 56 |
| Testnet Chain ID | 97 |
| Token Standard | BEP-20 compatible ERC-20 |
| Token Decimals | 18 |
| Proposed Supply | 100,000,000 YOKE |
| Wallet Protocol | WalletConnect |
| Wallet SDK | Reown AppKit |
| Frontend | React + TypeScript |
| Frontend Build Tool | Vite, subject to project setup |
| Telegram Integration | Telegram Mini Apps |
| Payment Backend | Node.js + TypeScript |
| Blockchain Interaction | Wagmi + Viem |
| Database | PostgreSQL |
| Subscription Interface | Existing YOKE Stock Analysis Bot |
| Gas Payment | User pays BNB |

All token parameters remain subject to final tokenomics approval.

The existing Bot runtime should be preserved where practical. The payment service should integrate with it through a documented API rather than requiring an unnecessary rewrite.

## 3. High-Level Architecture

```text
                         USER
                           |
                           v
                    TELEGRAM BOT
                           |
                           v
                  TELEGRAM MINI APP
                           |
                    REOWN APPKIT
                           |
                     USER WALLET
                           |
                           v
                    BNB SMART CHAIN
                           |
                  +--------+--------+
                  |                 |
                  v                 v
              YOKE TOKEN       PAYMENT ROUTER
                  |                 |
                  +--------+--------+
                           |
                           v
                    TREASURY WALLET

                           |
                           v
                   YOKE BACKEND API
                           |
              +------------+------------+
              |            |            |
              v            v            v
          AUTH SERVICE  ORDER SERVICE  BLOCKCHAIN
                                      MONITOR
              |            |            |
              +------------+------------+
                           |
                           v
                       DATABASE
                           |
                           v
                 SUBSCRIPTION SERVICE
                           |
                           v
                 YOKE STOCK ANALYSIS BOT
```

## 4. System Components

### 4.1 YOKE Token Contract

The token contract represents the YOKE utility token.

Proposed characteristics:

- BEP-20 compatible.
- Fixed initial supply.
- 18 decimals.
- No additional minting after deployment.
- No transfer tax.
- No reflection.
- No rebase mechanism.

The token contract should remain independent of subscription business logic.

### 4.2 YOKE Payment Router

A separate smart contract handles subscription payments.

Responsibilities:

- Receive a unique payment order ID.
- Transfer YOKE Tokens from the user to the designated Treasury Wallet.
- Emit a payment event.
- Prevent duplicate payment for an order.
- Reject invalid or already completed orders.

Proposed event:

```solidity
event PaymentReceived(
    bytes32 indexed orderId,
    address indexed payer,
    uint256 amount
);
```

The payment contract should:

- Use established OpenZeppelin security patterns.
- Use safe token transfer handling.
- Reject zero-value payments.
- Reject duplicate order IDs.
- Restrict administrative functions.
- Avoid unnecessary token custody.
- Include comprehensive unit and integration tests.

The payment contract should not control the user's wallet or private key.

### 4.3 Telegram Mini App

The Mini App provides the user interface for:

- Viewing subscription packages.
- Viewing token payment quotations.
- Connecting wallets.
- Verifying wallet ownership.
- Confirming payments.
- Checking payment status.
- Viewing subscription status.

The Mini App is the primary interface for wallet-related operations.

### 4.4 Wallet Integration

Reown AppKit provides wallet connectivity through WalletConnect.

Initial requirements:

- Support BNB Smart Chain.
- Detect connected wallet address.
- Detect active chain ID.
- Request network switching where necessary.
- Request transaction signatures.
- Display transaction status.
- Handle rejected or failed transactions.

Wallet connection does not automatically prove ownership of a Telegram account.

Wallet ownership must be separately verified through a signed challenge.

### 4.5 YOKE Backend API

The backend manages the application and payment lifecycle.

Core modules:

- Telegram authentication.
- Wallet verification.
- Subscription plans.
- Order creation.
- Payment quotations.
- Blockchain monitoring.
- Transaction verification.
- Subscription activation.
- Transaction reconciliation.

The backend must never store users' private keys or seed phrases.

### 4.6 Blockchain Monitor

The blockchain monitor listens for relevant Payment Router events.

It must verify:

- Correct network.
- Correct Payment Router address.
- Correct YOKE token contract.
- Successful transaction receipt.
- Correct payment order ID.
- Correct payer wallet.
- Correct payment amount.
- Required confirmation threshold.
- Previously unused transaction hash.

The backend must not rely only on frontend transaction responses.

### 4.7 Subscription Service

The subscription service communicates with YOKE Stock Analysis Bot.

Responsibilities:

- Activate eligible premium plans.
- Set subscription start and expiration.
- Prevent duplicate activation.
- Track subscription history.
- Handle payment reconciliation.
- Provide subscription status to the Bot.

The Bot remains responsible for enforcing premium access.

## 5. Authentication Architecture

Two independent authentication checks are required.

### Telegram Authentication

The backend validates Telegram Mini App initialization data.

Requirements:

- Validate the Telegram signature.
- Check authentication timestamp.
- Reject expired authentication data.
- Never trust unvalidated client-side user data.

### Wallet Ownership Verification

The backend generates a one-time challenge.

The challenge should include:

- Application domain.
- Wallet address.
- Chain ID.
- Unique nonce.
- Issued timestamp.
- Expiration timestamp.
- Intended authentication action.

The user signs the challenge using their wallet.

The backend verifies the signature and marks the nonce as used.

A signature must not be reusable for another login or payment authorization.

## 6. Subscription Payment Flow

### Step 1 â€” Subscription Selection

The user opens the Bot and selects an eligible premium plan.

The Bot launches the Telegram Mini App.

### Step 2 â€” Wallet Connection

The user connects a supported wallet using Reown AppKit.

The frontend verifies that the active network is BNB Smart Chain.

### Step 3 â€” Wallet Verification

The backend validates Telegram authentication and wallet ownership.

The wallet is linked to the authenticated Telegram account.

### Step 4 â€” Order Creation

The backend creates a unique order.

Order information:

- Order ID.
- Telegram user ID.
- Wallet address.
- Subscription plan.
- Fiat-denominated reference price.
- Token amount.
- Quotation expiration.
- Order status.

### Step 5 â€” Payment Authorization

The user authorizes the required token spending through their wallet.

For the initial Payment Router design, the standard flow requires:

1. Token approval.
2. Payment transaction.

Both transactions require user authorization and gas.

A permit-based alternative may be evaluated to reduce the number of transactions.

### Step 6 â€” Blockchain Payment

The Payment Router transfers tokens to the Treasury Wallet and emits the payment event.

### Step 7 â€” Payment Verification

The blockchain monitor detects the event and verifies all payment conditions.

The transaction hash is recorded with a unique database constraint.

### Step 8 â€” Subscription Activation

After successful verification, the backend activates the premium subscription.

### Step 9 â€” User Confirmation

The Mini App and Telegram Bot display the payment and subscription status.

## 7. Payment Order Status

Proposed order lifecycle:

```text
PENDING
   |
   v
AWAITING_APPROVAL
   |
   v
AWAITING_PAYMENT
   |
   v
VERIFYING
   |
   v
PAID
   |
   v
ACTIVATED
```

Additional statuses:

- EXPIRED
- FAILED
- RECONCILIATION_REQUIRED

All status changes must be recorded.

The same payment must never activate multiple subscriptions.

## 8. Database Architecture

Initial database entities:

### Users

- id
- telegram_user_id
- wallet_address
- wallet_verified_at
- created_at

### Subscription Plans

- id
- plan_name
- duration
- fiat_price
- active

### Payment Orders

- id
- order_reference
- user_id
- plan_id
- wallet_address
- token_amount
- quote_expires_at
- status
- created_at

### Blockchain Transactions

- id
- order_id
- transaction_hash
- chain_id
- token_contract
- payer_address
- recipient_address
- amount
- block_number
- confirmation_count
- verification_status

Transaction hashes must have a unique constraint within the applicable network.

Database transactions and idempotent processing must protect subscription activation.

## 9. Backend Structure

Proposed structure:

```text
yoke-payment-service/
â”‚
â”œâ”€â”€ src/
â”‚   â”œâ”€â”€ auth/
â”‚   â”‚   â”œâ”€â”€ telegram-auth.ts
â”‚   â”‚   â””â”€â”€ wallet-auth.ts
â”‚   â”‚
â”‚   â”œâ”€â”€ blockchain/
â”‚   â”‚   â”œâ”€â”€ network.ts
â”‚   â”‚   â”œâ”€â”€ provider.ts
â”‚   â”‚   â”œâ”€â”€ event-monitor.ts
â”‚   â”‚   â””â”€â”€ transaction-verifier.ts
â”‚   â”‚
â”‚   â”œâ”€â”€ orders/
â”‚   â”‚   â”œâ”€â”€ create-order.ts
â”‚   â”‚   â””â”€â”€ order-status.ts
â”‚   â”‚
â”‚   â”œâ”€â”€ payments/
â”‚   â”‚   â”œâ”€â”€ quotation.ts
â”‚   â”‚   â”œâ”€â”€ payment-verification.ts
â”‚   â”‚   â””â”€â”€ reconciliation.ts
â”‚   â”‚
â”‚   â”œâ”€â”€ subscriptions/
â”‚   â”‚   â”œâ”€â”€ activation.ts
â”‚   â”‚   â””â”€â”€ subscription-status.ts
â”‚   â”‚
â”‚   â”œâ”€â”€ database/
â”‚   â”‚   â””â”€â”€ connection.ts
â”‚   â”‚
â”‚   â””â”€â”€ server.ts
â”‚
â”œâ”€â”€ tests/
â”œâ”€â”€ package.json
â””â”€â”€ .env.example
```

This is a proposed logical structure. Actual implementation may be adjusted after repository inspection.

## 10. Security Requirements

The system must follow these principles:

1. Never request user seed phrases or private keys.
2. Never trust frontend payment confirmations.
3. Validate Telegram authentication on the backend.
4. Verify wallet ownership using one-time signed challenges.
5. Restrict payment to the official network and contract addresses.
6. Prevent transaction replay and duplicate order redemption.
7. Protect API credentials and environment variables.
8. Use secure administrative access.
9. Monitor blockchain events and transaction failures.
10. Maintain audit logs for payment and subscription events.
11. Use multisignature protection for treasury operations when practical.
12. Perform independent security review before production deployment.

## 11. Network Configuration

### BNB Smart Chain Testnet

- Chain ID: 97
- Native currency: tBNB
- Explorer: https://testnet.bscscan.com/

### BNB Smart Chain Mainnet

- Chain ID: 56
- Native currency: BNB
- Explorer: https://bscscan.com/

The project must use testnet configuration during development.

Mainnet contract addresses must never be mixed with testnet configuration.

## 12. Deployment Architecture

Proposed deployment stages:

### Development

- Local development environment.
- Local database.
- Test wallet.
- Unit testing.

### Testnet

- Testnet token contract.
- Testnet Payment Router.
- Telegram Mini App development deployment.
- Testnet payment verification.
- Subscription integration testing.

### Production

Production deployment requires:

- Approved tokenomics.
- Final smart contract specifications.
- Independent security review.
- Operational monitoring.
- Secure treasury configuration.
- Legal and compliance review.
- Recovery and incident response procedures.

No production deployment is authorized by this draft.

## 13. Cost and Infrastructure Considerations

Initial development should prioritize free or low-cost resources.

Potential costs include:

- RPC infrastructure.
- Frontend hosting.
- Backend hosting.
- Database hosting.
- Smart contract deployment gas.
- Wallet integration services.
- Security review.
- Legal consultation.

The project should avoid unnecessary paid infrastructure before actual product usage justifies it.

Gas sponsorship is not part of the initial design. Users pay their own BNB gas fees.

## 14. Open Technical Decisions

The following items remain unresolved:

- Final Reown AppKit configuration and SDK version.
- Supported wallet list.
- Final YOKE Token contract.
- Payment Router implementation.
- Permit-based payment support.
- RPC provider selection.
- Required block confirmation policy.
- Token quotation and pricing mechanism.
- Treasury multisignature configuration.
- Production hosting.
- Bot-to-payment-service integration protocol.

## 15. Implementation Roadmap

1. Finalize architecture and technical specifications.
2. Develop YOKE Token contract.
3. Develop Payment Router contract.
4. Write contract unit tests.
5. Deploy contracts to BNB Smart Chain Testnet.
6. Build Telegram Mini App.
7. Integrate Reown AppKit.
8. Develop payment backend.
9. Integrate subscription activation with the existing Bot.
10. Perform end-to-end and security testing.
11. Conduct legal and operational readiness review.

---

**YOKE â€” Connect. Empower. Automate.**
