# Computational Results

Supporting data for the paper:

> **"Macroitems: Optimal LP Solutions of the Precedence Constrained Knapsack Problem"**  
> Submitted to *INFORMS Mathematics of Operations Research*

This repository contains the raw benchmark runs and aggregated tables used to
produce all computational figures and tables in the paper.

---

## Repository Structure

```
computational_results/
├── README.md              ← this file
└── data/
    ├── runs_hfma.csv               ← raw runs: HFMA algorithm
    ├── runs_fma.csv                ← raw runs: FMA algorithm (no heap)
    ├── runs_hima.csv               ← raw runs: HIMA algorithm (in-trees)
    ├── runs_homa.csv               ← raw runs: HOMA algorithm (out-trees)
    ├── runs_bppf_and_hfma_medium.csv  ← raw runs: BPPF vs HFMA comparison
    └── aggregated/
        ├── agg_fig13_hfma_vs_fma.csv       ← Fig 13 table data
        ├── agg_fig14_hfma_large.csv        ← Fig 14 table data
        ├── agg_fig15_hfma_vs_bppf.csv      ← Fig 15 table data
        └── agg_fig16_specialized_large.csv ← Fig 16 table data
```

---

## Algorithms

| File | Name in paper | Description |
|---|---|---|
| `runs_hfma.csv` | **HFMA** | Heap-based Forest Macroitem Algorithm — general directed forests |
| `runs_fma.csv`  | **FMA**  | Forest Macroitem Algorithm without heap (linear scan) |
| `runs_hima.csv` | **HIMA** | Heap-based In-tree Macroitem Algorithm — in-forests only |
| `runs_homa.csv` | **HOMA** | Heap-based Out-tree Macroitem Algorithm — out-forests only |
| `runs_bppf_and_hfma_medium.csv` | **BPPF** | Bounded-Precision Parametric Pseudoflow (Hochbaum et al.) vs HFMA |

---

## Test Bed

Two benchmark sets are used:

### Medium-sized test bed
- **Sizes** $n \in \{100, 200, \ldots, 1\,000\}$ (10 values)
- **Topology**: `gen-forest` (directed forests)
- **Algorithms tested**: HFMA, FMA, BPPF

### Small test bed (HFMA/FMA scaling)
- **Sizes** $n \in \{10, 20, \ldots, 100\}$ (10 values)
- **Topology**: `gen-forest`
- **Algorithms tested**: HFMA, FMA

### Large-sized test bed
- **Sizes** $n \in \{10\,000, 20\,000, \ldots, 100\,000\}$ (10 values)
- **Topologies**: `gen-forest`, `gen-inforest`, `gen-outforest`
- **Algorithms tested**: HFMA, HIMA, HOMA

Each (topology, size) combination is crossed with:
- 6 profit-weight classes: `uncorr`, `weakly_corr`, `strongly_corr`, `neg_uncorr`, `neg_weakly_corr`, `neg_strongly_corr`
- 4 arc densities: `sparse`, `medium`, `dense`, `tree`
- 10 random seeds: 1–10

→ **240 instances per (topology, size)**

---

## Raw Run File Format (`runs_*.csv`)

Each row is one run on one instance. Deduplicated: one row per (instance, algorithm) pair.

| Column | Description |
|---|---|
| `n` | Number of items |
| `topo_dir` | Instance family: `forest`, `inforest`, `outforest` |
| `kp_class` | Profit-weight class (6 values) |
| `density` | Arc density: `sparse`, `medium`, `dense`, `tree` |
| `seed` | Random seed (1–10) |
| `n_nodes` | Number of items (from solver) |
| `n_arcs` | Number of precedence constraints |
| `topology` | Actual graph topology of this instance (`path`, `inforest`, `outforest`, …) |
| `n_macroitems` | Number of macroitems in the optimal sequence |
| `algorithm_ms` | Algorithm CPU time in milliseconds |

**Instance filename convention**: `{topo_dir}_{kp_class}_{density}_n{n:07d}_s{seed:02d}.txt`

### Run counts

| File | Rows | Coverage |
|---|---|---|
| `runs_hfma.csv` | 20 880 | all 87 sizes × 240 instances |
| `runs_fma.csv`  |  4 562 | sizes $n \le 1\,000$ (no heap needed) + toy sizes |
| `runs_hima.csv` |  6 960 | large sizes on `gen-inforest` (29 sizes × 240) |
| `runs_homa.csv` |  6 960 | large sizes on `gen-outforest` (29 sizes × 240) |

---

## BPPF Comparison File (`runs_bppf_and_hfma_medium.csv`)

One row per instance from the medium-sized `gen-forest` test bed ($n \in \{100,\ldots,1\,000\}$, 2 400 rows total).

| Column | Description |
|---|---|
| `n` | Number of items |
| `kp_class` | Profit-weight class |
| `density` | Arc density |
| `seed` | Random seed |
| `status` | `OK` or error |
| `n_macroitems` | Macroitems found by HFMA |
| `n_hpf_groups` | Groups found by BPPF |
| `forest_cpu_ms` | HFMA CPU time (ms) |
| `hpf_cpu_ms` | BPPF CPU time (ms) |
| `n_params` | Number of parametric breakpoints |

BPPF precision: $10^{-6}$. Macroitem counts agree on 2 152/2 160 instances tested; 8 discrepancies in `weakly_corr` with medium/dense density and $700 \le n \le 900$ (numerical precision issue discussed in the paper).

---

## Aggregated Files (`data/aggregated/`)

Pre-computed means per $n$ used directly for figures and tables in the paper.

| File | Figure | Description |
|---|---|---|
| `agg_fig13_hfma_vs_fma.csv` | Fig 13 | HFMA vs FMA mean CPU time, $n=10$–$20\,000$ |
| `agg_fig14_hfma_large.csv` | Fig 14 | HFMA mean CPU time, $n=10\,000$–$100\,000$ |
| `agg_fig15_hfma_vs_bppf.csv` | Fig 15 | HFMA vs BPPF mean CPU time, $n=100$–$1\,000$ |
| `agg_fig16_specialized_large.csv` | Fig 16 | HIMA, HOMA, speedup over HFMA, $n=10\,000$–$100\,000$ |

To reproduce aggregated files from raw runs:

```python
import pandas as pd

df = pd.read_csv("data/runs_hfma.csv")
agg = df.groupby("n")["algorithm_ms"].mean().reset_index()
agg.columns = ["n", "hfma_mean_ms"]
```

---

## Empirical Complexity

| Algorithm | Fit | $a$ | $R^2$ |
|---|---|---|---|
| HFMA | $a \cdot n \log n$ | $3.82 \times 10^{-4}$ | 0.986 |
| HIMA | $a \cdot n \log n$ | $1.04 \times 10^{-4}$ | 0.986 |
| HOMA | $a \cdot n \log n$ | $9.03 \times 10^{-5}$ | 0.985 |

Speedup of specialized variants over HFMA on the respective topology:
- **HIMA** vs HFMA on in-forests: **7.7×–9.9×**
- **HOMA** vs HFMA on out-forests: **4.1×–5.2×**

---

## Reproducibility

**Build the solver:**
```bash
mkdir -p build && cd build && cmake .. -DCMAKE_BUILD_TYPE=Release && make -j
```

**Generate all instances:**
```bash
python3 instances/gen_instances.py
```

**Run benchmarks:**
```bash
python3 report_performance/run_benchmark.py          # small + medium sizes
python3 report_performance/run_benchmark_large.py    # large sizes (heap only)
python3 PARAMETRIC_PSEUDO_FLOW/scripts/hpf_medium_batch.py  # BPPF comparison
```

All raw results are appended to `results/runs.csv`.

