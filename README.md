# Cont & Kukanov Smart Order Router Backtest

## Overview

This project implements a back-test of the static cost model from Cont & Kukanov ("Optimal Order Placement in Limit Order Markets"). The router splits a 5,000-share buy order across multiple venues, tuning three risk parameters (`lambda_over`, `lambda_under`, `theta_queue`) to minimize total cost. The allocator is implemented exactly as described in the provided pseudocode.

## Code Structure

- **backtest.py**: Loads `l1_day.csv`, constructs Level-1 snapshots, runs a grid search over the three risk parameters, and benchmarks the tuned router against three baselines: best-ask, TWAP, and VWAP. Prints a single JSON object with all required results and (optionally) saves a cumulative cost plot as `results.png`.
- **results.png**: (Optional) Cumulative cost plot for the optimal parameter set.
- **README.md**: This file.

## Parameter Search

- Grid search over:
    - `lambda_over`: [0.01, 0.1, 1.0]
    - `lambda_under`: [0.01, 0.1, 1.0]
    - `theta_queue`: [0.01, 0.1, 1.0]

## Baselines

- **Best-Ask**: Always takes as much as possible at the lowest ask across all venues.
- **TWAP**: Splits the order into nine equal time buckets and executes each at the best available price.
- **VWAP**: Splits the order proportionally to displayed ask size across venues at each snapshot.

## Output

- Prints a single JSON object to stdout containing:
    - The best parameters found.
    - Total cash spent and average fill price for the optimal strategy.
    - The same metrics for each baseline.
    - Savings (in basis points) versus each baseline.
- Optionally saves a cumulative cost plot as `results.png`.

## Suggested Improvement

*To improve fill realism, model queue position and slippage. For example, track your order’s position in the queue and simulate partial fills based on observed market flow, not just displayed size. This would better capture the risk of not being filled and the impact of order priority.*

## Usage

1. Place `l1_day.csv` in the same directory as `backtest.py`.
2. Run:
    ```
    python backtest.py
    ```
3. The script will print the required JSON and save `results.png` (if enabled).

---

*Allocator logic matches the provided pseudocode. The script is robust and skips empty snapshots. Parameter search is small for speed, but can be expanded for finer tuning.*
