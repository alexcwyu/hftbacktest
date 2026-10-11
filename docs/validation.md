# Validation evidence

Recorded 2026-10-12 on macOS arm64. Selected Python: **3.14.8**; uv0.13.0; native Cargo/Rust1.99.0. Commands below ran from the repository root. Exit124 means the documented time bound expired; exit139 means a native segmentation fault. An earlier failed attempt is retained even if a later rerun succeeds.

## Current scope and result

Complete Rust workspace:27unit tests and3doctests pass. Final CPython3.14 release wheel and source distribution build successfully after the latest all-group/all-extra dependency upgrade. Existing Rust examples and documentation now use current APIs; latency rows are supplied in memory. The full existing Python unittest was rerun in the final environment:1case,1error because tmp_20240501.npz is not present in this checkout. The unchanged test requires that market-data archive; no synthetic replacement or skip was introduced. Pytest additionally collects its Numba helper as a fixture-dependent function, so the native unittest command records the authoritative case.

Current recorded status: **blocked**. Interpreter fallback evidence and native prerequisites are described in [development](development.md). See [architecture](ayu-develop.md) for workflows, components, and existing source links.

## Executed commands

| Step | Exact command | Exit | Elapsed seconds | Evidence |
|---|---|---:|---:|---|
| python-sync | `uv sync --project py-hftbacktest --all-groups --all-extras --python 3.15` | 2 | 0.78 | [hftbacktest-python-sync.log](../../docs/maintenance-2026-10-12/hftbacktest-python-sync.log) |
| python314-sync | `uv sync --project py-hftbacktest --all-groups --all-extras --python 3.14.8` | 0 | 689.59 | [hftbacktest-python314-sync.log](../../docs/maintenance-2026-10-12/hftbacktest-python314-sync.log) |
| cargo-test | `cargo test --workspace` | 101 | 329.29 | [hftbacktest-cargo-test.log](../../docs/maintenance-2026-10-12/hftbacktest-cargo-test.log) |
| cargo-tests-fixed-builder | `cargo test --workspace` | 101 | 3.41 | [hftbacktest-cargo-tests-fixed-builder.log](../../docs/maintenance-2026-10-12/hftbacktest-cargo-tests-fixed-builder.log) |
| build | `uv build --project py-hftbacktest` | 0 | 479.76 | [hftbacktest-build.log](../../docs/maintenance-2026-10-12/hftbacktest-build.log) |
| cargo-tests-current-api | `cargo test --workspace` | 101 | 4.19 | [hftbacktest-cargo-tests-current-api.log](../../docs/maintenance-2026-10-12/hftbacktest-cargo-tests-current-api.log) |
| cargo-tests-fixed-examples | `cargo test --workspace` | 101 | 7.39 | [hftbacktest-cargo-tests-fixed-examples.log](../../docs/maintenance-2026-10-12/hftbacktest-cargo-tests-fixed-examples.log) |
| cargo-tests-current-examples | `cargo test --workspace` | 101 | 30.75 | [hftbacktest-cargo-tests-current-examples.log](../../docs/maintenance-2026-10-12/hftbacktest-cargo-tests-current-examples.log) |
| python-tests | `uv run --project py-hftbacktest --no-sync pytest py-hftbacktest/tests` | 1 | 5.43 | [hftbacktest-python-tests.log](../../docs/maintenance-2026-10-12/hftbacktest-python-tests.log) |
| python-unittest | `uv run --project py-hftbacktest --no-sync python -m unittest discover -s py-hftbacktest/tests` | 1 | 1.0 | [hftbacktest-python-unittest.log](../../docs/maintenance-2026-10-12/hftbacktest-python-unittest.log) |
| cargo-tests-current-docs | `cargo test --workspace` | 101 | 29.5 | [hftbacktest-cargo-tests-current-docs.log](../../docs/maintenance-2026-10-12/hftbacktest-cargo-tests-current-docs.log) |
| python-upgrade | `uv lock --project py-hftbacktest --upgrade --python 3.14.8` | 0 | 1.03 | [hftbacktest-python-upgrade.log](../../docs/maintenance-2026-10-12/hftbacktest-python-upgrade.log) |
| python-final-sync | `uv sync --project py-hftbacktest --all-groups --all-extras --python 3.14.8` | 0 | 606.87 | [hftbacktest-python-final-sync.log](../../docs/maintenance-2026-10-12/hftbacktest-python-final-sync.log) |
| cargo-tests-final | `cargo test --workspace` | 0 | 37.07 | [hftbacktest-cargo-tests-final.log](../../docs/maintenance-2026-10-12/hftbacktest-cargo-tests-final.log) |
| build-final | `uv build --project py-hftbacktest` | 0 | 388.28 | [hftbacktest-build-final.log](../../docs/maintenance-2026-10-12/hftbacktest-build-final.log) |
| python-unittest-latest | `uv run --project py-hftbacktest --no-sync python -m unittest discover -s py-hftbacktest/tests` | 1 | 0.98 | [hftbacktest-python-unittest-latest.log](../../docs/maintenance-2026-10-12/hftbacktest-python-unittest-latest.log) |

## Test summaries and failures

### cargo-test

Exit 101; elapsed 329.29seconds.

```text
   = note: `#[warn(dead_code)]` (part of `#[warn(unused)]`) on by default

warning: `hftbacktest` (example "gridtrading_live") generated 2 warnings (run `cargo fix --example "gridtrading_live" -p hftbacktest` to apply 1 suggestion)
error[E0599]: no associated function or constant named `builder` found for struct `LiveBot<CH, MD>` in the current scope
  --> hftbacktest/examples/logging_order_latency.rs:14:28
   |
14 |     let mut hbt = LiveBot::builder()
   |                            ^^^^^^^ associated function or constant not found in `LiveBot<_, _>`

For more information about this error, try `rustc --explain E0599`.
error: could not compile `hftbacktest` (example "logging_order_latency") due to 1 previous error
warning: build failed, waiting for other jobs to finish...
```

### cargo-tests-fixed-builder

Exit 101; elapsed 3.41seconds.

```text
  --> hftbacktest/examples/custom_evhandling.rs:24:15
   |
24 |     prelude::{Bot, Event, OrdType, Order, OrderId, Side, StateValues, TimeInForce},
   |               ^^^
   |
   = note: `#[warn(unused_imports)]` (part of `#[warn(unused)]`) on by default

For more information about this error, try `rustc --explain E0599`.
warning: `hftbacktest` (example "custom_evhandling") generated 1 warning
error: could not compile `hftbacktest` (example "custom_evhandling") due to 1 previous error; 1 warning emitted
warning: build failed, waiting for other jobs to finish...
warning: `hftbacktest` (example "live_order_error_handling") generated 2 warnings (run `cargo fix --example "live_order_error_handling" -p hftbacktest` to apply 1 suggestion)
```

### cargo-tests-current-api

Exit 101; elapsed 4.19seconds.

```text

   Compiling hftbacktest v0.9.4 (/Users/alex/workspaces/trading/hftbacktest/hftbacktest)
warning: `collector` (bin "collector" test) generated 4 warnings (run `cargo fix --bin "collector" -p collector --tests` to apply 1 suggestion)
error[E0599]: no method named `run` found for struct `LiveBot<CH, MD>` in the current scope
  --> hftbacktest/examples/gridtrading_live_bybit.rs:49:9
   |
49 |     hbt.run().unwrap();
   |         ^^^ method not found in `LiveBot<_, HashMapMarketDepth>`

For more information about this error, try `rustc --explain E0599`.
error: could not compile `hftbacktest` (example "gridtrading_live_bybit") due to 1 previous error
warning: build failed, waiting for other jobs to finish...
```

### cargo-tests-fixed-examples

Exit 101; elapsed 7.39seconds.

```text
68 |     maker_fee: f64,
   |     ^^^^^^^^^ help: if this is intentional, prefix it with an underscore: `_maker_fee`
   |
   = note: `#[warn(unused_variables)]` (part of `#[warn(unused)]`) on by default

warning: unused variable: `taker_fee`
  --> hftbacktest/examples/gridtrading_backtest_args.rs:69:5
   |
69 |     taker_fee: f64,
   |     ^^^^^^^^^ help: if this is intentional, prefix it with an underscore: `_taker_fee`

warning: `hftbacktest` (example "gridtrading_backtest_args") generated 2 warnings (run `cargo fix --example "gridtrading_backtest_args" -p hftbacktest` to apply 2 suggestions)
```

### cargo-tests-current-examples

Exit 101; elapsed 30.75seconds.

```text
test result: ok. 0 passed; 0 failed; 0 ignored; 0 measured; 0 filtered out; finished in 0.00s
test result: ok. 5 passed; 0 failed; 0 ignored; 0 measured; 0 filtered out; finished in 5.52s
test result: ok. 22 passed; 0 failed; 0 ignored; 0 measured; 0 filtered out; finished in 0.00s
test result: ok. 0 passed; 0 failed; 0 ignored; 0 measured; 0 filtered out; finished in 0.00s
test result: ok. 0 passed; 0 failed; 0 ignored; 0 measured; 0 filtered out; finished in 0.00s
test result: FAILED. 0 passed; 3 failed; 0 ignored; 0 measured; 0 filtered out; finished in 1.54s
```

### python-tests

Exit 1; elapsed 5.43seconds.

```text
E               FileNotFoundError: [Errno 2] No such file or directory: 'tmp_20240501.npz'
ERROR py-hftbacktest/tests/test_hftbacktest.py::test_run
FAILED py-hftbacktest/tests/test_hftbacktest.py::TestPyHftBacktest::test_run_backtest
```

### python-unittest

Exit 1; elapsed 1.0seconds.

```text
ERROR: test_run_backtest (test_hftbacktest.TestPyHftBacktest.test_run_backtest)
FileNotFoundError: [Errno 2] No such file or directory: 'tmp_20240501.npz'
Ran 1 test in 0.001s
```

### cargo-tests-current-docs

Exit 101; elapsed 29.5seconds.

```text
test result: ok. 0 passed; 0 failed; 0 ignored; 0 measured; 0 filtered out; finished in 0.00s
test result: ok. 5 passed; 0 failed; 0 ignored; 0 measured; 0 filtered out; finished in 5.52s
test result: ok. 22 passed; 0 failed; 0 ignored; 0 measured; 0 filtered out; finished in 0.01s
test result: ok. 0 passed; 0 failed; 0 ignored; 0 measured; 0 filtered out; finished in 0.00s
test result: ok. 0 passed; 0 failed; 0 ignored; 0 measured; 0 filtered out; finished in 0.00s
test result: FAILED. 2 passed; 1 failed; 0 ignored; 0 measured; 0 filtered out; finished in 1.13s
```

### cargo-tests-final

Exit 0; elapsed 37.07seconds.

```text
test result: ok. 0 passed; 0 failed; 0 ignored; 0 measured; 0 filtered out; finished in 0.00s
test result: ok. 5 passed; 0 failed; 0 ignored; 0 measured; 0 filtered out; finished in 5.51s
test result: ok. 22 passed; 0 failed; 0 ignored; 0 measured; 0 filtered out; finished in 0.00s
test result: ok. 0 passed; 0 failed; 0 ignored; 0 measured; 0 filtered out; finished in 0.00s
test result: ok. 0 passed; 0 failed; 0 ignored; 0 measured; 0 filtered out; finished in 0.00s
test result: ok. 3 passed; 0 failed; 0 ignored; 0 measured; 0 filtered out; finished in 0.04s
test result: ok. 0 passed; 0 failed; 0 ignored; 0 measured; 0 filtered out; finished in 0.00s
```

### python-unittest-latest

Exit 1; elapsed 0.98seconds.

```text
ERROR: test_run_backtest (test_hftbacktest.TestPyHftBacktest.test_run_backtest)
FileNotFoundError: [Errno 2] No such file or directory: 'tmp_20240501.npz'
Ran 1 test in 0.002s
```

## Synchronization

All upstream branch and tag refs were fetched by the workspace synchronization procedure. Fork updates use non-forcing pushes; upstream tags also have a private local namespace to preserve divergent tags. The default branch was merged into feature/ayu_develop, preserving the modern package layout and upstream source changes. The workspace synchronization JSON and log retain per-ref outcomes and merge history. This document does not claim that any feature branch was pushed.
