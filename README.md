# GameScape Analytics

A distributed PySpark pipeline on Google Cloud Dataproc that processes
113M Steam reviews (~40GB) to predict indie game success rates.

## Architecture

Bronze (raw CSV from GCS) → Silver (cleaned Parquet) → Gold (ML-ready dataset)

| Layer | Description |
|-------|-------------|
| Bronze | Raw ingestion from GCS, schema validation, Parquet conversion |
| Silver | English-only filtering, null removal, deduplication, type normalization |
| Gold | Game-level aggregation, feature vectorization, ML-ready output |

## Results

| Model | R² | RMSE | MAE |
|-------|----|------|-----|
| Linear Regression | 0.43 | 0.1289 | 0.0956 |
| Gradient Boosted Trees | 0.57 | 0.1124 | 0.0823 |
| **Random Forest** | **0.61** | **0.1052** | **0.0776** |

**Key finding:** Average playtime is the strongest predictor of game success
(importance 0.28) — 1.86x the next-ranked feature (Genre: RPG at 0.15).

## Scaling

| Data Size | Rows | Total Time |
|-----------|------|------------|
| 5% | 5.7M | 5 min |
| 25% | 28.4M | 20 min |
| 50% | 56.7M | 34 min |
| 100% | 113.4M | 60 min |

20x data increase → only 12x runtime increase (sub-linear scaling on
a fixed 2-worker cluster).

## Tech Stack
Python · PySpark · Google Cloud Dataproc · Cloud Storage · MLlib

## Dataset
- [Steam Reviews — Kaggle](https://www.kaggle.com/datasets/kieranpoc/steam-reviews)
- [Steam Store Games — Kaggle](https://www.kaggle.com/datasets/nikdavis/steam-store-games)

## Files
- `pre_processing.py` — Bronze and Silver layer ETL pipeline
- `ml_pipeline.py` — Gold layer feature engineering and model training
```
