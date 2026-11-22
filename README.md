# Behavioral Linkability of EV Charging Sessions — Analysis Code

Code for the paper *"Behavioral Linkability of Electric Vehicle Charging
Sessions in ISO 15118-Enabled Ecosystems: A Privacy–Utility Evaluation"*.

The repository holds the full evaluation pipeline: data acquisition,
preprocessing, per-user chronological splitting, pair construction with hard
negatives, pairwise verification models, candidate-set retrieval (pooled and
site-matched), feature ablation, metadata-generalization evaluation with
operational-utility metrics, and figure generation. A test suite checks the
leakage controls: the temporal split, train/test pair disjointness, and the
feature allowlist.

## Data

The pipeline works on ACN-Data (Caltech and JPL sites), which it downloads
through the `acnportal` client and then processes locally. Charging records
stay with the data provider; every intermediate file, table, and figure is
rebuilt from a fresh download.

## Requirements

- Python ≥ 3.8 (the paper's results were produced with Python 3.8.8)
- numpy 1.24.4, scikit-learn 1.3.2, matplotlib, pytz, pytest
- acnportal 0.3.3 (for the download step)

```
pip install -r requirements.txt
```

## Reproducing the study

1. Request a free ACN-Data API token at https://ev.caltech.edu/dataset
2. `export ACNDATA_API_TOKEN="..."`
3. `./run_all.sh` runs the test suite, downloads the two sites if they are
   absent, and executes the main analysis: pairwise verification, feature
   ablation, pooled retrieval, mitigation and utility, and the figures.

Optional stages:

- `RUN_SITE_MATCHED=1 ./run_all.sh` adds the site-matched retrieval runs
  (same-site candidates for V1, V2, and V2 without the site indicator, plus
  the models trained on same-site pairs).
- `RUN_SWEEP=1 ./run_all.sh` adds the minimum-session threshold sweep
  (10, 15, 30).
- `N_BOOT=200 ./run_all.sh` uses a smaller bootstrap for a quick pass.

Every stage uses the fixed seed 20260712. A timestamped run manifest is
written to `outputs/logs/run_manifest.txt`, tables to `outputs/tables/`, and
figures to `outputs/figures/`.

## Layout

```
run_all.sh    one-command orchestration of the study
src/          pipeline stages, in the order run_all.sh calls them
tests/        leakage and correctness tests (pytest)
```

## License

MIT; see `LICENSE`.
