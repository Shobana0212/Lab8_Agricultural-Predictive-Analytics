# MDI3003 -- Advanced Predictive Analytics

## Experiment 08: Agricultural Predictive Analytics -- Rice Yield Regression

This project develops and evaluates regression models for district-level
rice-yield prediction using historical state, district, and year
information. It emphasizes data-quality checks, leakage prevention,
chronological evaluation, reproducibility, and transparent reporting of
limitations.

**Student:** Shobana Balusamy\
**Register Number:** 23MID0212\
**Course:** MDI3003 -- Advanced Predictive Analytics\
**Semester:** Fall Semester 2026--2027

## Project files

-   `Experiment08_Rice_Predictive_Analytics.ipynb` --- main Jupyter
    notebook containing data checks, preprocessing, model training,
    evaluation, diagnostics, and acceptance checks.
-   `Rice_Data_2026-10-12.csv` --- input dataset used by the notebook.
    Place it in the same directory as the notebook.
-   `outputs_experiment08/` --- generated results, audit records, model
    artifact, plots, and evaluation outputs.
-   `MDI3003_Experiment08_Rice_Yield_Regression_Report.pdf` ---
    experiment report, if included in the repository.

## Objective

Predict rice yield for district-year observations using the available
geographic and temporal features, and compare four predeclared
regression approaches:

1.  Training-median baseline
2.  Ridge regression
3.  Decision Tree regression
4.  Random Forest regression

The target is `yield_t_ha`, measured in **tonnes per hectare (t/ha)**.
It is derived from `Yield (Kg/ha)` by dividing by 1,000.

## Dataset and features

The supplied CSV contains state, district, year, area, production, and
yield fields. The modelling feature set is:

-   `state`
-   `district`
-   `year`

`Area (Ha)` and `Production (Tonnes)` are excluded from model inputs
because they are post-harvest fields and can reveal information about
yield, creating target leakage.

**Important data limitation:** The supplied dataset does not contain a
`Season` column, although the lab specification calls for season as a
categorical feature. This notebook does not invent season values.
Instructor approval for this documented deviation remains pending. The
exact dataset publisher, source, and licence must also be verified
before redistribution or operational use.

## Evaluation design

The notebook uses a chronological split rather than random sampling:

  Partition    Years                                                 Rows
  ------------ ------------------------------------------------- --------
  Training     Available observations from 1988 and 1996--2020     13,457
  Validation   2021--2022                                           1,324
  Test         2023--2024                                             936

Preprocessing is fitted on training data only. State and district are
one-hot encoded using `OneHotEncoder(handle_unknown='ignore')`. Year is
median-imputed and standardized. Validation MAE is used to select the
model; the final test set is reserved for evaluation and must not be
used for model tuning.

## Models and main results

The evaluated settings are declared in the notebook:

-   **Median baseline:** `DummyRegressor(strategy='median')`
-   **Ridge:** `Ridge(alpha=1.0, solver='lsqr')`
-   **Decision Tree:** `max_depth=6`, `min_samples_leaf=10`,
    `random_state=42`
-   **Random Forest:** `n_estimators=60`, `max_depth=12`,
    `min_samples_leaf=5`, `n_jobs=2`, `random_state=42`

Ridge was selected because it had the lowest validation MAE.

  -----------------------------------------------------------------------
  Model              Validation MAE    Validation RMSE      Validation R²
                             (t/ha)             (t/ha) 
  -------------- ------------------ ------------------ ------------------
  Median                   0.850264           1.138439          -0.430550
  baseline                                             

  Ridge trend              0.444447           0.779771           0.328854

  Decision Tree            0.599680           0.854156           0.194699

  Random Forest            0.501631           0.768083           0.348823
  -----------------------------------------------------------------------

### Final test results

  Model                    Test MAE (t/ha)   Test RMSE (t/ha)     Test R²
  ---------------------- ----------------- ------------------ -----------
  Selected Ridge trend            0.388552           0.635615    0.523184
  Median baseline                 0.833910           1.064220   -0.336676

These results indicate that Ridge performed better than the median
baseline on the held-out 2023--2024 observations. They do not guarantee
similar performance in every future year or region.

## Requirements

The notebook uses Python and the following packages:

-   `numpy`
-   `pandas`
-   `matplotlib`
-   `scikit-learn`
-   `joblib`
-   `jupyter` or `notebook`

Install the packages with:

``` bash
python -m pip install numpy pandas matplotlib scikit-learn joblib jupyter
```

## How to run

1.  Download or clone the project repository.

2.  Place `Rice_Data_2026-10-12.csv` in the same directory as the
    notebook.

3.  Install the packages listed above.

4.  Start Jupyter:

    ``` bash
    jupyter notebook
    ```

5.  Open `Experiment08_Rice_Predictive_Analytics.ipynb`.

6.  Run the cells **from top to bottom** in a fresh session.

7.  Review the generated files in `outputs_experiment08/`.

The notebook skips the first line of the provided CSV because it
contains an HTML-style metadata comment. It does not overwrite the raw
dataset.

### Test-lock warning

The final evaluation cell creates `outputs_experiment08/TEST_LOCK`
before computing test metrics. If the lock already exists, the notebook
refuses to score the test set again. **Do not delete the lock or rerun
the test to try a different model.** Preserve the saved outputs and use
the existing results for reporting.

## Generated outputs

The notebook is designed to create the following audit and result
artifacts in `outputs_experiment08/`:

-   `provenance_record.json`
-   `duplicate_key_conflicts.csv`
-   `excluded_invalid_rows.csv`
-   `rice_canonical_audit.csv`
-   `split_manifest.csv`
-   `validation_category_audit.csv`
-   `validation_results.csv`
-   `train_validation_diagnostics.csv`
-   `selection.json`
-   `selected_pipeline_pretest.joblib`
-   `TEST_LOCK`
-   `test_results.csv`
-   `test_predictions.csv`
-   `actual_vs_predicted.png`
-   `residuals.png`
-   `largest_five_nonzero_errors.csv`
-   `year_robustness.csv`
-   `acceptance.json`
-   `rolling_origins.csv`

The exact set of files depends on how far the notebook executes
successfully.

## Reproducibility and data checks

The notebook records the raw dataset's SHA-256 hash, checks the schema,
removes exact duplicate rows, excludes conflicting state--district--year
groups, documents missing production values, and retains zero-yield
observations. It also checks chronological split separation and verifies
that the saved pipeline reproduces the test predictions after reloading.

Recorded dataset details:

-   Raw rows: **15,735**
-   Rows retained for modelling: **15,717**
-   Exact duplicate rows removed: **13**
-   Conflicting key rows excluded: **5 rows across 2
    state--district--year groups**
-   Raw SHA-256:
    `74a017708b0c084600b334bec644e11c73817438a7e0786c86498eb632d5a2b9`

## Limitations

-   The `Season` column is absent; season-specific predictions cannot be
    claimed.
-   Dataset provenance and licence/reuse terms still require
    verification.
-   Weather, soil, irrigation, water availability, rice variety, and
    management variables are not included.
-   District boundary changes and reporting differences may affect
    historical comparability.
-   The evaluation assesses temporal generalization, not generalization
    to completely unseen districts.
-   Two test years provide descriptive year-wise comparisons, not a
    reliable temporal confidence interval.
-   Error explanations are hypotheses, not established causes.
-   The results are an academic benchmark and should not be treated as a
    validated operational agronomic decision system.

## Repository

GitHub repository:
<https://github.com/Shobana0212/Lab8_Agricultural-Predictive-Analytics>
