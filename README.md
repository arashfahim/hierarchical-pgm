# hierarchical-pgm

Companion code for the paper *"A Hierarchical Resource-Efficient Deep Policy Gradient Method for Continuous-Time Optimal Control Problems"* by Arash Fahim (Florida State University) and Md. Arafatur Rahman (Citibank N.A.).

The paper proposes a hierarchical (multiscale) implementation of the deep policy gradient method (PGM) for continuous-time stochastic optimal control: rather than committing up front to a uniformly fine time discretization, a policy is trained on a coarse time grid first, then the grid is selectively refined only in the sub-intervals where the coarse solution is inaccurate. This repository contains the notebooks for the two numerical experiments in the paper.

## Contents

- **`LQC/Two-steps/LQC_Updated_v7.ipynb`** — one-fold hierarchical PGM (one refinement step) on a linear-quadratic stochastic control benchmark, compared against a brute-force fine-grid baseline.
- **`LQC/Three-steps/LQC_Updated_v8.ipynb`** — two-fold hierarchical PGM (two refinement steps) on the same benchmark.
- **`LOB/MS_PGM_v1.ipynb` – `MS_PGM_v7.ipynb`** — development history of the hierarchical PGM applied to an optimal-execution problem under stochastic price impact and resilience, formulated on a limit-order-book model; `MS_PGM_v7.ipynb` is the version whose results are reported in the paper.

## Citation

If you use this code, please cite the paper (citation details to be added once the manuscript is published).
