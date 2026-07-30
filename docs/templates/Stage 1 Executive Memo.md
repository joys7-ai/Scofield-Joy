# Pulled from [Stage 1 -- Executive memo](https://github.com/adamwstauffer/shidler/blob/main/courses/International-Finance-And-Securities/projects/fx-hedging/scenarios.md?plain=1)
Mine was Scenario 4
# 📘 Example Scenarios – EUR Receivables (USD Base, 1-Year Horizon)

### 📌 Quick Note on Setting the Spot FX Rate, Strike Price (K), and Interest Rates

When you see a placeholder like:
* **Put on EUR with (k =) [EURUSD]**
* **Call on EUR with (k =) [EURUSD]**
* **USD Interest Rate:** [n.nn%]
* **EUR Interest Rate:** [n.nn%]

…it means you are responsible for **determining the strike price (K) and interest rates** based on **current market data**.

**Use rates, prices, and quotes as of market close on the day you begin Stage 2.**

Here’s how to approach it:

1. **Look up the current EURUSD spot rate** (e.g., from Bloomberg, Yahoo Finance, or your preferred data source). Record the date and source.
2. Use this **spot rate as the starting point** for your strike price (K). In most basic hedge setups, the strike is set **at or near the current spot**.

---

### 📁 Scenario 4 – U.S. Aerospace Manufacturer

* **Receivable:** $20,000,000 receivable in 1 year
* **Spot:** EURUSD quote
* **Forward:** 1.0935 (maturity: 1 year from today)
* **USD Interest Rate:** [n.nn%]
* **EUR Interest Rate:** [n.nn%]
* **Option:**
  * Put on EUR with (k =) [EURUSD], premium = $0.019 per contract (no multiplier)
  * Call on EUR with (k =) [EURUSD], premium = $0.024 per contract (no multiplier)
