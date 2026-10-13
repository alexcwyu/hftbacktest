# Validation evidence

<!-- current-documentation-provenance -->
Documentation reviewed: 2026-10-13 / Git source base: `88fb0ea437128d75a7c33e12e9bb4b2323b8e219` / [Current architecture and feature guide](ayu-develop.md).
Historical execution commands/results below retain their original dates and scope; this metadata update is not a new application test run.
<!-- /current-documentation-provenance -->



Current campaign decision: the user explicitly excludes the remaining failing tests from completion requirements. Historical failures below remain failures; they are not relabeled as passes. Source/build/tooling/documentation requirements remain in scope.

Recorded 2026-10-12 on macOS arm64. Selected Python: **3.15.0**; uv 0.13.0; native Cargo/Rust 1.99.0. Commands below ran from the repository root. Exit124 means the documented time bound expired; exit139 means a native segmentation fault. An earlier failed attempt is retained even if a later rerun succeeds.

## Current scope and result

The maintained Python 3.15 complete graph resolves 67 packages and frozen synchronization passes. The native HftBacktest release wheel and source distribution build; strict source compilation, actual Hft/Databento/DBN imports, original native asset settings and the unchanged original strategy's Numba compilation pass. The original Rust workspace passes **27 unit cases and 3 doctests**. The single original Python replay case still fails at the absent exact `tmp_20240501.npz` archive. No different data, synthetic events or skip replaces that case.

Current replay status remains **blocked by the missing original archive**. The former Python 3.15 PyPI artifact gap is resolved by the licensed source build described in [development](development.md) and [SDK provenance](../vendor/databento-dbn/PROVENANCE.json). The source is official DBN 0.70.0 revision aa012adc, with original SDK enum/record/encoding/decoding bodies intact. Current PyO3 0.29.3 has a narrowly scoped `Py_tp_repr` registration fix preserving the previous last-slot-wins result; strict imports no longer panic on the duplicate-slot deprecation.

All variants of all 20 exported SDK enum classes retain identical repr, str, name and value. The genuine official two-row MBO fixture decodes to identical dataframe values/dtypes. That SDK fixture is never used as the missing Hft archive. A fresh SDK source archive installs in a separate Python 3.15 environment, and its exact patched runtime, licenses and provenance match the maintained source. Maturin packages the required SDK workspace members (`python`, `rust/dbn-macros`, `rust/dbn`); the complete original workspace, including C/CLI projects, is retained in the checkout.

The supplemental unchanged upstream PyO3 scope passes **56 cases** separately: 7 class formatting, 24 enum and 25 protocol cases; none ignored or filtered. The patch hash matches that reviewed runtime. These dependency controls do not add cases to the original Hft suite.

## Current Python 3.15 verification

Commands run from the repository root. Complete command arrays, including the inline strict runtime control and its real fixture/source paths, are preserved in the [portable check records](maintenance/index.html). A duration marked unknown was not measured and is not reconstructed.

| Scope | Command or recorded control | Exit | Wall seconds |
|---|---|---:|---:|
| Complete frozen graph | `uv sync --project py-hftbacktest --all-extras --all-groups --frozen --python 3.15.0` | 0 | 0.02 |
| Current lock | `uv lock --project py-hftbacktest --check` | 0 | 0.02 |
| Native Hft wheel/source archive | `uv build --project py-hftbacktest --python 3.15.0` | 0 | 236.15 |
| Strict source compile | `py-hftbacktest/.venv/bin/python -Werror -m compileall -q -f py-hftbacktest/hftbacktest py-hftbacktest/tests vendor/databento-dbn/python/python` | 0 | 0.06 |
| Real imports, original strategy JIT, enum/MBO control | Recorded `python -Werror -c` control using owned source and official fixture | 0 | 2.25 |
| Original Python case | `py-hftbacktest/.venv/bin/python -m pytest py-hftbacktest/tests/test_hftbacktest.py -q` | 1 | 0.29 |
| Original native scope | `cargo test --workspace --locked`, selected Python 3.15 binding interpreter | 0 | unknown |
| Portable SDK native wheel/source archive | `uv build --project vendor/databento-dbn --python 3.15.0 --out-dir /tmp/hftbacktest-dbn-final315-dist` | 0 | 46.10 |
| Upstream PyO3 existing regressions | Recorded isolated `cargo test` scope: `test_enum`, `test_class_formatting`, `test_proto_methods` | 0 | 50.75 |

## Historical attempts and earlier fallback

Earlier failed attempts and the Python 3.14 fallback below are retained as history. They do not describe the selected complete Python 3.15 source environment.

## Historical executed commands

| Step | Exact command | Exit | Elapsed seconds | Evidence |
|---|---|---:|---:|---|
| python-sync | `uv sync --project py-hftbacktest --all-groups --all-extras --python 3.15` | 2 | 0.78 | [check record](maintenance/index.html) |
| python314-sync | `uv sync --project py-hftbacktest --all-groups --all-extras --python 3.14.8` | 0 | 689.59 | [check record](maintenance/index.html) |
| cargo-test | `cargo test --workspace` | 101 | 329.29 | [check record](maintenance/index.html) |
| cargo-tests-fixed-builder | `cargo test --workspace` | 101 | 3.41 | [check record](maintenance/index.html) |
| build | `uv build --project py-hftbacktest` | 0 | 479.76 | [check record](maintenance/index.html) |
| cargo-tests-current-api | `cargo test --workspace` | 101 | 4.19 | [check record](maintenance/index.html) |
| cargo-tests-fixed-examples | `cargo test --workspace` | 101 | 7.39 | [check record](maintenance/index.html) |
| cargo-tests-current-examples | `cargo test --workspace` | 101 | 30.75 | [check record](maintenance/index.html) |
| python-tests | `uv run --project py-hftbacktest --no-sync pytest py-hftbacktest/tests` | 1 | 5.43 | [check record](maintenance/index.html) |
| python-unittest | `uv run --project py-hftbacktest --no-sync python -m unittest discover -s py-hftbacktest/tests` | 1 | 1.0 | [check record](maintenance/index.html) |
| cargo-tests-current-docs | `cargo test --workspace` | 101 | 29.5 | [check record](maintenance/index.html) |
| python-upgrade | `uv lock --project py-hftbacktest --upgrade --python 3.14.8` | 0 | 1.03 | [check record](maintenance/index.html) |
| python-final-sync | `uv sync --project py-hftbacktest --all-groups --all-extras --python 3.14.8` | 0 | 606.87 | [check record](maintenance/index.html) |
| cargo-tests-final | `cargo test --workspace` | 0 | 37.07 | [check record](maintenance/index.html) |
| build-final | `uv build --project py-hftbacktest` | 0 | 388.28 | [check record](maintenance/index.html) |
| python-unittest-latest | `uv run --project py-hftbacktest --no-sync python -m unittest discover -s py-hftbacktest/tests` | 1 | 0.98 | [check record](maintenance/index.html) |

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

## Python case compatibility follow-up

The original case still requires `tmp_20240501.npz` and fails with FileNotFoundError. A focused real builder probe exposed a masked removed method: `trade_len` is now `last_trades_capacity`; `constant_latency` is deprecated in favor of `constant_order_latency`. The existing case uses the current methods with the exact same capacity 1000 and latency values (100, 100). The native implementations set the same model fields.

The Numba strategy helper is named `run_strategy` and remains called by the original `TestPyHftBacktest.test_run_backtest`. This avoids pytest mistaking its required native-backtest argument for a fixture. Collection now selects exactly the one original unittest case. The entire helper body is AST-identical, and the full module is AST-identical after these three name migrations; no data, parameters or assertions change. The real original strategy compiles against the actual native-backed Numba class without constructing a pointer instance or substituting market events. The strict builder configuration probe also passes. These compilation checks do not prove a successful data replay.

The earlier PyPI-only full-dependency Python 3.15 control exited 2 because `databento-dbn==0.70.0` has no CPython 3.15 PyPI artifact or PyPI source distribution. Python 3.14 then resolved the latest 67-package graph without a lock change. The maintained official licensed source build and repr-slot owner fix described above now resolve that distribution gap on Python 3.15; the earlier failure remains recorded.

The required archive was not found by the recorded workspace/runtime search. The [official data guide](https://hftbacktest.readthedocs.io/en/latest/data.html) explains its event schema and timestamp ordering; it does not establish the identity of this missing test archive. No different instrument, date or manufactured feed is substituted.

## Original replay input recovery audit

The unchanged single original Python replay node still fails at the absent `tmp_20240501.npz` (exit 1, 0.47 seconds). Author history and all 41 published releases do not identify or supply this archive. Tardis documents keyless first-of-month CSVs, but actual GET controls, including its documented sample, return Cloudflare HTTP 403 from this environment. This is an input-provenance gap and observed publisher access failure; it is not evidence that an API key is inherently required or that the data is permanently unavailable. See [source-linked recovery diagnosis](maintenance/original-replay-input.md) and [exact original-case record](maintenance/replay-original-case-terminal.json). No input, case or assertion was substituted.
