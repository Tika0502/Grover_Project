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
| `hardware_simulation.ipynb` | Hardware-realistic simulation, fully offline: n = 2, 3, 4 on the ideal simulator, the depolarizing noise model (p₂ = 0.005) and the fake backend `FakeTorino` (a saved copy of a real IBM device's layout, native gates and noise; no account or connection). Also circuit depth and 2-qubit gate counts for n = 2–5 (`figures/fig_hardware_*.png`). |
| `requirements.txt` | Python packages needed. |
| `figures/` | All generated plots. |

## Setup

Requires Python 3.10 or newer.

```bash
python3 -m venv qiskit-env
source qiskit-env/bin/activate        # Windows: qiskit-env\Scripts\activate
pip install -r requirements.txt
```

Everything runs **locally and offline**: no IBM Quantum account, API key or internet connection is needed,
and nothing runs on real quantum hardware. Real-device behaviour is approximated with Aer noise models and the
offline fake backends that ship with `qiskit_ibm_runtime.fake_provider`.

## Running

Interactively:

```bash
jupyter notebook          # then open a notebook and choose "Run All"
```

Or headless, from the project folder (this re-runs everything and regenerates the figures):

```bash
jupyter nbconvert --to notebook --execute --inplace grover.ipynb
jupyter nbconvert --to notebook --execute --inplace grover_iterations.ipynb
jupyter nbconvert --to notebook --execute --inplace multiple_solutions.ipynb
jupyter nbconvert --to notebook --execute --inplace shots_convergence.ipynb
jupyter nbconvert --to notebook --execute --inplace noise.ipynb
jupyter nbconvert --to notebook --execute --inplace classical_comparison.ipynb
jupyter nbconvert --to notebook --execute --inplace hardware_simulation.ipynb
```

Run the notebooks from the project folder so that `%run grover_core.ipynb` can find the core notebook.

## Bit order

Qiskit writes measured bitstrings right to left: the rightmost character is qubit 0.
The `oracle` function in `grover_core.ipynb` follows the same convention, so the marked string you pass in (for example `'101'`)
is exactly the key you will see in the measurement counts.
