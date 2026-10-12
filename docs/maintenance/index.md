# hftbacktest: portable verification records

These records retain exact commands, outcomes, original-log hashes and available source snapshots. Failed, interrupted and earlier exploratory commands remain visible. A later focused pass proves only its own scope.

[Machine-readable records](verification.json) · [Validation](../validation.md) · [Engineering guide](../ayu-develop.md)

Original logs remain in the workspace maintenance evidence. Tracked source hashes exclude untracked additions and later edits, as stated in each captured snapshot.

| Check | Exit | Duration | Command |
| --- | ---: | ---: | --- |
| build-final | 0 | 388.28s | `uv build --project py-hftbacktest` |
| build | 0 | 479.76s | `uv build --project py-hftbacktest` |
| cargo-test | 101 | 329.29s | `cargo test --workspace` |
| cargo-tests-current-api | 101 | 4.19s | `cargo test --workspace` |
| cargo-tests-current-docs | 101 | 29.5s | `cargo test --workspace` |
| cargo-tests-current-examples | 101 | 30.75s | `cargo test --workspace` |
| cargo-tests-final | 0 | 37.07s | `cargo test --workspace` |
| cargo-tests-fixed-builder | 101 | 3.41s | `cargo test --workspace` |
| cargo-tests-fixed-examples | 101 | 7.39s | `cargo test --workspace` |
| current-original-python-suite-real-fixture-status | 1 | 0.35s | `uv run --project py-hftbacktest --no-sync python -m pytest py-hftbacktest/tests --tb=short` |
| current-original-unittest-pytest-collection | 0 | 0.48s | `uv run --project py-hftbacktest --no-sync python -m pytest py-hftbacktest/tests --collect-only` |
| current-python-builder-api-probe | 0 | 0.21s | `uv run --project py-hftbacktest --no-sync python -W error -c from hftbacktest import BacktestAsset; a=BacktestAsset().linear_asset(1.0).data(["tmp_20240501.npz"]).no_partial_fill_exchange().constant_order_latency(100,100).power_prob_queue_model3(3.0).tick_size(0.000001).lot_size(1.0).last_trades_capacity(1000).roi_lb(0.0).roi_ub(1.0); print(type(a).__name__, "complete original builder configuration accepted")` |
| original-python-builder-api-probe | 1 | 0.58s | `uv run --project py-hftbacktest --no-sync python -c from hftbacktest import BacktestAsset; a=BacktestAsset().linear_asset(1.0).data(["tmp_20240501.npz"]).no_partial_fill_exchange().constant_latency(100,100).power_prob_queue_model3(3.0).tick_size(0.000001).lot_size(1.0); print("last_trades_capacity",hasattr(a,"last_trades_capacity")); a.trade_len(1000)` |
| python-final-sync | 0 | 606.87s | `uv sync --project py-hftbacktest --all-groups --all-extras --python 3.14.8` |
| python-sync | 2 | 0.78s | `uv sync --project py-hftbacktest --all-groups --all-extras --python 3.15` |
| python-tests | 1 | 5.43s | `uv run --project py-hftbacktest --no-sync pytest py-hftbacktest/tests` |
| python-unittest-latest | 1 | 0.98s | `uv run --project py-hftbacktest --no-sync python -m unittest discover -s py-hftbacktest/tests` |
| python-unittest | 1 | 1.0s | `uv run --project py-hftbacktest --no-sync python -m unittest discover -s py-hftbacktest/tests` |
| python-upgrade | 0 | 1.03s | `uv lock --project py-hftbacktest --upgrade --python 3.14.8` |
| python314-sync | 0 | 689.59s | `uv sync --project py-hftbacktest --all-groups --all-extras --python 3.14.8` |
| refreshed-full-python315-dependency-sync | 2 | 0.55s | `uv sync --all-groups --all-extras --python 3.15 --no-install-project` |
| refreshed-latest-compatible-python314-lock | 0 | 0.19s | `uv lock --project py-hftbacktest --upgrade --python 3.14.8` |
| unchanged-strategy-native-numba-compile | 0 | 1.62s | `uv run --project py-hftbacktest --no-sync python -c import importlib.util; from hftbacktest.binding import ROIVectorMarketDepthBacktest_; spec=importlib.util.spec_from_file_location("original_case", "py-hftbacktest/tests/test_hftbacktest.py"); module=importlib.util.module_from_spec(spec); spec.loader.exec_module(module); module.run_strategy.compile((ROIVectorMarketDepthBacktest_.class_type.instance_type,)); print("Original strategy compiled against the actual native-backed Numba class; no market data or pointer instance constructed.")` |
