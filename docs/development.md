# Current maintained uv workflow

The selected complete Python environment is CPython 3.15.0. The complete 67-package graph builds current Databento DBN from licensed source, including the narrow PyO3 repr registration repair described below. The original 27 Rust unit cases and 3 doctests pass; the Python replay case still requires its missing original archive.

```sh
uv sync --project py-hftbacktest --all-groups --all-extras --frozen --python 3.15.0
uv build --project py-hftbacktest
uv run --project py-hftbacktest --no-sync python -m pytest py-hftbacktest/tests
cargo test --workspace --locked
```

The original Python unittest needs its exact `tmp_20240501.npz` input. Current builder methods preserve the original latency and capacity configuration; its unchanged Numba strategy compiles against the native-backed class, but complete replay remains unverified without that archive. See [validation](validation.md) and [source case](../py-hftbacktest/tests/test_hftbacktest.py).

## Retained upstream development reference

# HftBacktest — Development Guide

> **Last Updated**: 2026-04-07T00:00:00Z
> **Git Hash**: `a5e4a18`

## Setup

### Prerequisites

| Tool | Minimum Version | Purpose |
|---|---|---|
| Rust toolchain | 1.91.1 | Build Rust crates |
| Python | 3.11 | Python bindings and strategies |
| Maturin | 1.7+ | Build PyO3 Python extension |
| Numba | 0.61+ | JIT compilation of strategy functions |
| NumPy | 2.0–2.2 | Data arrays |

### Install Rust

```bash
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
# Ensure the required minimum version
rustup install 1.91.1
rustup default 1.91.1
```

### Install Python Bindings (Development)

```bash
# Clone the repository
git clone https://github.com/nkaz001/hftbacktest.git
cd hftbacktest

# Create and activate a virtual environment
python -m venv .venv
source .venv/bin/activate   # Linux/macOS
# .venv\Scripts\activate    # Windows

# Install build tools
pip install maturin

# Build and install the Python extension in editable mode
cd py-hftbacktest
maturin develop --release   # Release build (recommended for performance)
# OR
maturin develop             # Debug build (faster compile, slower runtime)
```

### Install from PyPI (End Users)

```bash
pip install hftbacktest
```

### Install Rust Crate Only

```bash
# Add to Cargo.toml
[dependencies]
hftbacktest = { version = "0.9.4", features = ["backtest", "live"] }

# Or build from source
cargo build --release --package hftbacktest
```

### Build the Connector (Live Trading)

```bash
cargo build --release --package connector
# Binary: target/release/connector
```

Run the connector:
```bash
./target/release/connector --name bf --connector binancefutures --config binancefutures.toml
```

## Project Structure

```
hftbacktest/                        # Workspace root
├── Cargo.toml                      # Workspace manifest (resolver v2)
├── README.rst                      # Project overview
├── ROADMAP.md                      # Planned features
│
├── hftbacktest/                    # Core Rust crate (crates.io: hftbacktest)
│   ├── Cargo.toml                  # version = "0.9.4", features: backtest/live/s3
│   └── src/
│       ├── lib.rs                  # Crate entry, feature flags
│       ├── prelude.rs              # Re-exports of common types
│       ├── types.rs                # Event, Order, Value, LiveError, etc.
│       ├── backtest/
│       │   ├── mod.rs              # MultiAssetMultiExchangeBacktest, builder, errors
│       │   ├── state.rs            # State<AT, FM>: position/balance/fee
│       │   ├── recorder.rs         # Snapshot recorder
│       │   ├── order.rs            # OrderBus (local↔exchange communication)
│       │   ├── assettype.rs        # LinearAsset / InverseAsset
│       │   ├── data/               # NpyDTyped reader, DataSource, S3 support
│       │   ├── models/
│       │   │   ├── latency.rs      # ConstantLatency, IntpOrderLatency
│       │   │   ├── queue.rs        # RiskAdverse, ProbQueue, L3FIFO models
│       │   │   └── fee.rs          # FlatPerTrade, TradingQty, TradingValue
│       │   ├── proc/
│       │   │   ├── mod.rs          # LocalProcessor + Processor traits
│       │   │   ├── local.rs        # Local processor impl
│       │   │   ├── nopartialfillexchange.rs
│       │   │   ├── partialfillexchange.rs
│       │   │   ├── l3_local.rs
│       │   │   └── l3_nopartialfillexchange.rs
│       │   └── evs.rs              # EventSet / EventIntentKind
│       ├── depth/
│       │   ├── mod.rs              # MarketDepth, L2MarketDepth, L3MarketDepth traits
│       │   ├── hashmapmarketdepth.rs
│       │   ├── roivectormarketdepth.rs
│       │   ├── btreemarketdepth.rs
│       │   └── fuse.rs
│       ├── live/                   # Live bot (feature = "live")
│       └── utils/
│
├── hftbacktest-derive/             # Procedural macro crate (NpyDTyped derive)
│
├── py-hftbacktest/                 # Python bindings crate
│   ├── Cargo.toml
│   ├── pyproject.toml              # Maturin config, version = "2.4.4"
│   ├── src/
│   │   ├── lib.rs                  # PyO3 module definition
│   │   ├── backtest.rs             # Backtest builder bindings
│   │   ├── depth.rs                # Depth type bindings
│   │   ├── order.rs                # Order type bindings
│   │   └── live.rs                 # Live bot bindings
│   └── hftbacktest/               # Python package
│       ├── __init__.py             # Public API, version = "2.4.4"
│       ├── binding.py              # Numba jitclass wrappers
│       ├── order.py                # Order jitclass + constants
│       ├── types.py                # Event flags + NumPy dtypes
│       ├── state.py                # StateValues access
│       ├── recorder.py             # Recorder Python wrapper
│       └── intrinsic.py
│
├── connector/                      # Live exchange connector binary
│   ├── Cargo.toml
│   └── src/
│       ├── main.rs
│       ├── connector.rs
│       ├── binancefutures/
│       ├── binancespot/
│       └── bybit/
│
└── collector/                      # Market data collector binary
    └── src/
```

## Configuration Reference

### BacktestAsset Builder (Python)

```python
from hftbacktest import BacktestAsset

asset = (
    BacktestAsset()

    # --- Data ---
    .data(["path/to/data.npz"])           # Feed data file(s) or ndarray(s)
    .initial_snapshot("snapshot.npz")     # Optional: start-of-day book snapshot

    # --- Asset Type ---
    .linear_asset(1.0)                    # Linear (value = qty * price * contract_size)
    .inverse_asset(1.0)                   # Inverse (value = qty / price * contract_size)

    # --- Latency Model ---
    .constant_latency(100_000, 100_000)   # entry_ns, response_ns
    .intp_order_latency(["latency.npz"])  # Historical interpolated latency

    # --- Queue / Fill Model ---
    .risk_adverse_queue_model()           # Conservative: fills only on trades
    .prob_queue_model(func)               # Probabilistic fill model
    .l3_fifo_queue_model()               # FIFO from L3 data

    # --- Exchange Model ---
    .no_partial_fill_exchange()           # Orders fill fully or not at all
    .partial_fill_exchange()             # Allows partial fills

    # --- Fee Model ---
    .trading_value_fee_model(-0.00005, 0.0007)  # maker_fee, taker_fee
    .flat_per_trade_fee_model(0.1)              # fixed fee per trade
)
```

### Connector Configuration (TOML)

```toml
# binancefutures.toml example
[exchange]
api_key = "YOUR_API_KEY"
api_secret = "YOUR_API_SECRET"
testnet = true

[[instruments]]
symbol = "btcusdt"
tick_size = 0.1
lot_size = 0.001
```

Run:
```bash
connector --name bf --connector binancefutures --config binancefutures.toml
```

### Cargo Feature Flags

| Feature | Default | Description |
|---|---|---|
| `backtest` | yes | Backtesting engine |
| `live` | yes | Live trading bot |
| `s3` | no | S3 data source support (requires `aws-sdk-s3`) |

Enable only backtest to minimize binary size:
```toml
hftbacktest = { version = "0.9.4", default-features = false, features = ["backtest"] }
```

## Testing

```bash
# Rust tests
cargo test --package hftbacktest
cargo test --package hftbacktest --all-features

# Python tests
cd py-hftbacktest
pip install -e ".[dev]"
pytest tests/

# Run a specific test
pytest tests/test_backtest.py -v
```

## Troubleshooting

### 1. Maturin build fails: "Rust version too old"

HftBacktest requires Rust 1.91.1 (2024 edition). Update with:
```bash
rustup update
rustup default stable
# Verify
rustc --version
```

### 2. `ImportError: No module named 'hftbacktest._hftbacktest'`

The Rust extension was not built. Run:
```bash
cd py-hftbacktest
maturin develop --release
```
If using a virtual environment, ensure it is activated before running maturin.

### 3. NumPy version conflict (`numpy >=2.0, <2.3` required)

Numba 0.61+ requires NumPy 2.x. Downgrade NumPy to a compatible version:
```bash
pip install "numpy>=2.0,<2.3"
```

### 4. Strategy runs slowly (JIT overhead on first call)

Numba compiles the `@njit` function on first invocation, which can take 10–60 seconds. This is one-time overhead per process. See the official docs on [JIT Compilation Overhead](../docs/jit_compilation_overhead.rst) for mitigation strategies (caching, AOT compilation).

### 5. `BacktestError::EndOfData` returned immediately

The feed data file could not be found or is empty. Verify:
```python
import os
assert os.path.exists("data/BTCUSDT_20240101.npz"), "Feed data file missing"
```
Data must be in `.npz` format with the `event_dtype` schema. See the [data preparation tutorial](https://hftbacktest.readthedocs.io/en/latest/tutorials/Data%20Preparation.html).

### 6. Order queue position never advances (no fills)

Using `risk_adverse_queue_model()` means orders fill only when trades occur at the order's price level. If the market never crosses your price, the order sits. Switch to `prob_queue_model` for a more optimistic fill simulation, or lower your limit price.

### 7. `iceoryx2` IPC errors in live mode

The connector and bot must use the same iceoryx2 service name and be run as the same user with sufficient shared memory permissions. On Linux, check:
```bash
ls /dev/shm/
# Ensure shared memory segments are visible
```

## Security Considerations

- **API keys**: Store exchange API keys in environment variables or a secrets manager, never in source code or committed TOML files.
- **Testnet first**: Both Binance Futures and Bybit connectors have testnet support. Always validate strategies on testnet before live deployment.
- **Risk controls**: HftBacktest does not include built-in position limits or kill switches. Implement max-position checks in your strategy logic.
- **IPC attack surface**: The iceoryx2 IPC channel between connector and bot is local-only (shared memory). Ensure the host is not shared with untrusted processes.

## See Also

- [architecture.md](architecture.md) — System components and module layout
- [workflow.md](workflow.md) — Event processing and order matching pipeline
- [state-management.md](state-management.md) — Order book, order lifecycle, position tracking
- [Official documentation](https://hftbacktest.readthedocs.io/)
- [Connector README](../connector/README.md)

## Maintained uv environment (2026-10-12)

The tested complete interpreter selection is Python 3.15.0. uv 0.13.0 resolves runtime dependencies, optional extras, development groups, and build isolation. `uv.lock` records exact versions; `--upgrade` refreshes them within the project and compatibility constraints. Review [validation evidence](validation.md) before treating a build or test command as successful on this host.

PyPI’s databento-dbn 0.70.0 wheels stop at Python 3.14. The Databento extra selects the licensed [official revision aa012adc](https://github.com/databento/dbn/tree/aa012adc6502e01b01d7b5c7836742dde21e93d8) retained in [vendor/databento-dbn](../vendor/databento-dbn/MAINTENANCE.md). Its full Rust workspace and original SDK source remain intact; [provenance](../vendor/databento-dbn/PROVENANCE.json) records the exact dependency and packaging changes. The owned HftBacktest extension uses current PyO3 0.29.3 with unchanged binding function bodies. No ABI-forward flag or relabeled wheel is used. The existing Python unittest still requires the absent `tmp_20240501.npz` market-data archive.

Python 3.15 deprecates duplicate native type slots. PyO3 simple enums register a generated repr and then the SDK custom repr, causing strict SDK import to panic. The licensed [PyO3 0.29.3 owner fix](../vendor/databento-dbn/vendor/pyo3/src/pyclass/create_type_object.rs) replaces only an existing `Py_tp_repr` pointer with the later pointer, preserving CPython’s previous effective last-slot-wins behavior. Every unrelated slot and SDK enum method/value remains unchanged. The SDK [root build metadata](../vendor/databento-dbn/pyproject.toml) explicitly includes that crate and both licenses in the source archive; a fresh source-archive-to-native-wheel build verifies portable ownership. Maturin uses the original active `[project] requires-python = ">=3.10"`; the unchanged unused Poetry `<3.15` tool table does not constrain that backend. See [Maturin’s source inclusion contract](https://www.maturin.rs/config).

### Layout and build ownership

The root Cargo workspace, its [verified native lock](../Cargo.lock), and py-hftbacktest maturin package are retained. Python commands use --project py-hftbacktest; this avoids flattening the native workspace or relocating its relative Rust path dependencies.

The native toolchain used for these checks is Rust 1.99.0 with Apple Command Line Tools. Make Cargo available with `export PATH=/opt/homebrew/opt/rustup/bin:$PATH`.

### Commands from the repository root

```sh
uv python install 3.15.0
uv lock --project py-hftbacktest --upgrade --python 3.15.0
uv sync --project py-hftbacktest --all-groups --all-extras --python 3.15.0
uv build --project py-hftbacktest
cargo test --workspace
uv run --project py-hftbacktest --no-sync python -m unittest discover -s py-hftbacktest/tests
```

### External inputs and test scope

Backtest datasets, Databento parsing wheels, live IPC components, and exchange connectors are separate runtime or native requirements. The Python package includes a Rust extension and NumPy/Numba integration, so an import-only source check is insufficient to validate the binding.

Existing upstream skips and xfails remain unchanged. Full suite failures, incomplete runs, and supplemental scoped checks are listed separately in validation. No new test cases were added. The architecture document links existing implementation files and explains the component contracts.

### Complete source build verification

The isolated and maintained full extras/development dependency graphs resolve 67 packages and install on Python 3.15. Real HftBacktest, Databento 0.87.0 and DBN 0.70.0 modules import with warnings treated as errors; the native asset builder accepts the original settings. The SDK decodes its own official MBO fixture through the real DBN reader. That SDK fixture is not substituted for the HftBacktest archive. The original unchanged strategy compiles against the real ROI Numba jitclass type without creating a pointer instance or fabricated market events. All variants of all 20 exported SDK enum classes retain byte-identical repr, str, name and value compared with the unpatched official build. The official two-row MBO fixture also decodes to identical dataframe values and dtypes. This confirms the binding’s compile contract; execution of the supplied Python case remains blocked at the missing original archive.

The supplemental unchanged upstream PyO3 regression scope separately passes all 56 cases: 7 class formatting, 24 enum and 25 protocol cases, with no ignored or filtered cases. It checks the exact repr-only patched runtime under the selected Python 3.15 interpreter. These are dependency regression controls, separate from the original HftBacktest 27 unit cases and 3 doctests.
