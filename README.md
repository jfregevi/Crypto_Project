# Dynamic Hedging of Impermanent Loss in Uniswap V3

This repository presents my contribution to a quantitative research project completed during my exchange semester at **EPFL** (*Financial Applications of Blockchains and Distributed Ledgers* course). 

While the broader team report ([`Crypto_Project.pdf`](./Crypto_Project.pdf)) covers other aspects of the pool, my work is focused on **Module 5**: the theoretical derivation of LP Greeks, building the data pipeline from Hyperliquid, and backtesting discrete delta-hedging strategies to mitigate Impermanent Loss (IL).

---

## Module 5

The implementation and analysis in [`hedge_backtest.ipynb`](./hedge_backtest.ipynb) cover:

### 1. Theoretical Greeks Derivation
* **LP Payoff Modeling:** Modeled the terminal payoff curve of concentrated liquidity positions as a short-gamma profile (analogous to a covered call / short put structure).
* **Delta Derivation:** Proved analytically that the position delta equals the exact physical token inventory held:
  $$\Delta_{LP}(p) = \frac{\partial V_{LP}}{\partial p} = x(p) = L \left( \frac{1}{\sqrt{p}} - \frac{1}{\sqrt{p_b}} \right)$$

* **Gamma Risk:** Derived the second derivative:
  $$\Gamma_{LP} = -\frac{L}{2p^{3/2}}$$
  confirming that narrower ranges require a much higher liquidity density ($L$), creating extreme local negative gamma risk.

### 2. Hyperliquid Market Data Pipeline
* Extracted hourly OHLCV perpetual price candles via the Hyperliquid REST API.
* Handled the 500-record API pagination limit to download continuous funding rate histories.
* Solved timestamp alignment issues (sub-second offsets) by flooring series to hourly intervals (`.dt.floor('h')`) to build a clean joint dataset.

### 3. Delta-Hedging Backtest & Results
* **Setup:** Simulated an overlay shorting $\vert{}\Delta_{LP}\vert{}$ ETH on Hyperliquid perpetuals for 5 representative ranges (P1 to P5) across 3 rebalancing frequencies (1h, 4h, 24h) over a period where ETH dropped ~53%.
* **Key Findings:**
  * **Frequency vs. Slippage Trade-off:** High-frequency rebalancing (1h) minimizes residual IL drift but suffers severe fee drag (0.045% taker fee) and funding drag.
  * **Optimal Net PnL:** For narrower positions, a 24h rebalancing interval yielded the highest Net Hedge PnL (up to ~$43k on a $100k notional), proving that transaction cost management outweighs micro-drift protection.
  * **Limits of Linear Hedging:** Perpetual futures neutralize first-order directional exposure ($\Delta$) but leave the structural negative $\Gamma$ unhedged when spot exits the active range.

---

## Repository Structure

```text
├── hedge_backtest.ipynb      # Notebook of module 5 (Greeks, API pipeline, backtest)
├── Crypto_Project.pdf        # Joint final research report
├── Images/                   # Generated payoff and backtest performance plots
├── perp_prices.parquet       # Hourly ETH perpetual prices (Hyperliquid)
├── funding_rates.parquet     # Hourly funding rates (Hyperliquid)
└── hedge_results.parquet     # Hourly PnL and residual risk backtest outputs
```

---

## Quick Start

```bash
pip install pandas numpy matplotlib seaborn pyarrow requests