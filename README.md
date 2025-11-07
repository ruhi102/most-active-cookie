# Most Active Cookie

A lightweight tool that identifies the most active cookie(s) for a given date from a CSV log file.

Built with only the Python standard library no external dependencies required for core functionality.

## Features

- 🚀 **Fast & Efficient** - Streaming parser with early-exit optimization
- 📦 **Zero Dependencies** - Uses only Python standard library
- 🌍 **UTC-Aware** - Properly handles timezone offsets
- ✅ **Production Ready** - Comprehensive error handling and testing
- 🔧 **Easy Install** - Package with entry point for direct CLI access

## What It Does

Given a sample cookie log CSV file:

```csv
cookie,timestamp
AtY0laUfhglK3lC7,2018-12-09T14:19:00+00:00
SAZuXPGUrfbcn5UA,2018-12-09T10:13:00+00:00
5UAVanZf6UtGyKVS,2018-12-09T07:25:00+00:00
AtY0laUfhglK3lC7,2018-12-09T06:19:00+00:00
SAZuXPGUrfbcn5UA,2018-12-08T22:03:00+00:00
4sMM2LxV07bPJzwf,2018-12-08T21:30:00+00:00
```

The tool counts how many times each cookie appears on the specified date (in UTC) and outputs the cookie(s) with the highest frequency. If multiple cookies tie for the highest count, all are printed on separate lines.

## Quick Start

### Prerequisites

- Python 3.9 or higher

### Installation

```bash
# Clone the repository
git clone <your-repo-url>.git
cd most-active-cookie

# (Optional but recommended) Create a virtual environment
python -m venv .venv

# Activate virtual environment
# On Windows:
.venv\Scripts\activate
# On macOS/Linux:
source .venv/bin/activate

# Install in editable mode
pip install -e .
```

### Basic Usage

```bash
most-active-cookie -f tests/data/cookie_log.csv -d 2018-12-09
```

**Output:**
```
AtY0laUfhglK3lC7
```

## Usage

```bash
most-active-cookie -f <path-to-csv> -d <YYYY-MM-DD>
```

### Arguments

| Flag | Description |
|------|-------------|
| `-f`, `--file` | Path to the cookie log CSV file |
| `-d`, `--date` | Target date in YYYY-MM-DD format (interpreted as UTC) |

### Behavior

- Prints the most active cookie(s), one per line
- Prints nothing if no cookies match the specified date
- Returns exit code `0` on success, `2` on argument/file errors

## Examples

### Single Winner

```bash
most-active-cookie -f tests/data/cookie_log.csv -d 2018-12-09
```

**Output:**
```
AtY0laUfhglK3lC7
```

### Tie (Multiple Winners)

When two or more cookies have the same highest count:

**Output:**
```
cookieA
cookieB
```

### No Matches

If the specified date has no entries, no output is printed (exit code remains `0`).

## Input Format

The CSV file must have:
- A header row: `cookie,timestamp`
- ISO-8601 formatted timestamps (e.g., `2018-12-09T14:19:00+00:00`)
- Timestamps may include timezone offsets (automatically normalized to UTC)

## Project Structure

```
most-active-cookie/
├── src/
│   └── cookiestats/
│       ├── __init__.py
│       ├── cli.py      # CLI: arg parsing, stdout/stderr, exit codes
│       ├── core.py     # Pure logic: counting, early-exit, date handling
│       └── io.py       # CSV parsing + UTC normalization
├── tests/
│   ├── __init__.py
│   ├── test_core.py    # Unit tests for core logic
│   └── data/
│       └── cookie_log.csv
├── README.md
├── pyproject.toml      # Packaging, entry point, tool config
├── LICENSE
└── .github/
    └── workflows/
        └── ci.yml      # CI: run tests on push/PR
```

## Design Philosophy

### Separation of Concerns

- **`core.py`** - Pure business logic (easy to test, no I/O side effects)
- **`io.py`** - Robust CSV reading and timestamp parsing
- **`cli.py`** - User-facing command interface, argument handling, exit codes

### UTC Correctness

All timestamps are parsed and converted to UTC before comparison. The target date (`-d`) is interpreted as a UTC date.

### Performance Optimizations

- **Streaming Parser** - Processes file line-by-line (O(n) time complexity)
- **Early Exit** - Stops reading once the target date range is passed (assumes descending timestamp order)
- **Memory Efficient** - Memory usage proportional only to unique cookies on the target day

### Standard Library Only

No external dependencies required for core functionality. Uses built-in modules: `csv`, `datetime`, `collections.Counter`, `argparse`.

## Assumptions

- The log file is sorted by timestamp in **descending order** (most recent first)
- The target date (`-d`) is interpreted as **UTC**
- Timestamps may include timezone offsets; they are normalized to UTC before comparison
- The file size is reasonable for streaming (no specific limit, but designed for logs that fit typical disk/memory constraints)

## Error Handling

| Error Type | Behavior |
|------------|----------|
| Invalid date format | Prints helpful message to stderr, exits with code `2` |
| File not found / OS errors | Prints error to stderr, exits with code `2` |
| Malformed CSV rows | Skipped safely without crashing |
| Success (including no matches) | Exits with code `0` |

## Running Tests

```bash
# Install with dev dependencies
pip install -e .[dev]

# Run tests
pytest -v
```

**Expected output:**
```
tests/test_core.py::test_single_winner PASSED
tests/test_core.py::test_tie PASSED
tests/test_core.py::test_no_matches PASSED
...
```

## Development

### Install Development Tools

```bash
pip install -e .[dev]
```

### Code Quality (if configured in pyproject.toml)

```bash
# Linting
ruff check src tests

# Formatting
black src tests
```

## Troubleshooting

### `ModuleNotFoundError: cookiestats` when running tests

Ensure you're in the project root and have installed in editable mode:

```bash
pip install -e .
pytest
```

### `most-active-cookie` command not found after install

1. Verify your virtual environment is activated
2. Confirm installation succeeded without errors
3. On Windows PowerShell, activate with:
   ```powershell
   .venv\Scripts\Activate
   ```

### No output is printed

This is expected behavior when no cookies match the provided date. The tool exits successfully (`0`) but produces no output.

## License

MIT License. See [LICENSE](LICENSE) for details.

---

**Contributing:** Issues and pull requests are welcome! Please ensure all tests pass before submitting.
