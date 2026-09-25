# Dataset

The dataset used in this project was created from Reddit-derived text collected for this study.

## Data Preparation

The collected data was processed through the following steps:

1. Data collection from Reddit
2. Text cleaning and preprocessing
3. Label preparation
4. Removal of conflicting text-label instances
5. Removal of duplicate texts
6. Stratified train/validation/test splitting

The final prepared dataset contains **54,450 text instances** across eight mental-health-related categories.

### Dataset Split

| Split | Instances |
|---|---:|
| Training | 38,115 |
| Validation | 8,167 |
| Test | 8,168 |
| **Total** | **54,450** |

## Data Availability

The collected Reddit text and the processed dataset files are **not included in this public repository**.

The data consists of user-generated Reddit content. Therefore, the repository does not redistribute the collected text. Researchers wishing to reproduce the experiments should independently collect or obtain appropriate data while following the applicable Reddit policies, legal requirements, and ethical research guidelines.

The complete data preparation procedure is documented in:

```text
notebooks/01_dataset_audit.ipynb
notebooks/02_dataset_preparation.ipynb
