# hftbacktest: portable verification records

Full raw logs are retained in the workspace maintenance evidence, not distributed here. Hashes identify those original logs. A successful build or selected test subset does not imply a full test-suite pass. Commands containing <workspace> describe the original execution environment.

[Machine-readable records](verification.json) · [Repository validation summary](../validation.md)

## Executed commands

| Check | Exit | Duration | Command | Outcomes |
| --- | ---: | ---: | --- | --- |
| build | 0 | unrecordeds | `uv build --project py-hftbacktest` | Successfully built py-hftbacktest/dist/hftbacktest-2.4.4.tar.gz<br>Successfully built py-hftbacktest/dist/hftbacktest-2.4.4-cp314-cp314-macosx_11_0_arm64.whl |
| build-final | 0 | unrecordeds | `uv build --project py-hftbacktest` | Successfully built py-hftbacktest/dist/hftbacktest-2.4.4.tar.gz<br>Successfully built py-hftbacktest/dist/hftbacktest-2.4.4-cp314-cp314-macosx_11_0_arm64.whl |
| cargo-test | 101 | unrecordeds | `cargo test --workspace` | See exit status; no test-count assertion. |
| cargo-tests-current-api | 101 | unrecordeds | `cargo test --workspace` | See exit status; no test-count assertion. |
| cargo-tests-current-docs | 101 | unrecordeds | `cargo test --workspace` | test result: ok. 0 passed; 0 failed; 0 ignored; 0 measured; 0 filtered out; finished in 0.00s<br>test result: ok. 5 passed; 0 failed; 0 ignored; 0 measured; 0 filtered out; finished in 5.52s<br>test result: ok. 22 passed; 0 failed; 0 ignored; 0 measured; 0 filtered out; finished in 0.01s<br>test result: ok. 0 passed; 0 failed; 0 ignored; 0 measured; 0 filtered out; finished in 0.00s<br>test result: ok. 0 passed; 0 failed; 0 ignored; 0 measured; 0 filtered out; finished in 0.00s<br>test result: FAILED. 2 passed; 1 failed; 0 ignored; 0 measured; 0 filtered out; finished in 1.13s |
| cargo-tests-current-examples | 101 | unrecordeds | `cargo test --workspace` | test result: ok. 0 passed; 0 failed; 0 ignored; 0 measured; 0 filtered out; finished in 0.00s<br>test result: ok. 5 passed; 0 failed; 0 ignored; 0 measured; 0 filtered out; finished in 5.52s<br>test result: ok. 22 passed; 0 failed; 0 ignored; 0 measured; 0 filtered out; finished in 0.00s<br>test result: ok. 0 passed; 0 failed; 0 ignored; 0 measured; 0 filtered out; finished in 0.00s<br>test result: ok. 0 passed; 0 failed; 0 ignored; 0 measured; 0 filtered out; finished in 0.00s<br>test result: FAILED. 0 passed; 3 failed; 0 ignored; 0 measured; 0 filtered out; finished in 1.54s |
| cargo-tests-final | 0 | unrecordeds | `cargo test --workspace` | test result: ok. 0 passed; 0 failed; 0 ignored; 0 measured; 0 filtered out; finished in 0.00s<br>test result: ok. 5 passed; 0 failed; 0 ignored; 0 measured; 0 filtered out; finished in 5.51s<br>test result: ok. 22 passed; 0 failed; 0 ignored; 0 measured; 0 filtered out; finished in 0.00s<br>test result: ok. 0 passed; 0 failed; 0 ignored; 0 measured; 0 filtered out; finished in 0.00s<br>test result: ok. 0 passed; 0 failed; 0 ignored; 0 measured; 0 filtered out; finished in 0.00s<br>test result: ok. 3 passed; 0 failed; 0 ignored; 0 measured; 0 filtered out; finished in 0.04s<br>test result: ok. 0 passed; 0 failed; 0 ignored; 0 measured; 0 filtered out; finished in 0.00s |
| cargo-tests-fixed-builder | 101 | unrecordeds | `cargo test --workspace` | See exit status; no test-count assertion. |
| cargo-tests-fixed-examples | 101 | unrecordeds | `cargo test --workspace` | See exit status; no test-count assertion. |
| python-final-sync | 0 | unrecordeds | `uv sync --project py-hftbacktest --all-groups --all-extras --python 3.14.8` | See exit status; no test-count assertion. |
| python-sync | 2 | unrecordeds | `uv sync --project py-hftbacktest --all-groups --all-extras --python 3.15` | See exit status; no test-count assertion. |
| python-tests | 1 | unrecordeds | `uv run --project py-hftbacktest --no-sync pytest py-hftbacktest/tests` | See exit status; no test-count assertion. |
| python-unittest | 1 | unrecordeds | `uv run --project py-hftbacktest --no-sync python -m unittest discover -s py-hftbacktest/tests` | See exit status; no test-count assertion. |
| python-unittest-latest | 1 | unrecordeds | `uv run --project py-hftbacktest --no-sync python -m unittest discover -s py-hftbacktest/tests` | See exit status; no test-count assertion. |
| python-upgrade | 0 | unrecordeds | `uv lock --project py-hftbacktest --upgrade --python 3.14.8` | See exit status; no test-count assertion. |
| python314-sync | 0 | unrecordeds | `uv sync --project py-hftbacktest --all-groups --all-extras --python 3.14.8` | See exit status; no test-count assertion. |
