# Small and Medium Test Bed Instances

Benchmark instances for the paper:

> **"Macroitems: Optimal LP Solutions of the Precedence Constrained Knapsack Problem"**  
> Submitted to *INFORMS Mathematics of Operations Research*

---

## Contents

This archive contains all **4 560 instances** of the small and medium test beds,
plus the Python generator used to create them.

```
instances/
  forest/
    forest_{kp_class}_{density}_n{n:07d}_s{seed:02d}.txt   (4 560 files)
gen_instances.py
```

**Size range**: $n \in \{10, 20, \ldots, 100, 200, \ldots, 1\,000\}$ (19 values)  
**Topology**: directed forests (`gen-forest`)

---

## Instance Parameters

Each size $n$ is crossed with:

| Parameter | Values |
|---|---|
| **Profit-weight class** | `uncorr`, `weakly_corr`, `strongly_corr`, `neg_uncorr`, `neg_weakly_corr`, `neg_strongly_corr` |
| **Arc density** | `sparse` ($\rho=0.3$), `medium` ($\rho=0.6$), `dense` ($\rho=0.9$), `tree` ($\rho=1.0$) |
| **Random seed** | 1, 2, …, 10 |

→ **240 instances per size** (6 × 4 × 10)

---

## Profit-Weight Classes

Based on Pisinger (1994), parameter $r = 1\,000$:

| Class | Weights | Profits |
|---|---|---|
| `uncorr` | $w \sim U[1,r]$ | $p \sim U[1,r]$ |
| `weakly_corr` | $w \sim U[1,r]$ | $p \sim U[\max(1,w-r/10),\, w+r/10]$ |
| `strongly_corr` | $w \sim U[1,r]$ | $p = w + r/10$ |
| `neg_uncorr` | as `uncorr` | with controlled negative profits |
| `neg_weakly_corr` | as `weakly_corr` | with controlled negative profits |
| `neg_strongly_corr` | as `strongly_corr` | with controlled negative profits |

---

## Arc Density

Arc density $\rho$ controls the expected number of arcs relative to a spanning tree:

| Label | $\rho$ | Arcs (expected) |
|---|---|---|
| `sparse` | 0.3 | $\approx 0.3(n-1)$ |
| `medium` | 0.6 | $\approx 0.6(n-1)$ |
| `dense` | 0.9 | $\approx 0.9(n-1)$ |
| `tree` | 1.0 | exactly $n-1$ (single spanning tree) |

---

## Instance File Format

Plain text, comments start with `#`:

```
n 100
profits  770 655 676 ...
weights  10 673 626 ...
arcs 27
1 4
2 7
...
```

- `n`: number of items  
- `profits`: integer profits (1-indexed)  
- `weights`: integer weights (1-indexed)  
- `arcs k`: followed by `k` lines `u v` (item `u` must be selected before item `v`)

---

## Regenerating Instances

To regenerate all instances (small, medium, and large) from scratch:

```bash
python3 gen_instances.py
```

The script creates `instances/forest/`, `instances/inforest/`, `instances/outforest/`
with 20 880 instances total. Edit the `SIZES` list in the script to control
which sizes are generated.
