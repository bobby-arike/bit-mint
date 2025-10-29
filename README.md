# 🪙 **BitMint Protocol**

### *Bitcoin-Powered Liquidity Through Trustless Collateralization*

---

## 🧭 **Overview**

**BitMint Protocol** enables Bitcoin holders to unlock liquidity without giving up their BTC exposure or custody.
By leveraging the **Stacks blockchain’s Bitcoin finality**, users can **lock Bitcoin-backed collateral** to **mint synthetic stablecoins** or access liquidity directly, maintaining full transparency and decentralized control.

BitMint turns **idle BTC into productive capital**, transforming Bitcoin holders into active DeFi participants while maintaining **overcollateralized security** and **trust-minimized settlement**.

---

## ⚙️ **System Overview**

BitMint operates as a decentralized lending and collateral management protocol.
It allows users to:

1. **Deposit BTC-backed collateral** (anchored via Stacks’ Bitcoin finality).
2. **Mint or borrow stablecoins** against that collateral.
3. **Repay loans** to reclaim the locked collateral.
4. **Rely on trustless liquidation mechanisms** to ensure system solvency.

### Key Concepts

| Concept                   | Description                                                               |
| ------------------------- | ------------------------------------------------------------------------- |
| **Collateral**            | BTC or BTC-anchored assets locked to back minted liquidity.               |
| **Loan**                  | An overcollateralized debt position represented on-chain.                 |
| **Collateral Ratio**      | Minimum percentage ensuring the collateral value exceeds borrowed value.  |
| **Liquidation Threshold** | Ratio below which a position becomes liquidatable.                        |
| **Synthetic Stablecoin**  | The liquidity token minted against collateral (future integration point). |

---

## 🏗️ **Contract Architecture**

### **Core Smart Contract:** `bitmint.clar`

#### 1. **Constants & Errors**

Defines protocol-wide parameters and standardized error codes for predictable behavior.

* **Authorization & Validation Errors:** e.g., `ERR-NOT-AUTHORIZED`, `ERR-INVALID-AMOUNT`
* **Loan Operation Errors:** e.g., `ERR-LOAN-NOT-FOUND`, `ERR-LOAN-NOT-ACTIVE`
* **Oracle Errors:** e.g., `ERR-INVALID-PRICE`, `ERR-INVALID-ASSET`

---

#### 2. **State Variables**

| Variable                   | Type   | Purpose                                           |
| -------------------------- | ------ | ------------------------------------------------- |
| `platform-initialized`     | `bool` | Tracks platform setup state                       |
| `minimum-collateral-ratio` | `uint` | Required collateralization (default `150%`)       |
| `liquidation-threshold`    | `uint` | Safety threshold for liquidation (default `120%`) |
| `total-btc-locked`         | `uint` | Aggregated BTC collateral in the protocol         |
| `total-loans-issued`       | `uint` | Global loan counter                               |

---

#### 3. **Data Maps**

| Map                 | Keys                   | Values                                           | Description                  |
| ------------------- | ---------------------- | ------------------------------------------------ | ---------------------------- |
| `loans`             | `{loan-id: uint}`      | Loan metadata (borrower, collateral, rate, etc.) | Registry of all issued loans |
| `user-loans`        | `{user: principal}`    | `{active-loans: list uint}`                      | Tracks active loans per user |
| `collateral-prices` | `{asset: "BTC"/"STX"}` | `{price: uint}`                                  | Oracle-provided asset prices |

---

#### 4. **Public Entry Points**

| Function                       | Description                                            |
| ------------------------------ | ------------------------------------------------------ |
| `initialize-platform`          | One-time initialization by contract owner              |
| `update-collateral-ratio`      | Adjust system-wide collateral requirements             |
| `update-liquidation-threshold` | Adjust liquidation parameters                          |
| `update-price-feed`            | Update price oracle for supported assets               |
| `deposit-collateral`           | Record BTC collateral deposits                         |
| `request-loan`                 | Open a new collateralized debt position                |
| `repay-loan`                   | Repay outstanding loan + interest to unlock collateral |

---

#### 5. **Private Helpers**

| Helper                              | Role                                                            |
| ----------------------------------- | --------------------------------------------------------------- |
| `calculate-collateral-ratio`        | Determines live collateral coverage                             |
| `calculate-interest`                | Computes accrued interest per block                             |
| `check-liquidation`                 | Monitors collateral adequacy and triggers liquidation if needed |
| `liquidate-position`                | Marks undercollateralized positions as liquidated               |
| `is-valid-asset` / `is-valid-price` | Oracle input validation                                         |

---

## 🔄 **Protocol Flow**

### **1. Loan Lifecycle**

```text
┌────────────────────┐
│  Deposit Collateral │
└─────────┬──────────┘
          │
          ▼
┌────────────────────┐
│  Request Loan       │
│ (Overcollateralized)│
└─────────┬──────────┘
          │
          ▼
┌────────────────────┐
│  Loan Active        │
│ (Interest Accrues)  │
└─────────┬──────────┘
          │
   ┌──────┴───────┐
   │              │
   ▼              ▼
Repay Loan   Collateral Falls
(Unlock BTC) Below Threshold
   │              │
   ▼              ▼
  Done     Liquidation Triggered
```

---

## 🧩 **Example Configuration**

| Parameter                  | Default | Description                    |
| -------------------------- | ------- | ------------------------------ |
| `minimum-collateral-ratio` | `150%`  | Minimum collateral coverage    |
| `liquidation-threshold`    | `120%`  | Trigger for forced liquidation |
| `platform-fee-rate`        | `1%`    | Protocol-level fee on interest |

---

## 🔐 **Security Considerations**

* **No direct BTC custody** — integration depends on BTC-pegged or wrapped assets validated via Stacks.
* **Overcollateralization** — ensures solvency and stability under market volatility.
* **On-chain price oracle** — trusted feed required for real-time liquidation logic.
* **Immutable loan records** — all actions (loan creation, repayment, liquidation) are transparently logged.

---

## 🪙 **Future Integrations**

* Cross-chain BTC collateral verification (sBTC, DLCs)
* Synthetic stablecoin minting (BitUSD / BitMintUSD)
* DAO governance for parameter tuning
* Automated liquidation auctions

---

## 🧑‍💻 **Development & Deployment**

### **Requirements**

* Stacks CLI ≥ 2.5.0
* Clarity runtime (testnet or local Devnet)
* Access to a BTC oracle feed (mocked for testing)

### **Deploying**

```bash
clarinet contract deploy bitmint
```

### **Testing**

```bash
clarinet test
```

---

## 📜 **License**

MIT License © 2025 BitMint Protocol Contributors

---

## 💡 **Summary**

**BitMint** bridges Bitcoin’s pristine collateral with DeFi’s capital efficiency.
Through trust-minimized, on-chain logic written in **Clarity**, it offers a secure, transparent, and composable foundation for Bitcoin-based financial applications.
