# HftBacktest

> **Last Updated**: 2026-04-07T00:00:00Z
> **Git Hash**: `a5e4a18`

A high-frequency trading backtesting framework written in Rust with Python bindings via PyO3/Maturin. Designed for developing and validating HFT and market-making strategies with tick-level accuracy.

## Overview

HftBacktest focuses on what most backtesting frameworks ignore: **latency**. Both feed latency (the delay between a market event occurring and your strategy seeing it) and order latency (the round-trip delay for order submission and response) are modeled explicitly. Order fills account for queue position in the limit order book, not just price crossing.

**Key Points**:
- Target audience: HFT researchers, quantitative market makers, crypto algorithmic traders
- Primary focus: Market making, grid trading, latency-sensitive strategies
- Supports: Binance Futures, Bybit Futures (live); any exchange with tick data (backtest)
- Language stack: Rust core (`hftbacktest` crate) + Python bindings (`py-hftbacktest`)

## Key Features

| Feature | Details |
|---|---|
| Tick-by-tick simulation | Nanosecond-resolution event replay from L2/L3 order book data |
| Feed latency modeling | Configurable delay between exchange timestamp and local receipt |
| Order latency modeling | Constant or historically interpolated entry/response latency |
| Queue position tracking | Probabilistic and FIFO-based fill models |
| Order book reconstruction | L2 (Market-By-Price) and L3 (Market-By-Order) feeds |
| Multi-asset backtesting | Simultaneous simulation across assets and exchanges |
| Live trading | Same algorithm code runs in live mode (Rust only) |
| Python/Numba JIT | Strategy code runs inside `@njit` compiled functions |
| S3 data access | Optional feature flag for loading tick data from AWS S3 |
| Partial fills | `PartialFillExchange` model for realistic fill simulation |

## Quick Start

Install from PyPI (Python 3.11+):

```bash
pip install hftbacktest
```

A minimal market-making strategy backtest:

```python
from numba import njit
import numpy as np
from hftbacktest import BacktestAsset, HashMapMarketDepthBacktest, BUY, SELL, GTX, LIMIT

@njit
def market_making_algo(hbt):
    asset_no = 0
    while hbt.elapse(10_000_000) == 0:   # step 10ms
        hbt.clear_inactive_orders(asset_no)
        depth = hbt.depth(asset_no)
        tick_size = depth.tick_size
        lot_size = depth.lot_size

        mid_price = (depth.best_bid + depth.best_ask) / 2.0
        half_spread = 0.0005 * mid_price

        bid_price = mid_price - half_spread
        ask_price = mid_price + half_spread

        bid_tick = int(np.round(bid_price / tick_size))
        ask_tick = int(np.round(ask_price / tick_size))
        order_qty = np.round(100.0 / mid_price / lot_size) * lot_size

        hbt.submit_buy_order(asset_no, bid_tick, bid_tick * tick_size, order_qty, GTX, LIMIT, False)
        hbt.submit_sell_order(asset_no, ask_tick, ask_tick * tick_size, order_qty, GTX, LIMIT, False)

    return True


asset = (
    BacktestAsset()
    .data(["data/BTCUSDT_20240101.npz"])
    .linear_asset(1.0)
    .constant_latency(100_000, 100_000)   # 100us feed + order latency
    .risk_adverse_queue_model()
    .no_partial_fill_exchange()
    .trading_value_fee_model(-0.00005, 0.0007)
)

hbt = HashMapMarketDepthBacktest([asset])
market_making_algo(hbt)
hbt.close()
```

## Architecture Summary

HftBacktest separates concerns into two processors that advance through the same event stream independently:

- **Local processor** — represents your strategy's view: it sees events after feed latency, and receives order responses after order latency.
- **Exchange processor** — represents the exchange: it matches orders using the queue model at exchange timestamps.

The backtest engine merges these two timelines into a unified event loop. At each tick, it dispatches the next event to whichever processor's clock is earliest.

See [architecture.md](architecture.md) for the full system diagram.

## Documentation Index

| Document | Contents |
|---|---|
| [architecture.md](architecture.md) | System architecture, component overview, module diagram |
| [workflow.md](workflow.md) | Backtesting pipeline, order matching flow, latency simulation |
| [state-management.md](state-management.md) | Order book state, order lifecycle, position tracking |
| [development.md](development.md) | Setup, project structure, config reference, troubleshooting |

## Source References

- Rust crate entry point: [`hftbacktest/src/lib.rs`](../hftbacktest/src/lib.rs)
- Python package entry: [`py-hftbacktest/hftbacktest/__init__.py`](../py-hftbacktest/hftbacktest/__init__.py)
- Workspace manifest: [`Cargo.toml`](../Cargo.toml)
- Python package manifest: [`py-hftbacktest/pyproject.toml`](../py-hftbacktest/pyproject.toml)
- Connector README: [`connector/README.md`](../connector/README.md)
