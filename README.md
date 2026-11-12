# Grover's Algorithm in Qiskit

University group project: implement Grover's search algorithm with Qiskit 2.x and study how it behaves.

**Goal.** Build a working Grover implementation for different problem sizes and different (numbers of)
marked states, then test it experimentally:

- how the success rate depends on the number of Grover rounds `r`,
- what happens with several solutions (`k > 1`),
- how the number of shots affects how close the measured result is to theory,
- the effect of noise (simulated noise models and real IBM hardware),
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
| `grover_core.ipynb` | Shared building blocks with explanations: `oracle`, `diffuser`, `grover`, `optimal_iterations`, `theoretical_success`, `success_rate`. Other notebooks load it with `%run grover_core.ipynb`. Start here to understand the algorithm. |
| `grover.ipynb` | Runs Grover at the optimal `r` for n = 2–5 (two marked states each), prints success rates and saves histograms (`figures/fig_histogram_n*_*.png`). |
| `grover_iterations.ipynb` | Success rate vs. number of rounds `r` for n = 3, 4, 5, compared with the theory curve (`figures/fig_iterations_n3_n4_n5.png`). |
| `requirements.txt` | Python packages needed. |
| `figures/` | All generated plots. |

More notebooks are added as the experiments are completed (multiple solutions, shots convergence,
noise, classical comparison, real hardware).

## Setup

Requires Python 3.10 or newer.

```bash
python3 -m venv qiskit-env
source qiskit-env/bin/activate        # Windows: qiskit-env\Scripts\activate
pip install -r requirements.txt
```

### IBM Quantum account (only needed for the real-hardware notebook)

Save your credentials once on your own machine. **Never commit your API key** (`apikey.json` is in `.gitignore`):

```python
from qiskit_ibm_runtime import QiskitRuntimeService
QiskitRuntimeService.save_account(channel="ibm_quantum_platform", token="<your API key>")
```

## Running

Interactively:

```bash
jupyter notebook          # then open a notebook and choose "Run All"
```

Or headless, from the project folder (this re-runs everything and regenerates the figures):

```bash
jupyter nbconvert --to notebook --execute --inplace grover.ipynb
jupyter nbconvert --to notebook --execute --inplace grover_iterations.ipynb
```

Run the notebooks from the project folder so that `%run grover_core.ipynb` can find the core notebook.

## Bit order

Qiskit writes measured bitstrings right to left: the rightmost character is qubit 0.
The `oracle` function in `grover_core.ipynb` follows the same convention, so the marked string you pass in (for example `'101'`)
is exactly the key you will see in the measurement counts.
