# Mechanism-Aware Drug Response Prediction with Multi-Omics Data

This project investigates how molecular profiles explain differences in cancer drug response. It combines cancer cell-line data from multiple omics layers with CTRPv2 drug-response measurements to compare prediction strategies and identify biologically meaningful drivers of sensitivity.

Goal for the project is

> Which molecular data types are most predictive of cancer drug response, and does their importance differ across drug mechanism-of-action (MOA) classes?


### Final model result comparison

The table below provides the top ten configurations from the full 426-compound run. Metrics are averaged across 2,130 compound-fold evaluations (426 compounds x 5 folds).

| Rank | Model | Strategy | Features | Pearson | Spearman | R-squared | RMSE |
| ---: | --- | --- | --- | ---: | ---: | ---: | ---: |
| 1 | XGBoost | Early fusion | Multi-omics | **0.4175** | **0.3700** | **0.1800** | **0.0855** |
| 2 | XGBoost | Early fusion + lineage | Multi-omics + lineage | 0.4174 | 0.3693 | 0.1798 | 0.0855 |
| 3 | XGBoost | Metadata augmented | Expression + lineage | 0.4174 | 0.3694 | 0.1789 | 0.0856 |
| 4 | XGBoost | Single omics | Expression | 0.4163 | 0.3683 | 0.1778 | 0.0857 |
| 5 | LightGBM | Early fusion + lineage | Multi-omics + lineage | 0.4087 | 0.3615 | 0.1727 | 0.0860 |
| 6 | LightGBM | Early fusion | Multi-omics | 0.4085 | 0.3612 | 0.1723 | 0.0861 |
| 7 | LightGBM | Single omics | Expression | 0.4054 | 0.3576 | 0.1690 | 0.0863 |
| 8 | LightGBM | Metadata augmented | Expression + lineage | 0.4052 | 0.3579 | 0.1691 | 0.0863 |
| 9 | XGBoost | Late fusion | Expression + mutations + CNV | 0.4016 | 0.3507 | 0.1397 | 0.0885 |
| 10 | LightGBM | Late fusion | Expression + mutations + CNV | 0.3924 | 0.3418 | 0.1320 | 0.0889 |

### 1. Clone the repository

```bash
git clone https://github.com/Hrishikesh332/Multi-Omics-Mechanism-Modelling-AAC.git
cd Multi-Omics-Mechanism-Modelling-AAC
```

### 2. Create an environment


```bash
python3 -m venv .venv
source .venv/bin/activate
python -m pip install jupyter pandas numpy scipy scikit-learn umap-learn \
  matplotlib seaborn requests pyarrow tqdm upsetplot joblib \
  xgboost lightgbm shap
```

### 3. Run the workflow


Run the notebooks in this order

1. `01_data_prep_eda_v4.ipynb`
2. `02_model_development_v4.ipynb`
3. `03_shap_moa_analysis_v2_426.ipynb`

The full 426-compound model comparison is computationally expensive. For a smaller validation run, set `RUN_FULL_MODELING = False` in `02_model_development_v4.ipynb` before execution.

## Modeling design

- **Target -** compound specific AAC values.
- **Eligibility -** at least 250 observed cell lines and AAC standard deviation of at least 0.04.
- **Final omics layers -** expression, mutations, and CNV.
- **Integration -** single-omics, early fusion, simple late fusion, learned late fusion, and lineage augmentation.
- **Models -** ElasticNet, RidgeCV, RidgeSelectK, PLSRegression, XGBoost, and LightGBM.
- **Evaluation -** Pearson correlation, Spearman correlation, RMSE, MAE, and R-squared.
- **Validation -** five-fold cross-validation with scaling and batch correction fitted only on training folds.
- **Interpretation -** fold-averaged SHAP values summarized by feature, omics layer, compound, and MOA class.

Random seeds are fixed to 42 where supported. Feature scaling, selection, and batch correction are applied within training folds to reduce information leakage.

## Optional W&B tracking

Weights & Biases logging is disabled by default.

```bash
python -m pip install wandb
wandb login
```

Then update the configuration in `02_model_development_v4.ipynb`:

```python
USE_WANDB = True
WANDB_PROJECT = "multiomics-drug-response-v4"
WANDB_ENTITY = None
WANDB_MODE = "online"
```

Model files are uploaded only when `WANDB_LOG_MODELS = True`