# Snapshot vs Time-Series Trajectory Inference on LARRY

Comparing three trajectory inference methods for cell fate prediction on the
[Weinreb 2020 LARRY](https://www.science.org/doi/10.1126/science.aaw3381) in-vitro
dataset — mouse hematopoietic progenitor cells tracked by clonal lineage tracing
across days 2, 4, and 6.

## Methods

| Method | Type | Description |
|--------|------|-------------|
| **Palantir** | Single-snapshot | Graph-based pseudotime on the day-2 snapshot alone. Computes diffusion maps and branch probabilities via a Markov chain. |
| **MultistageOT** | Single-snapshot | Optimal transport across consecutive time points. Learns a transport plan matching the day-2 distribution to days 4/6. |
| **PRESCIENT** | Multi-snapshot | Neural SDE trained on all time points. Simulates cell trajectories forward in time from the day-2 starting population. |

Single-snapshot methods receive only the day-2 cells as input.
Multi-snapshot methods additionally use the day-4/6 cells from one biological
replicate (Well 1) for training and are evaluated on the held-out replicate (Well 2).

## Evaluation

Predicted fate probabilities are compared against ground-truth clonal fate fractions:
for each day-2 cell with a clone assignment, we record what fraction of its
descendants (days 4/6) belong to each cell type. Metrics include per-lineage
Pearson r and the Weinreb 2020 Neutrophil–Monocyte bias score.

## Notebooks

| Notebook | Description |
|----------|-------------|
| `01_preprocessing_eda.ipynb` | Download LARRY data, preprocessing pipeline (log1p → HVG → scale → PCA → UMAP), clonal fate fraction computation |
| `02_palantir.ipynb` | Palantir trajectory inference and evaluation |

## Getting Started

```bash
pip install -r requirements.txt
```

Run notebooks in order: `01` → `02`. Notebook 01 downloads the raw data
(~2 GB) from the Klein Lab server and saves `larry_preprocessed.h5ad`,
which is required by subsequent notebooks.

**Alternative:** If CoSpar is installed, notebook 01 includes an optional
cell (`cs.datasets.hematopoiesis()`) that downloads a pre-processed version
of the data much faster than the raw MTX pipeline.

## Data

The LARRY in-vitro dataset (Weinreb et al. 2020, *Science*) is downloaded
automatically from the Klein Lab server. Raw files and processed `.h5ad`
outputs are excluded from this repository.
