# Computational Results

Supporting data for the paper:

> **"Optimal Macroitem Sequences in the Precedence Constrained Knapsack Problem"**  
> Valerio Dose, Fabio Furini, Marco Locatelli

This repository contains the raw benchmark runs used to
produce all computational figures and tables in the paper.

The solver source code is at **[fabiofurini/macroitems-cpp](https://github.com/fabiofurini/macroitems-cpp)**.

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
    └── runs_bppf_and_hfma_medium.csv  ← raw runs: BPPF vs HFMA comparison
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
- **Sizes** n ∈ {100, 200, …, 1,000} (10 values)
- **Topology**: `gen-forest` (directed forests)
- **Algorithms tested**: HFMA, FMA, BPPF

### Large-sized test bed
- **Sizes** n ∈ {10,000, 20,000, …, 100,000} (10 values)
- **Topologies**: `gen-forest`, `gen-inforest`, `gen-outforest`
- **Algorithms tested**: HFMA, HIMA, HOMA

Each (topology, size) combination is crossed with:
- 6 profit-weight classes: `uncorr`, `weakly_corr`, `strongly_corr`, `neg_uncorr`, `neg_weakly_corr`, `neg_strongly_corr`
- 4 arc densities: `sparse`, `medium`, `dense`, `tree`
- 10 random seeds: 1–10

→ **240 instances per (topology, size)**

---

## Instance Format

Each instance is a plain-text file with the following structure:

```
n <number_of_items>
profits  <p_1> <p_2> … <p_n>
weights  <w_1> <w_2> … <w_n>
arcs <number_of_arcs>
<tail_1> <head_1>
<tail_2> <head_2>
…
```

- **Items** are numbered 1 to n.
- **Profits** may be negative (neg-* classes, see below); weights are always positive.
- **Arcs** encode precedence constraints: arc `(i, j)` means item `i` must be selected whenever item `j` is selected.
- **Capacity** is NOT stored in the file. It is passed to the solver at run time as a percentage of the total weight (e.g., 50% of sum of all weights).

**Illustrative example** (n = 10, for format purposes only — not one of the published instances):
```
n 10
profits  1090 395 1043 395 185 281 141 624 677 -482
weights  990 295 943 295 85 181 41 524 577 382
arcs 9
1 2
1 3
4 2
1 5
6 2
5 7
3 8
1 9
10 2
```

### Profit-Weight Classes

Based on Pisinger (1994), with r = 1000:

| Class | Profits | Weights |
|---|---|---|
| `uncorr` | uniform in [1, 1000] | uniform in [1, 1000] |
| `weakly_corr` | uniform in [max(1, w−100), w+100] | uniform in [1, 1000] |
| `strongly_corr` | w + 100 (deterministic) | uniform in [1, 1000] |
| `neg_uncorr` | same as `uncorr`, ~25% of profits negated | uniform in [1, 1000] |
| `neg_weakly_corr` | same as `weakly_corr`, ~25% of profits negated | uniform in [1, 1000] |
| `neg_strongly_corr` | same as `strongly_corr`, ~25% of profits negated | uniform in [1, 1000] |

The neg-* classes always contain at least one positive and one negative profit.

### Arc Densities

The density parameter rho controls the expected fraction of items that have a precedence arc:

| Label | rho | Meaning |
|---|---|---|
| `sparse` | 0.3 | ~30% of items have a parent arc |
| `medium` | 0.6 | ~60% of items have a parent arc |
| `dense`  | 0.9 | ~90% of items have a parent arc |
| `tree`   | 1.0 | every item (except roots) has exactly one parent → spanning tree |

### Topologies

| Family | Generator | Precedence graph |
|---|---|---|
| `gen-forest` | random spanning forest, each edge oriented randomly | general directed forest (mixed in- and out-arcs) |
| `gen-inforest` | each node i gets at most one outgoing arc to a node j > i | in-forest (each node has out-degree ≤ 1) |
| `gen-outforest` | each node i gets at most one incoming arc from a node j < i | out-forest (each node has in-degree ≤ 1) |

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
| `runs_hfma.csv` | 9 600 | `gen-forest` medium + large sizes (20 × 240), plus `gen-inforest`/`gen-outforest` large sizes (10 + 10 × 240) |
| `runs_fma.csv`  |  2 880 | `gen-forest` medium sizes (10 × 240) plus n = 10,000, 20,000 (2 × 240) |
| `runs_hima.csv` |  2 400 | large sizes on `gen-inforest` (10 × 240) |
| `runs_homa.csv` |  2 400 | large sizes on `gen-outforest` (10 × 240) |

---

## BPPF Comparison File (`runs_bppf_and_hfma_medium.csv`)

One row per instance from the medium-sized `gen-forest` test bed (n ∈ {100, …, 1,000}, 2,400 rows total).

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

BPPF precision: 1e-6. Macroitem counts agree on 2,392/2,400 instances tested; 8 discrepancies in `weakly_corr` with medium/dense density and 700 ≤ n ≤ 900 (numerical precision issue discussed in the paper).

---

## Figures in the Paper

| Figure | Data | Description |
|---|---|---|
| Fig 12 | `runs_hfma.csv`, `runs_fma.csv` | HFMA vs FMA mean CPU time, n = 100..20,000 |
| Fig 13 | `runs_hfma.csv` | HFMA mean CPU time, n = 10,000..100,000 |
| Fig 14 | `runs_hima.csv`, `runs_homa.csv` | HIMA, HOMA, speedup over HFMA, n = 10,000..100,000 |
| Fig 15 | `runs_bppf_and_hfma_medium.csv` | HFMA vs BPPF mean CPU time, n = 100..1,000 |

To compute the mean CPU time per $n$ from any raw run file:

```python
import pandas as pd

df = pd.read_csv("data/runs_hfma.csv")
agg = df.groupby("n")["algorithm_ms"].mean().reset_index()
agg.columns = ["n", "hfma_mean_ms"]
```

---

## Complexity

- **HFMA** (general forests): O(n² log n)
- **HIMA** (in-forests) and **HOMA** (out-forests): O(n log n)

Speedup of specialized variants over HFMA on the respective topology:
- **HIMA** vs HFMA on in-forests: **7.5×–9.8×**
- **HOMA** vs HFMA on out-forests: **4.3×–5.0×**

---

## Instance Downloads

The benchmark instances are available as zip archives in the
[**v1.0 Release**](https://github.com/fabiofurini/macroitems-data/releases/tag/v1.0):

| File | Size | Contents |
|---|---|---|
| [`instances_medium.zip`](https://github.com/fabiofurini/macroitems-data/releases/download/v1.0/instances_medium.zip) | 8.5 MB | 2,400 gen-forest instances, n = 100..1,000 (all seeds) |
| [`instances_large_forest.zip`](https://github.com/fabiofurini/macroitems-data/releases/download/v1.0/instances_large_forest.zip) | 905 MB | 2,400 gen-forest instances, n = 10,000..100,000 (all seeds) |
| [`instances_large_inforest.zip`](https://github.com/fabiofurini/macroitems-data/releases/download/v1.0/instances_large_inforest.zip) | 897 MB | 2,400 gen-inforest instances, n = 10,000..100,000 (all seeds) |
| [`instances_large_outforest.zip`](https://github.com/fabiofurini/macroitems-data/releases/download/v1.0/instances_large_outforest.zip) | 887 MB | 2,400 gen-outforest instances, n = 10,000..100,000 (all seeds) |

Each zip contains flat files named `{topo}_{class}_{density}_n{n:07d}_s{seed:02d}.txt`.
The instance generator `scripts/gen_instances.py` reproduces all instances from scratch.

---

## Reproducibility

**Build the solver:**
```bash
mkdir -p build && cd build && cmake .. -DCMAKE_BUILD_TYPE=Release && make -j
```

**Generate all instances:**
```bash
python3 scripts/gen_instances.py
```

**Run benchmarks:**

```bash
# Fig 12: HFMA and FMA on medium gen-forest instances, n = 100..1,000
python3 scripts/run_fig12_hfma_fma.py

# Fig 12/13: HFMA on medium and large gen-forest instances
python3 scripts/run_hfma_medium.py
python3 scripts/run_hfma_large_forest.py

# Fig 13: HFMA on large gen-inforest/gen-outforest instances
python3 scripts/run_fig14_hfma_large_inout.py

# Fig 14: HIMA and HOMA on large gen-inforest/gen-outforest instances
python3 scripts/run_fig14_specialized_large.py

# Fig 15: BPPF vs HFMA on medium gen-forest instances, n = 100..1,000
# Requires the BPPF binary built from:
#   https://github.com/hochbaumGroup/Bounded-precision-simple-parametric
python3 ../../CODE_PARAMETRIC_PSEUDOFLOW/scripts/hpf_medium_batch.py
```

Each script writes its raw output CSV(s) to `outputs/` in the corresponding
project directory; these are the files combined into the `runs_*.csv` and
`runs_bppf_and_hfma_medium.csv` files published in `data/`.

