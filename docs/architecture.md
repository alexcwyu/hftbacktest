# HftBacktest — Architecture

> **Last Updated**: 2026-04-07T00:00:00Z
> **Git Hash**: `a5e4a18`

## System Architecture Overview

```mermaid
graph TB
    subgraph Python["Python Layer (py-hftbacktest)"]
        PyInit["BacktestAsset builder"]
        PyAPI["HashMapMarketDepthBacktest / ROIVectorMarketDepthBacktest"]
        Numba["@njit Strategy Function"]
    end

    subgraph Rust["Rust Core (hftbacktest crate)"]
        subgraph Backtest["backtest module"]
            Builder["MultiAssetMultiExchangeBacktest builder"]
            EventLoop["Event Loop (elapse / wait_order_response)"]
            Local["Local Processor"]
            Exchange["Exchange Processor"]
            OrderBus["Order Bus"]
        end

        subgraph Depth["depth module"]
            HashMapDepth["HashMapMarketDepth (L2)"]
            ROIVecDepth["ROIVectorMarketDepth (L2)"]
            BTreeDepth["BTreeMarketDepth (L2)"]
            FusedDepth["FusedHashMapMarketDepth"]
            L3Depth["L3MarketDepth trait"]
        end

        subgraph Models["models"]
            ConstantLat["ConstantLatency"]
            IntpLat["IntpOrderLatency"]
            QueueModels["QueueModel (Risk-Adverse / ProbQueue / L3FIFO)"]
            FeeModels["FeeModel (Flat / TradingQty / TradingValue)"]
        end

        subgraph Data["data"]
            Reader["NpyDTyped Reader (.npz)"]
            S3["S3 Source (optional feature)"]
            Preprocess["FeedLatencyAdjustment"]
        end

        State["State (position / balance / fee)"]
        Recorder["Recorder (P&L snapshots)"]
    end

    subgraph Live["live module (Rust-only)"]
        LiveBot["LiveBot"]
        Connector["Connector process (IPC via iceoryx2)"]
        BinanceFut["BinanceFutures connector"]
        Bybit["Bybit connector"]
    end

    PyInit -->|"Rust FFI (PyO3)"| Builder
    PyAPI -->|"wraps pointer"| EventLoop
    Numba -->|"calls via ctypes/cffi"| PyAPI

    Builder --> Local
    Builder --> Exchange
    Builder --> EventLoop
    EventLoop --> Local
    EventLoop --> Exchange
    Local <-->|"order requests/responses"| OrderBus
    Exchange <-->|"order fills"| OrderBus
    Local --> Depth
    Exchange --> Depth
    Exchange --> Models
    Local --> Models
    Local --> State
    State --> Recorder

    Data --> Local
    Data --> Exchange

    LiveBot -->|"IPC"| Connector
    Connector --> BinanceFut
    Connector --> Bybit
```

## Trading Paradigm and Key Features

| Paradigm | Description |
|---|---|
| **Tick-by-tick replay** | Every market event is replayed in nanosecond order; no bar aggregation |
| **Dual-timeline simulation** | Separate local and exchange clocks; events dispatched by earliest timestamp |
| **Explicit latency modeling** | Feed delay and order round-trip delay are first-class parameters |
| **Queue-aware fills** | Fill probability depends on queue position, not just price crossing |
| **L2 / L3 support** | Market-By-Price (level 2) and Market-By-Order (level 3) reconstruction |
| **Multi-asset** | Multiple assets and exchanges run in the same simulation session |
| **Live parity** | Same algorithm code is executed in both backtest and live modes |
| **JIT compatibility** | Python strategy functions compile with Numba `@njit` |
| **Fee models** | Flat-per-trade, trading-quantity, and trading-value fee structures |
| **Partial fills** | Optional `PartialFillExchange` model for realistic queue behavior |

## Core Components

### Backtest Engine

The central struct is `MultiAssetMultiExchangeBacktest` (built via `build_hashmap_backtest` / `build_roivec_backtest`). It owns a vector of `(Local, Exchange)` processor pairs, one per asset.

**Source references**:
- Builder and engine: [`hftbacktest/src/backtest/mod.rs`](../hftbacktest/src/backtest/mod.rs)
- Local processor trait: [`hftbacktest/src/backtest/proc/mod.rs`](../hftbacktest/src/backtest/proc/mod.rs)
- Local implementation: [`hftbacktest/src/backtest/proc/local.rs`](../hftbacktest/src/backtest/proc/local.rs)
- Exchange (no partial fill): [`hftbacktest/src/backtest/proc/nopartialfillexchange.rs`](../hftbacktest/src/backtest/proc/nopartialfillexchange.rs)
- Exchange (partial fill): [`hftbacktest/src/backtest/proc/partialfillexchange.rs`](../hftbacktest/src/backtest/proc/partialfillexchange.rs)

### Market Depth Implementations

Four concrete implementations of the `MarketDepth` trait are provided:

| Implementation | Use Case | Source |
|---|---|---|
| `HashMapMarketDepth` | General purpose L2 | [`depth/hashmapmarketdepth.rs`](../hftbacktest/src/depth/hashmapmarketdepth.rs) |
| `ROIVectorMarketDepth` | Cache-friendly, region-of-interest | [`depth/roivectormarketdepth.rs`](../hftbacktest/src/depth/roivectormarketdepth.rs) |
| `BTreeMarketDepth` | Ordered iteration over levels | [`depth/btreemarketdepth.rs`](../hftbacktest/src/depth/btreemarketdepth.rs) |
| `FusedHashMapMarketDepth` | Fused depth from multiple sources | [`depth/fuse.rs`](../hftbacktest/src/depth/fuse.rs) |

The `L2MarketDepth` and `L3MarketDepth` traits extend the base `MarketDepth` trait.

**Source**: [`hftbacktest/src/depth/mod.rs`](../hftbacktest/src/depth/mod.rs)

### Latency Models

| Model | Description | Source |
|---|---|---|
| `ConstantLatency` | Fixed nanosecond entry and response delay | [`models/latency.rs`](../hftbacktest/src/backtest/models/latency.rs) |
| `IntpOrderLatency` | Interpolated from historical latency `.npz` data | [`models/latency.rs`](../hftbacktest/src/backtest/models/latency.rs) |

### Queue / Fill Models

| Model | Description |
|---|---|
| `RiskAdverseQueueModel` | Queue advances only on trades at the same price (conservative) |
| `ProbQueueModel` | Probability-based fill using configurable functions (LogProb, PowerProb) |
| `L3FIFOQueueModel` | True FIFO queue from L3 order book feed |

**Source**: [`hftbacktest/src/backtest/models/queue.rs`](../hftbacktest/src/backtest/models/queue.rs)

### Fee Models

| Model | Description |
|---|---|
| `FlatPerTradeFeeModel` | Fixed fee per trade |
| `TradingQtyFeeModel` | Fee proportional to quantity |
| `TradingValueFeeModel` | Maker/taker fee proportional to notional value |

**Source**: [`hftbacktest/src/backtest/models/fee.rs`](../hftbacktest/src/backtest/models/fee.rs)

### Python Bindings

The Python layer is built with PyO3 and Maturin. Rust structs are exposed as Python objects. Strategy-visible types (Order, depth methods) are wrapped with Numba `@jitclass` for use inside `@njit` functions.

**Source references**:
- PyO3 bindings: [`py-hftbacktest/src/lib.rs`](../py-hftbacktest/src/lib.rs)
- Numba class wrappers: [`py-hftbacktest/hftbacktest/binding.py`](../py-hftbacktest/hftbacktest/binding.py)
- Order jitclass: [`py-hftbacktest/hftbacktest/order.py`](../py-hftbacktest/hftbacktest/order.py)
- Event and dtype definitions: [`py-hftbacktest/hftbacktest/types.py`](../py-hftbacktest/hftbacktest/types.py)

## Module Diagram

```mermaid
graph LR
    subgraph hftbacktest_crate["hftbacktest crate"]
        lib["lib.rs"]
        prelude["prelude.rs"]
        types["types.rs"]
        backtest_mod["backtest/mod.rs"]
        depth_mod["depth/mod.rs"]
        live_mod["live/ (feature=live)"]

        backtest_mod --> models_mod["backtest/models/"]
        backtest_mod --> proc_mod["backtest/proc/"]
        backtest_mod --> state_mod["backtest/state.rs"]
        backtest_mod --> data_mod["backtest/data/"]
        backtest_mod --> recorder_mod["backtest/recorder.rs"]
        backtest_mod --> order_mod["backtest/order.rs"]

        lib --> backtest_mod
        lib --> depth_mod
        lib --> live_mod
        lib --> types
        lib --> prelude
    end

    subgraph py_hftbacktest["py-hftbacktest crate"]
        pysrc["src/lib.rs"]
        pyinit["hftbacktest/__init__.py"]
        pybind["hftbacktest/binding.py"]
        pyorder["hftbacktest/order.py"]
        pytypes["hftbacktest/types.py"]
        pystate["hftbacktest/state.py"]
        pyrecorder["hftbacktest/recorder.py"]
    end

    subgraph connector_crate["connector crate"]
        conn_main["src/main.rs"]
        binance["src/binancefutures/"]
        bybit_conn["src/bybit/"]
    end

    subgraph collector_crate["collector crate"]
        coll_src["src/"]
    end

    py_hftbacktest -->|"depends on"| hftbacktest_crate
    connector_crate -->|"depends on"| hftbacktest_crate
```

## See Also

- [workflow.md](workflow.md) — How events flow through the backtest pipeline
- [state-management.md](state-management.md) — Order book and position state lifecycle
- [development.md](development.md) — Build setup and configuration
