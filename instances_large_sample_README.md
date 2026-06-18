# Large Test Bed Instances — Sample

Benchmark instances for the paper:

> **"Macroitems: Optimal LP Solutions of the Precedence Constrained Knapsack Problem"**  
> Submitted to *INFORMS Mathematics of Operations Research*

---

## Contents

This archive contains a **representative sample** of the large test bed:
**one instance per (topology, profit-weight class, arc density, size)**
(seed = 01 only), plus the generator.

```
instances/
  forest/       gen-forest instances,   n = 10 000..100 000, seed=01  (240 files)
  inforest/     gen-inforest instances, n = 10 000..100 000, seed=01  (240 files)
  outforest/    gen-outforest instances,n = 10 000..100 000, seed=01  (240 files)
gen_instances.py
```

**Total**: 720 files (240 per topology)

> **Note**: The full test bed has 10 seeds per combination (7 200 instances per
> topology, 21 600 total). The full set is too large for direct distribution
> (~6 GB uncompressed). Use `gen_instances.py` to regenerate all 10 seeds.

---

## Instance Parameters

| Parameter | Values |
|---|---|
| **Sizes** $n$ | 10 000, 20 000, 30 000, 40 000, 50 000, 60 000, 70 000, 80 000, 90 000, 100 000 |
| **Topologies** | `gen-forest` (general directed forests), `gen-inforest` (in-trees), `gen-outforest` (out-trees) |
| **Profit-weight class** | `uncorr`, `weakly_corr`, `strongly_corr`, `neg_uncorr`, `neg_weakly_corr`, `neg_strongly_corr` |
| **Arc density** | `sparse` ($\rho=0.3$), `medium` ($\rho=0.6$), `dense` ($\rho=0.9$), `tree` ($\rho=1.0$) |
| **Seeds in this archive** | seed = 01 only |

---

## Topologies

| Directory | Description | Algorithm |
|---|---|---|
| `forest/` | General directed forests | HFMA (`forest`) |
| `inforest/` | Forests of in-trees (all arcs point toward roots) | HIMA (`inforest`) |
| `outforest/` | Forests of out-trees (all arcs point away from roots) | HOMA (`outforest`) |

---

## Profit-Weight Classes and Arc Density

See `instances_small_medium_README.md` for full descriptions (same conventions
apply at all sizes).

---

## Instance File Format

Same as the small/medium instances:

```
n 10000
profits  770 655 ...
weights  10 673 ...
arcs 2993
1 4
...
```

---

## Regenerating the Full Test Bed

To regenerate all 10 seeds for all sizes and topologies:

```bash
python3 gen_instances.py
```

The `SIZES` list in the script controls which sizes are generated (currently
includes all sizes from 10 to 100 000). This takes several minutes and
requires ~6 GB of disk space.
