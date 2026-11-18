# Grover's Algorithm in Qiskit

University group project: implement Grover's search algorithm with Qiskit 2.x and study how it behaves.

**Goal.** Build a working Grover implementation for different problem sizes and different (numbers of)
marked states, then test it experimentally:

- how the success rate depends on the number of Grover rounds `r`,
- what happens with several solutions (`k > 1`),
- how the number of shots affects how close the measured result is to theory,
- the effect of noise (Aer noise models and offline "fake" IBM backends that imitate real devices),
- how Grover compares with classical search.

All figures go to `figures/` and are used in the PDF report.

## Notation

| Symbol | Meaning |
|---|---|
| `n` | number of qubits |
| `N = 2**n` | size of the search space |
| `k` | number of marked states (solutions) |
| `r` | number of Grover rounds (oracle + diffuser) |
| `θ = asin(√(k/N))` | rotation angle per round |

Theoretical success probability after `r` rounds: `sin²((2r+1)·θ)`.
Optimal number of rounds: `r = round(π/(4θ) − 0.5)`.

## Files

| File | What it does |
|---|---|
| `grover_core.ipynb` | Shared building blocks with explanations: `oracle`, `diffuser`, `grover`, `optimal_iterations`, `theoretical_success`, `success_rate`, and the noise-model helper `depolarizing_noise_model`. Other notebooks load it with `%run grover_core.ipynb`. Start here to understand the algorithm. |
| `grover.ipynb` | Runs Grover at the optimal `r` for n = 2–5 (two marked states each), prints success rates and saves histograms (`figures/fig_histogram_n*_*.png`). |
| `grover_iterations.ipynb` | Success rate vs. number of rounds `r` for n = 3, 4, 5, compared with the theory curve (`figures/fig_iterations_n3_n4_n5.png`). |
| `multiple_solutions.ipynb` | Several solutions: n = 4 with k = 1, 2, 3, 4, 8 marked states. Histograms at the optimal `r`, success vs. `r` for each k, and optimal `r` vs. k, including the special cases k = N/4 (100% after 1 round) and k = N/2 (no advantage) (`figures/fig_multiple_solutions_*.png`). |
| `shots_convergence.ipynb` | How the number of shots affects accuracy: n = 3, marked `'101'`, 20 repeated runs at 10, 100, 1000 and 10 000 shots. Shows the mean stays on the theory value while the spread shrinks like 1/√shots (`figures/fig_shots_convergence_*.png`). |
| `noise.ipynb` | Effect of gate errors: n = 3, success vs. `r` with a depolarizing noise model at CNOT error 0–0.05, both sampled (4096 shots) and exact (density-matrix). Shows the lower peak, the flattening toward random guessing and the best `r` moving earlier (`figures/fig_noise_success_vs_rounds.png`). |
| `classical_comparison.ipynb` | Number of oracle queries: classical random search (plain Python, averaged over 10 000 searches) vs. Grover's optimal `r` and `r/P` (with retries), for N = 4 … 1024 and k = 1, 4. Shows the quadratic speed-up (`figures/fig_classical_comparison_queries.png`). |
| `requirements.txt` | Python packages needed. |
| `figures/` | All generated plots. |
