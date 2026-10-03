# Machine Learning for Intrusion Detection

**A course project comparing classical classifiers and a GRU autoencoder on network-traffic data.** The notebooks examine detection quality alongside batch inference time, including the effect of weak attack recall despite high precision.

**Start here:** [`IDS.ipynb`](IDS.ipynb) for the saved experiments and final evaluation cells. The repository also contains [`data_preprocessing&modeling.ipynb`](data_preprocessing%26modeling.ipynb), the same 18 experiment cells with an extra opening Colab badge.

**Review guide:** [cell map, input contract, saved-output provenance, and timing boundaries](docs/NOTEBOOK_GUIDE.md).

## Problem and contribution record

Intrusion detection involves class imbalance: a useful comparison needs precision, recall, and F1 alongside accuracy. These notebooks compare Logistic Regression, Random Forest, LightGBM, and a GRU autoencoder using a binary normal/attack target.

The public contribution record includes [JAEUK02's notebook upload](https://github.com/JAEUK02/SecurityProject-IDS-/commit/cb7a16cd4347eb132f966361493fa2ef1f31e7a6). This repository provides the code and saved outputs from the course work; detailed allocation of the related team project's responsibilities is not recorded here.

## Implementation

1. Load an external `cicids2017_cleaned.csv` file and inspect the class distribution.
2. Replace infinite values with missing values and drop incomplete rows.
3. Convert the chosen normal label to `0` and attacks to `1`, standardize numeric features, and use a stratified 70/30 split.
4. Train classical classifiers and a GRU autoencoder trained on normal rows.
5. Inspect confusion matrices, precision, recall, F1, ROC-AUC where available, and batch prediction timings.

**Stack:** Python, pandas, NumPy, scikit-learn, LightGBM, TensorFlow/Keras, and matplotlib. The notebooks were written for a Colab/Google Drive workflow.

## Archived results

These values are transcribed from the **final saved evaluation outputs**, not a new reproduction. Earlier cells contain other trial outputs. Some archived errors do not match the current cell source, so these outputs do not establish a clean run of the committed notebook; see the [provenance notes](docs/NOTEBOOK_GUIDE.md#saved-output-provenance). The saved preprocessing output reports 1,764,525 training rows and 756,226 test rows, with 52 features.

| Model | Precision | Recall | F1 | Recorded batch inference time |
| --- | ---: | ---: | ---: | ---: |
| Logistic Regression | 0.8780 | 0.8497 | 0.8636 | 0.0730 s |
| Random Forest | 0.9964 | 0.9949 | 0.9956 | 7.0169 s |
| LightGBM | 0.9934 | 0.9996 | 0.9965 | 3.0272 s |
| GRU autoencoder | 0.9838 | 0.3148 | 0.4770 | 63.2865 s |

The saved run illustrates a concrete tradeoff: the GRU model's high precision coexists with low attack recall, while LightGBM has stronger recall and F1 in this particular experiment. These are offline batch measurements in an undocumented execution environment; they do not establish a production latency target or detection guarantee.

## Files and inspection

| File | Purpose |
| --- | --- |
| [`IDS.ipynb`](IDS.ipynb) | Preprocessing, successive model trials, final metrics, and timing outputs |
| [`data_preprocessing&modeling.ipynb`](data_preprocessing%26modeling.ipynb) | Related preprocessing/modeling notebook, with largely overlapping experiments |

GitHub can render the existing notebook outputs without running the code. To explore in a compatible Python environment with the imported libraries installed:

```bash
git clone https://github.com/JAEUK02/SecurityProject-IDS-.git
cd SecurityProject-IDS-
jupyter notebook IDS.ipynb
```

A fresh training run additionally requires the cleaned CSV, the notebook's expected label names and columns, a consistent preprocessing path, and the Google Drive setup used by the initial loading cells. The data file and a dependency lock are not included.

## Status and reproducibility limits

This repository preserves coursework experiments and their saved outputs. A full top-to-bottom run currently needs a separate code cleanup:

- An early preprocessing cell contains a trailing `on` syntax error. The following preprocessing cell uses a different normal-label mapping.
- `StandardScaler` is fitted before the train/test split, so test-distribution information enters preprocessing.
- The GRU anomaly threshold is calculated using normal-labelled test rows. A stronger evaluation would calibrate this on separate validation data.
- The GRU uses a sequence length of one; this setup does not establish temporal traffic modeling or unseen-attack detection performance.
- The runtime environment, cleaned dataset provenance, and exact dependency versions are not packaged for reproducibility.

These limitations should accompany the archived metrics when discussing the project. No notebook code, training data, or evaluation output was changed by this documentation update.
