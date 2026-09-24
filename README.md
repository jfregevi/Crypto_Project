# Uniswap V3 Liquidity Provision & Dynamic Delta-Hedging

Financial Engineering and decentralized market microstructure research project conducted at **EPFL** (M.Sc. in Financial Engineering — *Financial Applications of Blockchains and Distributed Ledgers*, 2026).

This repository examines concentrated liquidity dynamics in the **Uniswap V3 USDC/WETH 0.05%** pool (`0x88e6A0c2dDD26FEEb64F039a2c41296FcB3f5640`) across a six-month bear market (October 2025 – March 2026, $\sim -51\%$ ETH drop), and backtests a discrete delta-hedging strategy using **Hyperliquid** perpetual futures.

## 📁 Repository Structure

```
├── Crypto_Project.pdf        # Complete research report (Modules 1 to 5)
├── hedge_backtest.ipynb      # Final Delta-Hedging backtesting notebook (Module 5)
├── Images/                   # Generated charts and payoff visualisations
├── perp_prices.parquet       # Hourly OHLCV candle data (ETH-PERP on Hyperliquid)
├── funding_rates.parquet     # Historical hourly funding rate records
└── hedge_results.parquet     # Hourly backtest output for all 15 hedging variants
```

## 🔬 Research Summary

1. **On-Chain Data Extraction (Module 1):** Full reconstruction of contract state from deployment block using an Ethereum archive node, validating over 1,000,000 swap events and daily tick-level liquidity snapshots.
2. **Liquidity Distribution Analysis (Module 2):** TVL decomposition (*In-Range* vs. *Out-of-Range*) and liquidity concentration dynamics (ILR and L-HHI metrics).
3. **Execution Cost & Slippage Simulation (Module 3):** Implementation and validation of an exact tick-by-tick Uniswap V3 swap engine, price impact power-law regression, and effective spread drift correction.
4. **Liquidity Provision Analytics (Module 4):** Evaluation of 5 synthetic \$100,000 LP profiles ($\pm 0.1\%$ to *Full Range*). Highlights the paradox of concentrated liquidity: high fee leverage during in-range intervals, but rapid drift to 100% WETH and deep impermanent loss under adverse trends.
5. **Dynamic Delta Hedging (Module 5):** Proof of the LP position's structural short gamma exposure ($\Gamma_{LP} < 0$) and the equivalence $\Delta_{LP} = x(p)$. Backtest of a short perp hedge on Hyperliquid across 15 configurations (5 ranges $\times$ 3 rebalancing frequencies: 1h, 4h, 24h) factoring in 0.045% taker fees and funding rate cash flows.

## 📊 Key Findings

* **Tracking Precision vs. Fee Drag:** Frequent rebalancing (1h) minimizes residual impermanent loss ($\pm 15\%$), but execution costs and funding payments degrade net PnL. In contrast, 24h rebalancing yields the highest final net profit despite significant intra-day delta drift.
* **Economies of Scale:** Rebalancing fee drag severely impairs viability for retail-scale capital; a minimum portfolio size (e.g., \$100k+) is necessary for the strategy to be profitable.
* **Funding Tailwinds:** Despite ETH's prolonged bear market, perpetual funding rates remained predominantly positive due to persistent demand for leveraged longs, creating a net yield for the short hedger.

## 🚀 Quickstart

Install the required dependencies:

```bash
pip install pandas numpy matplotlib seaborn pyarrow requests
```

Open and run `hedge_backtest.ipynb` in your preferred Jupyter or VS Code environment.