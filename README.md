# Snapshot vs Time-Series Trajectory Inference on LARRY

Comparing three trajectory inference methods for cell fate prediction on the
[Weinreb 2020 LARRY](https://www.science.org/doi/10.1126/science.aaw3381) in-vitro
dataset — mouse hematopoietic progenitor cells tracked by clonal lineage tracing
across days 2, 4, and 6.

## Methods

| Method | Type | Description |
|--------|------|-------------|
| **Palantir** | Single-snapshot | Graph-based pseudotime on the day-2 snapshot alone. Computes diffusion maps and branch probabilities via a Markov chain. |
| **MultistageOT** | Single-snapshot | Models cell differentiation as a series of intermediate optimal-transport stages within a single snapshot. Uses global information across all cells and differentiation stages to infer coherent trajectories from initial to terminal states. |
| **PRESCIENT** | Multi-snapshot | Generative neural SDE (Yeo et al. 2021) trained on all time points. Simulates cell trajectories forward in time from the day-2 starting population to predict fate probabilities. |

Single-snapshot methods receive only the day-2 cells as input.
Multi-snapshot methods additionally use the day-4/6 cells from the training wells
(Wells 0+1) for training and are evaluated on the held-out replicate (Well 2).

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
| `03_multistage.ipynb` | MultistageOT trajectory inference and evaluation |
| `04_prescient.ipynb` | PRESCIENT neural SDE training and evaluation (runs on Google Colab) |

## Getting Started

```bash
pip install -r requirements.txt
```

Run notebooks in order: `01` → `02` → `03`. Notebook `04` (PRESCIENT) runs on
Google Colab — see the notebook header for setup instructions.

Notebook 01 downloads the raw data (~2 GB) from the Klein Lab server and saves
`larry_preprocessed.h5ad`, which is required by all subsequent notebooks.

**Alternative (faster):** Notebook 01 includes a section 2b that uses
`cs.datasets.hematopoiesis()` to download a pre-processed ~150 MB h5ad instead
of the full ~2 GB raw MTX files. This path remaps CoSpar's column names to match
downstream notebook conventions automatically. Limitations: only the 49k cloned
cells are included (vs 130k full dataset), no Well-based train/test split, and
no raw expression layers — so notebook 04 (PRESCIENT) requires the full raw pipeline.

## Data

The LARRY in-vitro dataset (Weinreb et al. 2020, *Science*) is downloaded
automatically from the Klein Lab server. Raw files and processed `.h5ad`
outputs are excluded from this repository.
