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
| final-selected315-original-workspace-tests | 0 | Nones | `cargo test --workspace --locked` |
| hftbacktest-final315-complete-frozen-sync | 0 | 0.02s | `uv sync --project py-hftbacktest --all-extras --all-groups --frozen --python 3.15.0` |
| hftbacktest-final315-lock-check | 0 | 0.02s | `uv lock --project py-hftbacktest --check` |
| hftbacktest-final315-original-case | 1 | 0.29s | `py-hftbacktest/.venv/bin/python -m pytest py-hftbacktest/tests/test_hftbacktest.py -q` |
| hftbacktest-final315-portable-strict-runtime | 0 | 2.25s | `py-hftbacktest/.venv/bin/python -Werror -c import sys,json,importlib.metadata,importlib.util,hashlib,os
from pathlib import Path
import hftbacktest,databento,databento_dbn
from hftbacktest.binding import ROIVectorMarketDepthBacktest_
p=Path("vendor/databento-dbn/tests/data/test_data.mbo.v3.dbn.zst")
f=databento.DBNStore.from_bytes(p.read_bytes()).to_df()
spec=importlib.util.spec_from_file_location("original_hft_case",Path("py-hftbacktest/tests/test_hftbacktest.py"));m=importlib.util.module_from_spec(spec);spec.loader.exec_module(m);m.run_strategy.compile((ROIVectorMarketDepthBacktest_.class_type.instance_type,))
enums={name:[{"repr":repr(x),"str":str(x),"name":x.name,"value":x.value} for x in cls.variants()] for name,cls in vars(databento_dbn).items() if isinstance(cls,type) and hasattr(cls,"variants")}
print(json.dumps({"python":sys.version,"executable":sys.executable,"packages":{d.metadata["Name"]:d.version for d in importlib.metadata.distributions()},"imports":{x.__name__:x.__file__ for x in [hftbacktest,databento,databento_dbn]},"abi_forward_flag":os.environ.get("PYO3_USE_ABI3_FORWARD_COMPATIBILITY"),"strict_builder":str(type(hftbacktest.BacktestAsset().constant_order_latency(100,100).last_trades_capacity(1000))),"official_sdk_fixture":{"path":str(p),"sha256":hashlib.sha256(p.read_bytes()).hexdigest(),"rows":len(f),"used_as_hft_archive":False},"enums":enums,"real_mbo_frame":{"json":f.to_json(date_format="iso",date_unit="ns"),"dtypes":{k:str(v) for k,v in f.dtypes.items()},"index_dtype":str(f.index.dtype)},"original_strategy_jit":{"signatures":[str(s) for s in m.run_strategy.signatures],"hft_pointer_instances_created":False}},indent=2))` |
| hftbacktest-final315-sdk-portable-build | 0 | 46.1s | `uv build --project vendor/databento-dbn --python 3.15.0 --out-dir /tmp/hftbacktest-dbn-final315-dist` |
| hftbacktest-final315-sdk-provenance-sdist-install | 0 | 55.23s | `uv pip install --python /tmp/hftbacktest-full-python315-env/bin/python --no-deps --reinstall /tmp/hftbacktest-dbn-final315-dist/databento_dbn-0.70.0.tar.gz` |
| hftbacktest-final315-sdk-provenance-sdist | 0 | 0.82s | `uv build --project vendor/databento-dbn --sdist --python 3.15.0 --out-dir /tmp/hftbacktest-dbn-final315-dist` |
| hftbacktest-final315-sdk-sdist-isolated-install | 0 | 44.43s | `uv pip install --python /tmp/hftbacktest-full-python315-env/bin/python --no-deps --reinstall /tmp/hftbacktest-dbn-final315-dist/databento_dbn-0.70.0.tar.gz` |
| hftbacktest-final315-sdk-sdist-strict-runtime | 0 | 2.48s | `/tmp/hftbacktest-full-python315-env/bin/python -Werror -c import sys,json,importlib.metadata,importlib.util,hashlib,os
from pathlib import Path
import hftbacktest,databento,databento_dbn
from hftbacktest.binding import ROIVectorMarketDepthBacktest_
p=Path("vendor/databento-dbn/tests/data/test_data.mbo.v3.dbn.zst")
f=databento.DBNStore.from_bytes(p.read_bytes()).to_df()
spec=importlib.util.spec_from_file_location("original_hft_case",Path("py-hftbacktest/tests/test_hftbacktest.py"));m=importlib.util.module_from_spec(spec);spec.loader.exec_module(m);m.run_strategy.compile((ROIVectorMarketDepthBacktest_.class_type.instance_type,))
enums={name:[{"repr":repr(x),"str":str(x),"name":x.name,"value":x.value} for x in cls.variants()] for name,cls in vars(databento_dbn).items() if isinstance(cls,type) and hasattr(cls,"variants")}
print(json.dumps({"python":sys.version,"executable":sys.executable,"packages":{d.metadata["Name"]:d.version for d in importlib.metadata.distributions()},"imports":{x.__name__:x.__file__ for x in [hftbacktest,databento,databento_dbn]},"abi_forward_flag":os.environ.get("PYO3_USE_ABI3_FORWARD_COMPATIBILITY"),"strict_builder":str(type(hftbacktest.BacktestAsset().constant_order_latency(100,100).last_trades_capacity(1000))),"official_sdk_fixture":{"path":str(p),"sha256":hashlib.sha256(p.read_bytes()).hexdigest(),"rows":len(f),"used_as_hft_archive":False},"enums":enums,"real_mbo_frame":{"json":f.to_json(date_format="iso",date_unit="ns"),"dtypes":{k:str(v) for k,v in f.dtypes.items()},"index_dtype":str(f.index.dtype)},"original_strategy_jit":{"signatures":[str(s) for s in m.run_strategy.signatures],"hft_pointer_instances_created":False}},indent=2))` |
| hftbacktest-final315-strict-compile | 0 | 0.06s | `py-hftbacktest/.venv/bin/python -Werror -m compileall -q -f py-hftbacktest/hftbacktest py-hftbacktest/tests vendor/databento-dbn/python/python` |
| hftbacktest-final315-strict-runtime | 0 | 1.58s | `py-hftbacktest/.venv/bin/python -Werror /tmp/hftbacktest315_runtime_proof.py` |
| hftbacktest-final315-wheel-build | 0 | 236.15s | `uv build --project py-hftbacktest --python 3.15.0` |
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
| repr-slot-upstream-regressions | 0 | 50.75s | `py-hftbacktest/.venv/bin/python -c import os,pathlib,shutil,subprocess,sys,sysconfig,tempfile; p=pathlib.Path(tempfile.mkdtemp(prefix="hft-pyo3-repr-review-")); source=pathlib.Path("vendor/databento-dbn/vendor/pyo3"); shutil.copytree(source,p/"pyo3",ignore=shutil.ignore_patterns("target","__pycache__")); env=dict(os.environ); env["PYO3_PYTHON"]=sys.executable; env["DYLD_LIBRARY_PATH"]=sysconfig.get_config_var("LIBDIR")+(":"+env["DYLD_LIBRARY_PATH"] if env.get("DYLD_LIBRARY_PATH") else ""); env["CARGO_TARGET_DIR"]=str(p/"target"); env["PYTHONWARNINGS"]="error"; print("Owned source copy:",p,flush=True); raise SystemExit(subprocess.call(["cargo","test","--manifest-path",str(p/"pyo3/Cargo.toml"),"--features","auto-initialize","--test","test_enum","--test","test_class_formatting","--test","test_proto_methods"],env=env))` |
| unchanged-strategy-native-numba-compile | 0 | 1.62s | `uv run --project py-hftbacktest --no-sync python -c import importlib.util; from hftbacktest.binding import ROIVectorMarketDepthBacktest_; spec=importlib.util.spec_from_file_location("original_case", "py-hftbacktest/tests/test_hftbacktest.py"); module=importlib.util.module_from_spec(spec); spec.loader.exec_module(module); module.run_strategy.compile((ROIVectorMarketDepthBacktest_.class_type.instance_type,)); print("Original strategy compiled against the actual native-backed Numba class; no market data or pointer instance constructed.")` |
