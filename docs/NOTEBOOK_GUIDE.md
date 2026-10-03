# Notebook guide

This guide maps the archived experiments and their execution dependencies. For project scope, recorded metrics, and evaluation limitations, start with the [README](../README.md).

## Which notebook should I read?

Use [`IDS.ipynb`](../IDS.ipynb). At the [reviewed snapshot](https://github.com/JAEUK02/SecurityProject-IDS-/tree/e4c83d37e39079752bae3cb91bf6567b105e478a), its 18 cells are exactly identical, including their saved outputs and cell metadata, to cells 1–18 of [`data_preprocessing&modeling.ipynb`](../data_preprocessing%26modeling.ipynb). The latter adds an opening Colab badge and a notebook-level Colab-link flag. It is not a separate model comparison or independent reproduction.

**Cell indices below are zero-based positions in the notebook JSON, counting both code and Markdown cells.** They are not Jupyter execution counts. All saved code-cell execution counts in these two notebooks are null. Add one to an `IDS.ipynb` index to locate the corresponding cell in the other notebook.

## Reading map

| `IDS.ipynb` index | Other notebook index | What to inspect |
| --- | --- | --- |
| 0–1 | 1–2 | Colab Drive mount and CSV loading |
| 2 | 3 | Alternative relative-path loading/preprocessing attempt; contains a syntax error |
| 3–4 | 4–5 | Active `Normal Traffic` label mapping, preprocessing shapes, and label counts |
| 6 | 7 | Imports and first `evaluate_model` function, reporting accuracy and F1 |
| 7–10 | 8–11 | Initial Logistic Regression, Random Forest, LightGBM, and GRU trials |
| 12 | 13 | Seeded Random Forest, LightGBM, and GRU retry |
| 14 | 15 | Replacement evaluation function adding precision, recall, and optional ROC-AUC |
| 15 | 16 | Final classical-model metrics and batch timings used in the README |
| 16 | 17 | Final GRU training, metrics, and batch timing used in the README |

Cells 5, 11, and 13 are section headings; cell 17 is empty. Search for `precision, recall, roc-auc` to find the final section. Earlier trials overwrite the same model variables, so a model name alone does not identify which saved result is being discussed.

## Input and environment contract

- Cell 0 imports `google.colab` and mounts Drive. This is a Colab-specific loading route, not an ordinary local Jupyter setup step.
- Cell 1 reads `/content/drive/MyDrive/infosec/cicids2017_cleaned.csv`. Cell 2 instead names `cicids2017_cleaned.csv` relative to the runtime working directory. The repository does not contain that CSV or code that produces the cleaned file.
- Cell 3 expects the target column to be named exactly `Attack Type`. It maps exactly `Normal Traffic` to 0 and **every other value** to 1. Confirm the source label distribution before relying on that mapping. The alternative cell 2 uses `Benign`; these choices are not interchangeable.
- Features are every remaining column after dropping `Attack Type`, passed directly to `StandardScaler`. There is no categorical encoder or explicit feature-column allowlist. The saved output has 52 features, but this does not define their complete schema or establish the provenance of a replacement CSV.
- NumPy, pandas, scikit-learn, LightGBM, TensorFlow/Keras, and matplotlib appear in imports. A notebook frontend is needed for local inspection; the original dependency versions and hardware are not recorded as an installable environment.

Viewing saved results on GitHub does not require the CSV or training libraries. A new run requires a separately prepared, consistent loading/preprocessing path and a documented dataset and environment. Seeds in later cells do not reconstruct the original environment or guarantee identical results across environments.

## State dependencies and known blockers

1. Cell 2 fails Python parsing at the trailing `on` on its label-mapping line. Its earlier statements do not run before this syntax error. Merely adding the CSV does not make a top-to-bottom run work.
2. Cell 3 consumes an existing `df`; it does not load its own data. In the Drive route, that state comes from cell 1. Resolve the competing loading and label-mapping paths before attempting a new experiment.
3. Cell 14 replaces `evaluate_model`. Final cells 15–16 must use this version; otherwise the LightGBM/GRU calls that supply `probas` are incompatible with the earlier function signature.
4. Cell 15 requires the prepared train/test arrays and the cell-14 function. Cell 16 also relies on imports, timing utilities, and seeded runtime state established earlier, notably in cell 15. Running the final GRU cell alone is not a self-contained experiment.
5. Cells 7–10, 12, and 15–16 contain repeated training runs. Reading the final saved metrics does not require executing the earlier trials.

These are code-inspection findings, not a tested execution recipe. The existing preprocessing and threshold-calibration leakage described in the README still needs correction before a stronger evaluation.

## Saved output provenance

Some saved errors and current source are inconsistent:

- Cell 2 stores a `FileNotFoundError`, although its current source has a syntax error that would prevent execution.
- Cell 15 stores a `NameError` for `gru_ae` after the classical-model outputs. Its current source contains no `gru_ae` reference; the archived traceback refers to code not present in that cell now.

Consequently, the outputs are an archive of interactive work, not evidence of a successful clean run of the exact currently committed source. The README values are transcriptions of the final saved outputs. A future reproduction should retain the original archive, record the revised source and environment, and report newly generated results separately.

## What the timers actually measure

- Cell 15 times the `predict(X_test)` call for Logistic Regression/Random Forest and the LightGBM score-prediction call. Loading, cleaning, scaling, fitting, and metric computation are outside those timers. LightGBM's conversion from scores to binary labels is also outside its timer.
- Cell 16 first makes an untimed GRU prediction and then times a second prediction. Reconstruction-error calculation and anomaly thresholding follow the timer. This is a warmed repeat prediction call, not a measured end-to-end detector.
- Printed per-sample values divide a batch duration by the number of test rows and round to six decimal places. The saved Logistic Regression value of `0.000000` is rounding, not zero latency; these averages are not individual-request latency measurements.
- Final ROC-AUC is printed for LightGBM and the GRU. The GRU passes reconstruction MSE as the ranking score, not a calibrated attack probability. The final Logistic Regression/Random Forest calls omit `probas`, so they do not print ROC-AUC.

## Review record

Reviewed from the committed [IDS source](https://github.com/JAEUK02/SecurityProject-IDS-/blob/e4c83d37e39079752bae3cb91bf6567b105e478a/IDS.ipynb) and [companion source](https://github.com/JAEUK02/SecurityProject-IDS-/blob/e4c83d37e39079752bae3cb91bf6567b105e478a/data_preprocessing%26modeling.ipynb). Notebook JSON, source, saved outputs, and cell equivalence were inspected; code cells were syntax-parsed without execution. No dataset retrieval, training, or fresh metric verification was performed.
