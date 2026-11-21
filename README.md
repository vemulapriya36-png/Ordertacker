# Simple Order Tracker
SmartCommunity Hack · Project #85 · Small Business & Retail

A beginner-friendly, menu-driven terminal app to record orders and move them
**placed → processing → shipped → delivered** (or cancelled), with strict input
validation, JSON persistence, search, analytics and report export.
Standard library only - Python 3.10+.

## Quick start
```bash
python run.py --seed     # first run: load the 40-order demo dataset
python run.py            # later runs: your data in data/orders.json
python -m unittest discover -s tests    # run the 12 tests
```
Options: `--data PATH` to use a different JSON file. Set `NO_COLOR=1` to disable colours.

## Feature map (brief → implementation)
| Brief requirement | Where |
|---|---|
| Interactive terminal menu | `ordertracker/ui.py` |
| Defensive input validation | `ordertracker/validation.py` (re-used by UI, logic, CSV import and file loading) |
| Core business logic (placed → delivered) | `ordertracker/logic.py` (`OrderBook.add / advance / cancel`) |
| Local persistence + corrupt-file recovery | `ordertracker/storage.py` (atomic JSON writes, auto-backup of bad files) |
| Search & filter (keyword / category / status) | `OrderBook.search`, menu option 4 |
| Executive summary | `ordertracker/reports.py` (totals, averages, delivery rate, trends) |
| Export Markdown / text audit log / CSV | menu option 8 → `exports/` |
| Threshold warnings (overdue, high-value) | menu option 7 + startup banner |
| Bulk CSV import with row-level error log | `ordertracker/importer.py`, menu option 9 |

## Suggested 3-minute demo
1. `python run.py --seed` - show banner, note the overdue warning.
2. **1) Add order** - type bad values (letters as quantity, `2026-13-01`, due date before order date) and show each being rejected, then enter valid ones.
3. **2) Update status** - advance the new order; **5) Details** shows its history.
4. **6) Summary** - status bars, revenue by category, monthly trend.
5. **9) Bulk import** `data/legacy_import_with_errors.csv` - 2 rows accepted, 6 rejected with reasons.
6. **0) Exit**, relaunch `python run.py` - data is still there. Then **8) Export**.

## Data files
- `data/sample_orders.json|csv` - 40 orders incl. edge cases (qty 1000 @ 0.01, price 99,999.99, same-day due date, cancelled, overdue). Regenerate with `python scripts/generate_sample_data.py`.
- Sample "today" is 2026-11-21, so overdue counts shift if you demo on another date.
- `data/legacy_import_with_errors.csv` - deliberately bad rows for the import demo.

## Business rules
Quantity 1-1000 · price 0.01-100,000 · dates real `YYYY-MM-DD` · due ≥ order date ·
status can only move one step forward, or be cancelled before delivery ·
orders ≥ 10,000 trigger a high-value warning.

## Ideas to extend
Tkinter/Textual front-end, matplotlib charts (`reports.compute_summary` already returns chart-ready data), SQLite storage, per-customer reports.
