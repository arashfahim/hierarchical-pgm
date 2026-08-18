# hierarchical-pgm

Companion code for the paper *"A Hierarchical Resource-Efficient Deep Policy Gradient Method for Continuous-Time Optimal Control Problems"* by Arash Fahim (Florida State University) and Md. Arafatur Rahman (Citibank N.A.), available at [arXiv:2502.14141](https://arxiv.org/html/2502.14141v3).

The paper proposes a hierarchical (multiscale) implementation of the deep policy gradient method (PGM) for continuous-time stochastic optimal control: rather than committing up front to a uniformly fine time discretization, a policy is trained on a coarse time grid first, then the grid is selectively refined only in the sub-intervals where the coarse solution is inaccurate. This repository contains the notebooks for the two numerical experiments in the paper.

## Contents

All three notebooks are self-contained: there are no external data files anywhere in this repo. Every experiment simulates its own trajectories (Brownian sample paths) at run time inside the notebook — "data" is generated, not loaded.

### `LQC/Two-steps/LQC_Updated_v7.ipynb`
One-fold hierarchical PGM (one refinement step) on a 1-D linear-quadratic stochastic control (LQSC) benchmark, for which a closed-form value function is available for comparison. Structure, top to bottom:
1. **Parameters** — LQSC coefficients ($a,b,c,d,\sigma,A,B,\alpha,\beta$, horizon $T$).
2. **Function class** — the neural-network architecture used for both the policy and the value-function surrogate.
3. **Closed-form solution** — the Riccati-equation solution used as ground truth.
4. **PGM** — the single-scale (coarse) policy-gradient trainer class and its training loop.
5. **Multi-scale PGM** — the hierarchical class that wraps the coarse policy and adds one refinement level; its training loop.
6. **Brute-force** — a fine-uniform-grid PGM baseline trained directly at the fine time step, for comparison.
7. Final cells produce the wall-clock boxplot and the relative-error comparison plots, and log timing to `wallclock_log.json`.

### `LQC/Three-steps/LQC_Updated_v8.ipynb`
Same LQSC benchmark, extended to a two-fold hierarchical PGM (two refinement levels: coarse → "Multi-scale PGM 1" → "Multi-scale PGM 2"), again compared against the same brute-force fine-grid baseline. Structurally identical to `v7` with an extra multi-scale stage.

> **Known issue:** 6 of the 7 `plt.savefig(...)` calls in this notebook use a hardcoded absolute path from the original development machine (`/Users/arashfahim/Documents/GitHub/multiscale-PGM-for-stochastic-control/LQC/Three-steps/...`), which does not exist in this repo or on any other machine. Running those cells as-is will raise `FileNotFoundError`. Edit those paths (or replace them with a relative filename, as the 7th `savefig` call already does) before running, or comment the lines out if you don't need the saved figure files — the model training and printed results are unaffected either way.

### `LOB/MS_PGM_v7.ipynb`
Hierarchical PGM applied to an optimal-execution problem under stochastic price impact and resilience (a limit-order-book model). This is the final version in a `MS_PGM_v1.ipynb`–`v7.ipynb` development sequence — only `v7` corresponds to the results reported in the paper; `v1`–`v6` are kept for development history and are not individually described here. Structure:
1. **Parameters** — price-impact/resilience model parameters and problem horizon.
2. **Coarse PGM** — `trade_net` (policy network) and `value_fnc` (value-function network) classes, wrapped in the `optimal_execution` class that trains the coarse-scale policy.
3. **Fine-scale PGM** — the `fine_optimal_execution` class, which solves the refined sub-problem on the selected fine sub-intervals.
4. Final cells train `oe100`, a brute-force baseline trained directly at a fine 100-step grid, compare it against the hierarchical result over multiple random seeds, produce the wall-clock boxplot and trajectory/manifold diagnostic plots, and log timing to `wallclock_log.json`.

## Requirements

All three notebooks were run under **Python 3.12.2** and depend on:

```
numpy scipy pandas torch matplotlib seaborn ipython
```

`LOB/MS_PGM_v7.ipynb` additionally requires `plotly`. All notebooks call `matplotlib.pyplot.rc('text', usetex=True)` for LaTeX-rendered plot labels, so a working **LaTeX installation** (with the `amsmath` package) must be on your `PATH` — without it, any cell that produces a plot will raise a LaTeX/`RuntimeError` rather than falling back to non-LaTeX rendering.

## How to run

Open a notebook in Jupyter (`jupyter notebook` or `jupyter lab`) and run all cells top to bottom — each notebook is meant to be executed in order, since later cells (multi-scale/fine training, brute-force baseline, comparison plots) depend on model instances and variables defined earlier. Training is done on CPU by default (no CUDA-specific code); expect the brute-force baselines in particular to take noticeably longer than the hierarchical runs, which is the point being measured.

Each notebook writes/appends its own wall-clock timing to a `wallclock_log.json` file in its own directory on every run (see the `overwrite_wallclock_log` flag near the end of each notebook to start a fresh log instead of appending), plus whichever `.png`/`.pdf` plot files its `savefig` calls point at (subject to the path caveat above for `v8`).

## Reproducing the paper's results

- `LQC_Updated_v7.ipynb` produces the one-fold hierarchical PGM vs. brute-force comparison and timing reported for the linear-quadratic benchmark (≈4.2× wall-clock speedup in the paper).
- `LQC_Updated_v8.ipynb` produces the two-fold hierarchical PGM vs. brute-force comparison and timing for the same benchmark (≈5.2× wall-clock speedup in the paper).
- `MS_PGM_v7.ipynb` produces the optimal-execution (limit-order-book) comparison and timing against the `oe100` fine-grid baseline (≈2.7× wall-clock speedup in the paper).

The paper's reported speedups are averaged over multiple independent runs (20 runs for the figures in the paper); a single notebook run will reproduce the same qualitative comparison but individual-run timings will vary with machine load. Re-running a notebook multiple times with `overwrite_wallclock_log = False` accumulates runs into the same `wallclock_log.json`, whose `"average"` field mirrors how the paper's tables were computed.

## Citation

If you use this code, please cite the paper:

```bibtex
@misc{fahim2025hierarchical,
  title         = {A Hierarchical Resource-Efficient Deep Policy Gradient Method for Continuous-Time Optimal Control Problems},
  author        = {Fahim, Arash and Rahman, Md. Arafatur},
  year          = {2025},
  eprint        = {2502.14141},
  archivePrefix = {arXiv},
  url           = {https://arxiv.org/html/2502.14141v3}
}
```

Journal publication is pending; this entry will be updated once the manuscript is accepted.
