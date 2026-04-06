# Migration Guide: hftbacktest

**Branch**: `feature/ayu_develop`
**Standard**: See `docs/PYTHON_MODERN_STANDARD.md` in the trading workspace root.

## Overview

This project has been modernized on the `feature/ayu_develop` branch to use the 2026 Python tooling stack. When syncing from upstream (default branch), the following changes must be re-applied if upstream overwrites them.

## What Changed

### 1. Build System (pyproject.toml)
- **Build backend**: `maturin` (unchanged -- required for Rust extensions)
- **PEP 621 metadata**: Already present, unchanged
- **Dependencies**: Managed by `uv`, lockfile in `py-hftbacktest/uv.lock`
- **Dev dependencies**: Added `ruff`, `pyright`, `pytest` as optional `dev` extras

### 2. Removed Legacy Files
No legacy files were removed. The project already used `pyproject.toml`.

### 3. Source Layout
- **Layout**: Unchanged -- maturin projects use their own layout convention
- **Python package**: `py-hftbacktest/hftbacktest/` (not moved to src/ -- maturin requires this layout)
- **Rust source**: `py-hftbacktest/src/` (Rust extension code)
- **Import unchanged**: `import hftbacktest` still works

### 4. Tooling Configuration (added to py-hftbacktest/pyproject.toml)

#### Ruff (linting + formatting)
```toml
[tool.ruff]
line-length = 88
target-version = "py313"

[tool.ruff.lint]
select = ["E", "W", "F", "I", "UP", "B", "SIM", "C4", "RUF", "PERF", "TC", "PTH"]
ignore = ["E501"]

[tool.ruff.lint.per-file-ignores]
"tests/*" = ["S101"]
"__init__.py" = ["F401"]

[tool.ruff.lint.isort]
known-first-party = ["hftbacktest"]
```

#### Pyright (type checking)
```toml
[tool.pyright]
pythonVersion = "3.13"
typeCheckingMode = "basic"
```

#### Pytest
```toml
[tool.pytest.ini_options]
minversion = "9.0"
addopts = ["-ra", "-q", "--strict-markers", "--import-mode=importlib"]
testpaths = ["tests"]
pythonpath = ["src"]
xfail_strict = true
filterwarnings = ["error"]
```

### 5. Python Version
- `.python-version` set to `3.13` (at repo root and in py-hftbacktest/)
- `requires-python` left as `">=3.11"` (upstream compatibility)

## After Upstream Sync Checklist

When merging upstream changes into `feature/ayu_develop`:

1. **Check pyproject.toml**: Upstream may modify `[project]` metadata (version bumps, new deps). Merge those changes but keep `[tool.ruff]`, `[tool.pyright]`, `[tool.pytest]` sections intact.
2. **Re-lock**: Run `cd py-hftbacktest && uv lock` to update `uv.lock` with any new/changed dependencies.
3. **Verify**: Run `cd py-hftbacktest && uv sync && uv run python -c "import hftbacktest"` (requires Rust toolchain for build).

## Quick Commands

```bash
cd py-hftbacktest
uv sync                                    # Install all deps (requires Rust toolchain)
uv run python -c "import hftbacktest"      # Verify import
uv run pytest                              # Run tests
uv run ruff check .                        # Lint
uv run ruff format .                       # Format
uv lock                                    # Re-generate lockfile
```

## Notes

- Building requires a Rust toolchain (maturin compiles Rust extensions)
- The `py-hftbacktest/` subdirectory is the Python project root; run all uv commands from there
- Do NOT change the build backend from `maturin` -- it is required for the Rust extensions
