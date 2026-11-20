# YOKE Smart Contract Specification

**Project:** YOKE Ecosystem
**Network:** BNB Smart Chain
**Token Standard:** BEP-20 compatible
**Status:** Technical Draft â€” Not Approved for Mainnet Deployment
**Version:** 1.0

---

## 1. Purpose

This document defines the initial technical specifications for the YOKE Token and YOKE Payment Router.

The initial blockchain utility is limited to redeeming YOKE Tokens for eligible premium subscription access to the YOKE Stock Analysis Bot.

The system is designed for gradual distribution, transparent payment verification, and secure interaction through a Telegram Mini App.

## 2. Network Configuration

| Parameter | Configuration |
|---|---|
| Blockchain | BNB Smart Chain |
| Mainnet Chain ID | 56 |
| Testnet Chain ID | 97 |
| Mainnet Native Gas | BNB |
| Testnet Native Gas | tBNB |
| Token Standard | BEP-20 compatible |
| Wallet Architecture | WalletConnect + Telegram Mini App |
| Gas Payment | Paid by the user |

All initial development and testing must take place on BNB Smart Chain Testnet.

Mainnet deployment is not authorized by this specification.

## 3. YOKE Token Contract

### 3.1 Token Parameters

| Parameter | Proposed Value |
|---|---|
| Token Name | YOKE |
| Token Symbol | YOKE |
| Decimals | 18 |
| Initial Total Supply | 100,000,000 YOKE |
| Maximum Supply | 100,000,000 YOKE |
| Minting After Deployment | Not permitted |
| Burning | Standard optional token-holder burn |
| Transferability | Standard BEP-20 transfers |

The token contract must not include a public or owner-controlled function that can increase the maximum supply.

### 3.2 Token Allocation

| Allocation | Percentage | Amount |
|---|---:|---:|
| Ecosystem & User Incentives | 35% | 35,000,000 |
| Liquidity Reserve | 20% | 20,000,000 |
| Development | 15% | 15,000,000 |
| Treasury | 15% | 15,000,000 |
| Founder & Core Team | 10% | 10,000,000 |
| Partnerships | 5% | 5,000,000 |
| **Total** | **100%** | **100,000,000** |

These allocations are preliminary. Wallet addresses, release schedules, vesting contracts, and distribution procedures must be finalized before deployment.

### 3.3 Required Functions

The token contract should provide:

- `name()`
- `symbol()`
- `decimals()`
- `totalSupply()`
- `balanceOf(address)`
- `transfer(address, uint256)`
- `approve(address, uint256)`
- `allowance(address, address)`
- `transferFrom(address, address, uint256)`

The implementation should use a well-established OpenZeppelin ERC-20 implementation compatible with BNB Smart Chain.

No proxy upgrade mechanism is required for the initial version.

## 4. YOKE Payment Router Contract

### 4.1 Purpose

The Payment Router handles YOKE Token payments for premium subscription orders.

Its responsibilities are:

1. Receive payment instructions from a connected wallet.
2. Transfer YOKE Tokens from the user's wallet to the designated treasury.
3. Prevent duplicate order payments.
4. Emit an on-chain payment event.
5. Allow the backend to verify the payment.

The contract does not activate Telegram subscriptions directly.

### 4.2 Payment Flow

The initial payment process uses two blockchain transactions.

**Transaction 1 â€” Approve**

The user approves the Payment Router to spend a specified amount of YOKE Tokens.

**Transaction 2 â€” Pay**

The user calls the Payment Router's `pay()` function.

The router transfers the approved tokens directly from the user's wallet to the treasury.

The user pays the required BNB gas fees for both transactions.

### 4.3 Required State Variables

Proposed state variables:

- `IERC20 public immutable yokeToken`
- `address public immutable treasury`
- `mapping(bytes32 => bool) public paidOrders`

The token and treasury addresses are configured during deployment and cannot be changed in the initial design.

### 4.4 Required Payment Function

Proposed interface:

```solidity
function pay(
    bytes32 orderId,
    uint256 amount
) external;
```

Validation requirements:

- `orderId` must not be zero.
- `amount` must be greater than zero.
- `orderId` must not have been paid previously.
- The user must have sufficient token balance.
- The user must have approved sufficient allowance.
- The token transfer must succeed.

On successful payment, the contract must mark the order as paid and emit an event.

### 4.5 Payment Event

Required event:

```solidity
event PaymentReceived(
    bytes32 indexed orderId,
    address indexed payer,
    uint256 amount
);
```

The event allows the backend to associate an on-chain transaction with an existing subscription order.

### 4.6 Duplicate Payment Protection

The contract must reject an order ID that has already been paid.

Conceptually:

```solidity
require(
    !paidOrders[orderId],
    "Order already paid"
);
```

The order ID must be unique for every payment request.

### 4.7 Token Transfer

The router should use OpenZeppelin `SafeERC20` to execute:

```solidity
safeTransferFrom(
    msg.sender,
    treasury,
    amount
);
```

The token transfer and order recording must occur atomically.

The initial YOKE Token will not charge transfer taxes or transaction fees.

The Payment Router must not retain user payment balances.

No withdrawal function is required because tokens are transferred directly to the treasury.

## 5. Backend Payment Verification

A successful wallet transaction does not automatically mean a premium subscription is activated.

The backend must independently verify:

1. The transaction exists on BNB Smart Chain.
2. The transaction receipt has successful status.
3. The transaction is on the expected chain.
4. The Payment Router contract address is correct.
5. The `PaymentReceived` event exists in the receipt.
6. The event's `orderId` matches an existing unpaid backend order.
7. The event's payer matches the authenticated wallet.
8. The payment amount matches the order amount.
9. The transaction has reached the configured confirmation threshold.
10. The transaction hash has not previously been processed.

Only after all checks pass may the subscription service activate premium access.

### 5.1 Idempotency

The backend must prevent duplicate subscription activation.

Database constraints should include:

- Unique transaction hash.
- Unique payment order ID.
- Unique processed payment record.

Repeated event processing must not extend or duplicate a subscription unintentionally.

## 6. Security Requirements

The initial contracts must follow these requirements:

- Use Solidity 0.8.x with an explicitly pinned compiler version.
- Use established OpenZeppelin contract libraries.
- Use `SafeERC20` for token transfers.
- Validate zero addresses during construction.
- Reject zero payment amounts.
- Reject reused order IDs.
- Protect external payment execution against reentrancy where appropriate.
- Avoid unnecessary privileged functions.
- Avoid upgradeable proxies in the initial version.
- Do not store private keys or seed phrases in the contract or backend.
- Never trust screenshots or client-side payment status.
- Never trust Telegram `initDataUnsafe` as server authentication.

The Payment Router should have no owner-controlled ability to withdraw users' tokens or arbitrarily modify payment records.

## 7. Initial Testing Requirements

### YOKE Token Tests

- Token name and symbol are correct.
- Decimals equal 18.
- Initial supply equals 100 million YOKE.
- Initial recipient receives the full supply.
- Transfers work correctly.
- Approval and `transferFrom` work correctly.
- No additional minting is possible.

### Payment Router Tests

- Successful payment transfers the correct amount.
- Payment event contains the correct order ID.
- Payment event contains the correct payer.
- Payment event contains the correct amount.
- Duplicate order ID is rejected.
- Zero amount is rejected.
- Zero order ID is rejected.
- Insufficient balance is rejected.
- Insufficient allowance is rejected.
- Failed token transfer does not mark the order as paid.
- Tokens are transferred directly to treasury.
- Router does not retain payment balances.

### Backend Integration Tests

- Correct payment activates premium access.
- Incorrect amount does not activate premium access.
- Wrong payer does not activate premium access.
- Wrong network does not activate premium access.
- Wrong router contract does not activate premium access.
- Failed transaction does not activate premium access.
- Duplicate transaction does not activate premium twice.
- Unconfirmed transactions do not activate premium.

## 8. Deployment Plan

### Phase 1 â€” Local Development

- Implement YOKE Token.
- Implement YOKE Payment Router.
- Write unit tests.
- Run static analysis.
- Review contract security.

### Phase 2 â€” BNB Testnet

- Deploy test YOKE Token.
- Deploy test Payment Router.
- Test wallet approval.
- Test payment execution.
- Verify emitted events.
- Integrate with backend.
- Test Telegram premium activation.

### Phase 3 â€” Security Review

- Review contract source code.
- Review access control and token distribution.
- Test payment edge cases.
- Conduct independent security review before mainnet consideration.

### Phase 4 â€” Mainnet Readiness

Mainnet deployment requires separate approval after:

- Legal and regulatory assessment.
- Final tokenomics approval.
- Treasury and distribution wallet preparation.
- Contract security review.
- Backend and monitoring readiness.
- Operational recovery procedures.

## 9. Explicitly Out of Scope

The initial implementation does not include:

- Staking or yield generation.
- Dividends or revenue sharing.
- Guaranteed token appreciation.
- Public token sale.
- Automatic exchange listing.
- Cross-chain bridging.
- Governance voting.
- NFT functionality.
- Complex DeFi integrations.
- Automatic subscription activation from unverified client requests.

These features must not be added without a separate design and approval process.

## 10. Design Status

This document is a technical design specification, not production-ready smart contract code.

All contract implementations must be tested and reviewed before deployment.

**Current approved direction:**
- BNB Smart Chain.
- User-paid gas.
- WalletConnect + Telegram Mini App.
- Fixed-supply YOKE Token.
- Separate YOKE Payment Router.
- Backend-verified premium subscription activation.
