# 📄 Bitcoin-Backed Lending Platform

## Overview

The **Bitcoin-Backed Lending Platform** is a decentralized financial (DeFi) protocol built on the Stacks blockchain. It enables users to **lock Bitcoin as collateral** and receive **crypto-denominated loans** in a trustless and permissionless manner. The platform enforces strict collateralization, automated liquidation, and fair interest mechanisms, ensuring secure and predictable borrowing and lending experiences.

---

## ✨ Key Features

* 🔒 **Bitcoin-backed loans** with on-chain enforcement
* 💸 **Over-collateralization** to reduce risk (default: 150%)
* ⚠️ **Automatic liquidation** of under-collateralized loans (triggered at 120%)
* 🛠️ **Governance-controlled parameters** (fee rates, collateral ratios, asset prices)
* 📈 **Interest accrual per block**
* 🧾 Transparent loan and collateral tracking

---

## ⚙️ System Overview

### Participants

* **Borrowers**: Lock BTC and receive loans
* **Platform Owner**: Authorized to manage parameters and feed asset prices
* **Smart Contract**: Executes all core logic including loan issuance, repayment, liquidation, and parameter updates

### Collateral and Loan Flow

1. **Deposit Collateral**: Users deposit BTC (via trusted off-chain integration, e.g., sBTC, xBTC, or via native BTC wrapping mechanism).
2. **Request Loan**: User requests a loan, validated against price feed and collateral ratio.
3. **Interest Accrual**: Interest accrues per block based on a fixed rate.
4. **Repayment**: User repays loan + interest, and receives back collateral.
5. **Liquidation**: If BTC price drops and collateral ratio falls below threshold, position is liquidated.

---

## 📐 Contract Architecture

### Constants

* `minimum-collateral-ratio` (default `u150` = 150%)
* `liquidation-threshold` (default `u120` = 120%)
* `platform-fee-rate` (default `u1` = 1%)
* `VALID-ASSETS` = `"BTC"`, `"STX"` (extendable)

### Data Variables

* `platform-initialized`: Contract activation flag
* `total-btc-locked`: Aggregate BTC collateral
* `total-loans-issued`: Counter for unique loan IDs

### Data Maps

| Map Name            | Purpose                                          |
| ------------------- | ------------------------------------------------ |
| `loans`             | Tracks each loan’s parameters and status         |
| `user-loans`        | Maintains list of active loans for each borrower |
| `collateral-prices` | Price feeds for BTC or other accepted assets     |

---

## 🔄 Functional Overview

### Initialization

```clarity
(define-public (initialize-platform) ...)
```

* Restricts call to `CONTRACT-OWNER`
* Activates platform once

---

### Collateral Management

```clarity
(define-public (deposit-collateral (amount uint)) ...)
```

* Locks BTC (assumes trusted BTC wrapping mechanism or sBTC compatibility)
* Updates total locked BTC

---

### Loan Lifecycle

#### Request Loan

```clarity
(define-public (request-loan (collateral uint) (loan-amount uint)) ...)
```

* Validates sufficient collateral via real-time BTC price
* Issues loan with 5% interest rate by default
* Appends loan ID to borrower's list

#### Repay Loan

```clarity
(define-public (repay-loan (loan-id uint) (amount uint)) ...)
```

* Calculates interest based on time elapsed
* Requires full repayment to close loan
* Returns collateral to user and removes active loan reference

---

### Liquidation Logic

```clarity
(define-private (check-liquidation (loan-id uint)) ...)
```

* Evaluates real-time collateral ratio
* Liquidates position if below threshold
* Flags loan as "liquidated" and clears borrower's loan list

---

## 🛡️ Governance Functions

These functions are restricted to `CONTRACT-OWNER`.

* `update-collateral-ratio`
* `update-liquidation-threshold`
* `update-price-feed`

All updates are validated and constrained to prevent manipulation.

---

## 📊 Read-Only Functions

| Function             | Description                                                |
| -------------------- | ---------------------------------------------------------- |
| `get-loan-details`   | Retrieve metadata for a specific loan                      |
| `get-user-loans`     | Fetch all active loan IDs for a borrower                   |
| `get-platform-stats` | View system-wide stats like total BTC locked, loans issued |
| `get-valid-assets`   | Retrieve list of allowed collateral assets                 |

---

## 📡 Data Flow (Simplified)

```plaintext
User -> deposit-collateral -> Contract locks BTC

User -> request-loan -> Contract validates & creates loan record

Platform Owner -> update-price-feed -> Updates BTC/USD oracle

Anyone -> check-liquidation -> Triggers auto-liquidation if under-collateralized

User -> repay-loan -> Closes loan, returns collateral
```

---

## ✅ Security Considerations

* All external interactions (e.g., BTC deposits) must be trusted or bridged securely (e.g., via sBTC).
* Loan issuance is protected by real-time price checks and enforced collateralization.
* Platform owner actions are validated to prevent abuse.
* Loan caps and collateral ratios are set conservatively to minimize protocol risk.

---

## 🔧 Extensibility & Future Enhancements

* **Multi-Asset Support**: Add support for new collateral types via `VALID-ASSETS`.
* **Interest Rate Model**: Replace fixed interest rate with dynamic model.
* **Fee Mechanism**: Accrue and distribute platform fees to a DAO or treasury.
* **Liquidator Incentives**: Reward third parties who trigger valid liquidations.
* **Oracle Integration**: Replace manual price feeds with Chainlink or similar oracle service.

---

## 📘 Contract Entry Points Summary

| Type      | Entry Point                    | Description                          |
| --------- | ------------------------------ | ------------------------------------ |
| Public    | `initialize-platform`          | One-time platform setup              |
| Public    | `deposit-collateral`           | Lock BTC as collateral               |
| Public    | `request-loan`                 | Request a loan using BTC             |
| Public    | `repay-loan`                   | Repay a loan and retrieve collateral |
| Public    | `update-collateral-ratio`      | Adjust global collateral ratio       |
| Public    | `update-liquidation-threshold` | Adjust liquidation trigger           |
| Public    | `update-price-feed`            | Manually update BTC price            |
| Read-Only | `get-loan-details`             | Inspect individual loan              |
| Read-Only | `get-user-loans`               | Inspect user's active loans          |
| Read-Only | `get-platform-stats`           | Inspect platform-level stats         |
| Read-Only | `get-valid-assets`             | List of acceptable collateral assets |

---

## 📎 Requirements

* Stacks blockchain
* Clarity 2.1+
* Compatible BTC collateralization mechanism (sBTC, bridging, wrapping, etc.)
* External or manual BTC/USD price feed

---

## 📬 Contact / Contributing

For improvements or integration suggestions, please submit an issue or pull request. This contract can be extended to support multi-collateral, additional governance logic, and DAO integration.

---

## 📄 License

MIT License. Use at your own risk. This smart contract is unaudited and should not be used in production without proper third-party security reviews.
