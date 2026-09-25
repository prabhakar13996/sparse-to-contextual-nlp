# From Sparse Features to Contextual Representations

**An empirical comparison of TF-IDF, BiLSTM, and DistilBERT for mental-health text classification.**

## Overview

This repository contains the code, experiment results, and figures for an empirical study of three NLP approaches for multi-class mental-health text classification:

- **TF-IDF + Logistic Regression**
- **BiLSTM**
- **DistilBERT**

The study examines how model performance changes as the representation moves from sparse lexical features to recurrent neural representations and contextual Transformer representations.

### Research question

> **How does the evolution from sparse lexical representations to recurrent and contextual Transformer representations affect mental-health text classification performance?**

### Research subquestions

1. How do TF-IDF, BiLSTM, and DistilBERT compare in classification performance?
2. How does training-data size affect the performance of each approach?
3. Which categories are most difficult to classify, and what linguistic characteristics contribute to errors?

## Dataset

The experiments use a cleaned mental-health text classification dataset with eight categories:

- Anxiety
- Depression
- Happy
- Mentalhealth
- Normal
- Sad
- Stress
- Suicidal

The original dataset contains **55,693 records**. During dataset preparation, conflicting text-label groups and duplicate texts were removed.

The final prepared dataset contains **54,450 examples**, split using a fixed stratified split:

- **Training:** 38,115
- **Validation:** 8,167
- **Test:** 8,168

Prepared data are stored in `data/prepared/`.

> **Data note:** The source dataset contains mental-health/social-media text. Check the applicable dataset and source terms before redistributing the raw or cleaned dataset publicly.

## Experimental Setup

### TF-IDF + Logistic Regression

TF-IDF uses lowercase text, unigram and bigram features, `min_df=2`, `max_df=0.95`, sublinear TF scaling, and up to 100,000 features. Logistic Regression uses class-balanced training.

### BiLSTM

The BiLSTM experiment uses a training-only vocabulary, maximum vocabulary size of 50,000, minimum token frequency of 2, maximum sequence length of 128, embedding dimension 100, hidden dimension 128, a bidirectional LSTM, dropout 0.3, Adam with learning rate 0.001, 5 epochs, and class-weighted cross-entropy loss.

### DistilBERT

The Transformer experiment uses `distilbert-base-uncased`, maximum sequence length 128, batch size 16, learning rate `2e-5`, 3 epochs, weight decay 0.01, 10% warm-up, AdamW, gradient clipping, and class-weighted cross-entropy loss. Checkpoints are selected using validation Macro F1.

## Evaluation

Because the dataset is class-imbalanced, **Macro F1** is the primary evaluation metric.

Additional metrics include:

- Accuracy
- Macro Precision
- Macro Recall
- Weighted F1
- Per-class F1

Confusion matrices and text-length error analysis are also included.

## Main Results

The following values are from the direct full-data test runs used for the main model comparison.

| Model | Test Accuracy | Test Macro F1 | Test Weighted F1 |
|---|---:|---:|---:|
| TF-IDF + Logistic Regression | 75.12% | 70.22% | 75.24% |
| BiLSTM | 71.78% | 64.25% | 72.27% |
| DistilBERT | 78.83% | 74.49% | 79.08% |

Under the configurations used in these experiments, **DistilBERT achieved the highest test Macro F1 among the three evaluated approaches**.

## Data-Efficiency Experiment

Training-data fractions of **10%, 25%, 50%, 75%, and 100%** were evaluated while keeping the validation and test sets fixed.

| Model | Macro F1 at 10% | Macro F1 at 100% | Absolute Gain |
|---|---:|---:|---:|
| TF-IDF + Logistic Regression | 0.6257 | 0.7022 | +0.0765 |
| BiLSTM | 0.4931 | 0.6546 | +0.1615 |
| DistilBERT | 0.6629 | 0.7528 | +0.0899 |

These results describe how each approach responds to increasing training-data size under the experimental setup used in this study.

## Error Analysis

The error analysis examines per-class F1 scores, confusion pairs, overall test errors, and error rates by text length.

A recurring confusion pattern across the models is **Depression ↔ Suicidal**.

The **Sad** and **Mentalhealth** categories have comparatively lower F1 scores, while **Normal** has substantially higher F1 across the evaluated models.

For DistilBERT, the per-class F1 values are:

| Class | F1 |
|---|---:|
| Anxiety | 0.8645 |
| Depression | 0.7224 |
| Happy | 0.7937 |
| Mentalhealth | 0.6646 |
| Normal | 0.9483 |
| Sad | 0.5596 |
| Stress | 0.6983 |
| Suicidal | 0.7081 |

The text-length analysis is descriptive and does not claim that text length alone causes classification errors.

## Repository Structure

```text
sparse-to-contextual-nlp/
├── README.md
├── .gitignore
├── data/
│   └── prepared/
│       ├── train.csv
│       ├── validation.csv
│       └── test.csv
├── notebooks/
│   ├── 01_dataset_audit.ipynb
│   ├── 02_dataset_preparation.ipynb
│   ├── 03_tfidf_baseline.ipynb
│   ├── 04_bilstm.ipynb
│   ├── 05_distilbert.ipynb
│   ├── 06_data_efficiency.ipynb
│   ├── 07_error_analysis.ipynb
│   └── 08_poster_figures.ipynb
├── results/
│   ├── bilstm_results.csv
│   ├── bilstm_test_predictions.csv
│   ├── bilstm_training_history.csv
│   ├── data_efficiency_computational_cost.csv
│   ├── data_efficiency_results.csv
│   ├── data_efficiency_summary.csv
│   ├── distilbert_results.csv
│   ├── distilbert_test_predictions.csv
│   ├── distilbert_training_history.csv
│   ├── error_analysis_confusion_pairs.csv
│   ├── error_analysis_overall_metrics.csv
│   ├── error_analysis_per_class_metrics.csv
│   ├── error_analysis_text_length.csv
│   ├── poster_main_results_table.csv
│   ├── tfidf_logistic_regression_classification_report.csv
│   ├── tfidf_logistic_regression_results.csv
│   └── tfidf_test_predictions.csv
└── figures/
    ├── poster_model_comparison_macro_f1.png
    ├── poster_model_comparison_accuracy.png
    ├── poster_data_efficiency_macro_f1.png
    ├── poster_per_class_f1.png
    ├── poster_distilbert_confusion_matrix.png
    ├── poster_error_rate_text_length.png
    └── poster_training_time.png
```

## Notebook Workflow

The notebooks are intended to be followed in numerical order:

1. **`01_dataset_audit.ipynb`** — inspect dataset quality, duplicates, labels, and text statistics.
2. **`02_dataset_preparation.ipynb`** — remove conflicting/duplicate texts and create the stratified train/validation/test split.
3. **`03_tfidf_baseline.ipynb`** — train and evaluate the TF-IDF + Logistic Regression baseline.
4. **`04_bilstm.ipynb`** — train and evaluate the BiLSTM model.
5. **`05_distilbert.ipynb`** — fine-tune and evaluate DistilBERT.
6. **`06_data_efficiency.ipynb`** — evaluate performance using different training-data fractions.
7. **`07_error_analysis.ipynb`** — analyze class-level performance, confusion patterns, and text-length errors.
8. **`08_poster_figures.ipynb`** — generate the final comparison tables and poster figures.

## Reproducibility

The experiments use fixed dataset splits and a fixed random seed of **42** where applicable.

The neural-network experiments use PyTorch, and the DistilBERT experiment uses Hugging Face Transformers. The recorded DistilBERT run used an NVIDIA GeForce RTX 3050 6GB Laptop GPU.

To reproduce the experiments:

1. Clone this repository.
2. Install the required Python packages from `requirements.txt`.
3. Place the prepared datasets in `data/prepared/`.
4. Run the notebooks in numerical order.
5. Use `08_poster_figures.ipynb` to regenerate the final figures and summary table.

## Limitations

- The study compares three representative approaches under one experimental configuration per model.
- Results depend on the dataset, fixed split, hyperparameters, random seed, and hardware/software environment used.
- Class imbalance remains an important characteristic of the dataset, which is why Macro F1 is used as the primary metric.
- Several categories are semantically close and show recurring confusion.
- Error analysis by text length is descriptive rather than causal.
- Training time is hardware- and software-dependent.
- The models are evaluated as text classifiers and are **not clinical diagnostic systems**.

## Scope

This repository is intended for **academic and research reproducibility** of the experiments described in the associated research poster. The reported results should be interpreted in the context of this dataset and experimental setup rather than as a general claim that one model family is universally superior for mental-health text classification.
