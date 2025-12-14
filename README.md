# Duel Duck Prediction Market 🏏

Duel Duck is a **prediction market protocol on Solana**, built with **Rust** and **Anchor**. Users can create and participate in simple **yes/no duels** (predictions) on real-world events — with a strong focus on crypto, sports, esports, and gaming.

Duel Duck aggregates collective belief into real-time probabilities using an on-chain automated market maker.

---

## ✨ Key Features

* Fully on-chain transparency
* Automatic smart contract payouts
* Creator commissions
* No hidden fees
* Fast & low-cost execution on Solana

---

## 🧠 Market Mechanism: LMSR

Duel Duck uses the **Logarithmic Market Scoring Rule (LMSR)** to provide:

* Continuous liquidity
* Dynamic pricing
* No need for order books or counterparties

---

## 📐 Core Formulas

### 1️⃣ Cost Function

```
C = b * ln(e^(q_yes / b) + e^(q_no / b))
```

### 2️⃣ YES Share Price

```
P_yes = e^(q_yes / b) / (e^(q_yes / b) + e^(q_no / b))
```

### 3️⃣ NO Share Price

```
P_no = 1 - P_yes
```

### Parameters

* `b` — Liquidity parameter (higher = lower price sensitivity)
* `q_yes` — Outstanding YES shares
* `q_no` — Outstanding NO shares

---

## 🏏 Trading Example

### *Will Real Madrid win in UEFA-2025?*

This example shows how probabilities change as users trade.

---

### Initial Market State (Rust)

```rust
let b: u64 = 1_000;            // Liquidity parameter
let mut q_yes: u128 = 100_000; // Outstanding YES shares
let mut q_no: u128 = 100_000;  // Outstanding NO shares
```

* Initial Probability: **P_yes = 0.5 (50%)**
* Market starts neutral because `q_yes == q_no`

---

### User 1 Buys YES

```rust
q_yes += 50_000; // New q_yes = 150_000
```

* New Probability: **P_yes ≈ 0.73 (73%)**
* Cost is calculated as the **difference in the LMSR cost function** before and after the trade.

---

### User 2 Buys NO

```rust
q_no += 75_000; // New q_no = 175_000
```

* New Probability: **P_yes ≈ 0.31 (31%)**

📌 **Insight**: The more shares bought in one direction, the higher the implied probability and the higher the marginal cost for additional shares.

---

## ⚔️ How Duel Duck Works (Solana Program)

### 1️⃣ Creating a Duel

Any user can create a new **yes/no prediction market** by submitting a Solana instruction with:

* Title & description
* Resolution timestamp
* Liquidity parameter `b`
* Optional tags (cricket, crypto, esports, etc.)

Markets are initialized with:

```rust
q_yes = q_no
```

This ensures **50/50 starting odds**.

---

### 2️⃣ Trading (Buy / Sell Shares)

* **Buy YES** → Increases `q_yes`
* **Buy NO** → Increases `q_no`
* **Sell** → Decreases user-held shares and refunds based on current LMSR pricing

All trades are:

* Atomic
* Fully on-chain
* Instantly reflected in prices

Users receive **fungible YES/NO SPL tokens**, unique to each duel.

---

### 3️⃣ Settlement

After the resolution time passes, the duel creator or an integrated oracle resolves the market:

```rust
resolve_duel(outcome: Yes | No)
```

* Winning shares redeem for **1 USDC (or configured SPL token)** each
* Losing shares are worth **0**

Users claim rewards via:

```rust
redeem()
```

Payouts are executed automatically by the Solana program using CPI transfers.

---

### 4️⃣ Creator Commissions

* Each duel defines a configurable commission rate
* A percentage of trading volume accrues to the creator
* Incentivizes high-quality, popular markets

---

## 📜 License

MIT License
