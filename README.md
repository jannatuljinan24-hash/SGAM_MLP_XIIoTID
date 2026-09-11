# SGAM-MLP-XIIoTID

*SGAM-MLP stands for Source-Group-Aware Masking Multi-Layer Perceptron.*

This repository contains the code and final experiment notebook for our work on robust multiclass intrusion detection using the X-IIoTID dataset.

The main idea of this study is to look at what happens when a complete group of features becomes unavailable during inference. In a real IIoT monitoring environment, network, resource, or activity information may not always be available at the same time. Our proposed model, SGAM-MLP, was designed to make the classifier less dependent on any single source group.

## Project Overview

We used the X-IIoTID dataset for 10-class intrusion detection.

After preprocessing and leakage screening, the experiment used:

- 820,834 records
- 56 active raw predictors
- 134 encoded model inputs
- 26 Network features
- 24 Resource features
- 6 Activity features

The data was divided into training, validation, and frozen test sets using an approximately 70/15/15 split.

Three fixed random seeds were used throughout the experiments:

`42, 123, 2026`

This gave a total of 15 model-seed runs.

## Models

Five models were included in the final comparison:

1. Class-Weighted XGBoost
2. Standard MLP
3. Class-Weighted MLP
4. Random Feature Masking MLP
5. SGAM-MLP (Proposed)

The proposed SGAM-MLP uses class-weighted learning together with source-group-aware masking during training.

Instead of randomly hiding unrelated individual features, the model is trained by temporarily masking a complete Network, Resource, or Activity feature group. The purpose is to make the model learn from the remaining sources when one source group is unavailable.

The MLP architecture used in the experiment is:

`134 → 128 → 64 → 10`

with ReLU activation, dropout, AdamW optimization, and early stopping.

## Evaluation

The models were evaluated on the frozen test set under both complete and degraded-input conditions.

The main degraded conditions were:

- No Network
- No Resource
- No Activity

Matched-size random masking conditions were also included as controls.

The main evaluation metric was Macro-F1 because the dataset contains multiple classes with different levels of representation.

## Main Results

On complete input, Class-Weighted XGBoost produced the highest Macro-F1 of `0.9950`.

SGAM-MLP achieved a complete-input Macro-F1 of:

`0.9790`

The main goal of SGAM-MLP, however, was robustness when a complete source group was unavailable.

The mean primary robustness Macro-F1 was:

| Model | Primary Macro-F1 |
|---|---:|
| SGAM-MLP (Proposed) | 0.8363 |
| Random Feature Masking MLP | 0.7431 |
| Class-Weighted XGBoost | 0.6084 |
| Standard MLP | 0.4736 |
| Class-Weighted MLP | 0.4539 |

For SGAM-MLP, the individual source-removal results were:

| Condition | Macro-F1 |
|---|---:|
| No Activity | 0.9729 |
| No Resource | 0.9021 |
| No Network | 0.6338 |

Network-feature loss was the most difficult condition for all of the evaluated models.

The paired group-bootstrap comparison between SGAM-MLP and Random Feature Masking MLP showed an improvement of `9.318` percentage points, with a 95% interval of `8.912–9.685`.

## Additional Analysis

The notebook also includes:

- Validation Macro-F1 comparison
- Training and validation loss curves
- Robustness comparison across feature-loss conditions
- Confusion matrices for all five models
- Class-wise recall comparison
- ROC and Precision-Recall curves
- Paired group-bootstrap analysis
- Friedman test
- Wilcoxon post-hoc tests
- Holm correction
- Rank-biserial effect size
- LIME local explanation
- SHAP global feature importance
- Missingness-only diagnostic
- Model size and runtime summary

## Notebook

The main notebook in this repository is:

`SGAM_MLP_XIIoTID_Final.ipynb`

The saved notebook contains the final outputs from all 15 planned model-seed runs.

## Running the Notebook

The experiments were developed and run in Google Colab.

For a fresh reproduction, the notebook follows four stages:

`PREPARE → TRAIN → FINAL → REPORT`

### PREPARE

Loads the dataset, performs preprocessing, creates the data splits, and prepares the encoded features.

### TRAIN

Trains the five models using the three fixed seeds.

### FINAL

Performs the frozen-test evaluation using the completed model artifacts.

### REPORT

Generates the final tables, figures, statistical analysis, and explainability outputs from the saved results.

If the trained artifacts and evaluation results already exist in Google Drive, the `REPORT` stage can be run without retraining the models.

## Dataset

The X-IIoTID dataset used in this study is publicly available.

Official repository:
https://github.com/Alhawawreh/X-IIoTID

Kaggle dataset page:
https://www.kaggle.com/datasets/munaalhawawreh/xiiotid-iiot-intrusion-dataset

The raw dataset is not included in this repository because of its size.
The notebook expects the dataset file to be available separately as:

`X-IIoTID_dataset.csv`

Users who want to reproduce the experiments can download the dataset from one of the sources above and update the dataset path in the notebook if needed.

## Reproducibility Note

All preprocessing parameters were learned from the training partition and then reused for validation and testing.

The test set was kept frozen during model selection and training.

The three fixed seeds were used to report training variability. They should not be interpreted as independent datasets.

The repository keeps the final outputs visible so that the reported experiment can be inspected without rerunning the full training process.

## Authors

**Jannatul Jinan**  
Email: jannatuljinan24@gmail.com

**Jannatul Ferdouse Shifa**  
Email: jannatulshifa067@gmail.com
