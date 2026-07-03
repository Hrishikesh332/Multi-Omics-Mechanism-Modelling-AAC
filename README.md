## Optional W&B

To connect `02_model_development_v4.ipynb` to Weights & Biases

```bash
pip install wandb
wandb login
```

Then set the notebook configuration

```python
USE_WANDB = True
WANDB_PROJECT = "multiomics-drug-response-v4"
WANDB_ENTITY = None  # optional: W&B username or team
WANDB_MODE = "online"
```

The notebook logs the run config at start, then uploads summary metrics, result tables, CSV/JSON outputs, and PNG figures at the end. Model files are uploaded only if `WANDB_LOG_MODELS = True`.
