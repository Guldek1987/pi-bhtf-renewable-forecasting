# PI-BHTF: Physics-Informed Bayesian Hybrid Temporal Forecasting for Renewable Generation

> Calibrated multi-horizon forecasting of solar, wind, and total renewable generation with uncertainty quantification, target–horizon analysis, ablation study, and locked external SCADA validation.

---

## Repository Overview

This repository contains the full experimental pipeline for **physics-informed Bayesian multi-horizon forecasting** of renewable energy generation. The study focuses on predicting three target families:

- **Solar generation**
- **Wind generation**
- **Total solar–wind renewable generation**

for five forecast horizons:

- **H1**
- **H3**
- **H6**
- **H12**
- **H24**

The central objective is not only to minimize point forecasting error, but also to produce **calibrated uncertainty estimates** and evaluate model behavior across individual **target–horizon tasks**. The proposed model, **PI-BHTF** (*Physics-Informed Bayesian Hybrid Temporal Forecaster*), is compared against classical machine learning, boosting, probabilistic, deep learning, and compact SOTA-style models.

---

## Research Motivation

Renewable generation forecasting is challenging because solar and wind power are driven by different physical mechanisms and meteorological processes. Solar generation is strongly associated with irradiance, daylight cycles, temperature, and seasonal effects, while wind generation depends on wind speed, air density, and nonlinear power-curve behavior.

This project addresses these challenges through:

- **Multi-horizon forecasting** rather than a single-step forecast;
- **Separate evaluation of solar, wind, and total generation**;
- **Target–horizon analysis** instead of only averaged metrics;
- **Physics-informed feature engineering**;
- **Bayesian uncertainty estimation**;
- **Conformal interval calibration**;
- **Locked external validation** on independent SCADA data.

---

## Datasets

Two open datasets were used with different roles in the experimental design.

| Dataset role | Dataset | Link | Use in this project |
|---|---|---|---|
| Main dataset | Wind and solar power generation dataset | https://data.mendeley.com/datasets/gxc6j5btrx/1 | Training, validation, internal testing, feature engineering, model selection |
| External dataset | Solar and wind power data from the Chinese State Grid Renewable Energy Generation Forecasting Competition | https://figshare.com/articles/dataset/Solar_and_wind_power_data_from_the_Chinese_State_Grid_Renewable_Energy_Generation_Forecasting_Competition/17304221 | Locked external validation only |

### Main Dataset

The main Mendeley dataset contains time series of solar and wind power generation together with meteorological predictors, including:

- Wind speed
- Air density
- Temperature
- Humidity
- Ground irradiance
- Atmospheric irradiance
- Solar power generation
- Wind power generation

It was used for:

- Model training;
- Validation-based model selection;
- Hyperparameter selection;
- Internal testing;
- Multi-horizon target construction;
- Feature engineering and preprocessing.

### External Dataset

The external Chinese State Grid SCADA dataset contains real measurements from:

- **8 solar stations**
- **6 wind farms**
- **2019–2020 observation period**
- **15-minute original temporal resolution**

In this project, it was used only for **locked external validation**. It was not used for model training, hyperparameter tuning, feature selection, calibration, or model selection.

---

## Data Processing Pipeline

The data preparation procedure was designed to avoid data leakage and ensure that all transformations were compatible with both internal and external validation.

### Main processing steps

1. **Raw data ingestion**
   - Official dataset files were loaded and audited.
   - Main and external sources were kept logically separated.

2. **Schema harmonization**
   - Columns were mapped to a unified English feature schema:
     - `timestamp`
     - `solar_power`
     - `wind_power`
     - `total_power`
     - `wind_speed`
     - `air_density`
     - `temperature`
     - `humidity`
     - `irradiance_ground`
     - `irradiance_atmospheric`

3. **Temporal integrity checks**
   - Timestamp consistency was verified.
   - Duplicate rows and duplicate timestamp–site–energy combinations were checked.
   - The external SCADA data were aggregated from 15-minute resolution to hourly resolution.

4. **Chronological split**
   - The main dataset was split chronologically:
     - **70% train**
     - **15% validation**
     - **15% internal test**
   - No random shuffling was used.

5. **Locked external validation**
   - The Chinese State Grid dataset was kept as an independent external test source.
   - External data were transformed only using train-fitted preprocessing parameters.

6. **Capacity normalization**
   - Solar, wind, and total generation were normalized to comparable scales.
   - This prevented absolute capacity differences from dominating the error metrics.

7. **Feature engineering**
   - Lag features
   - Rolling statistics
   - Ramp features
   - Volatility features
   - Calendar-cycle features
   - Solar irradiance-based features
   - Wind speed / air density interaction features

8. **Multi-horizon target construction**
   - Targets were generated for:
     - Solar: H1, H3, H6, H12, H24
     - Wind: H1, H3, H6, H12, H24
     - Total: H1, H3, H6, H12, H24

This produced **15 target–horizon forecasting tasks**.

---

## Final Modeling Matrices

| Component | Description |
|---|---|
| Predictor matrix | 422 engineered predictors |
| Target matrix | 15 target–horizon outputs |
| Main splits | Train, validation, internal test |
| External split | Locked external validation |
| Target families | Solar, wind, total generation |
| Forecast horizons | H1, H3, H6, H12, H24 |

---

## Notebook Structure

The project is organized into six sequential notebooks.

| Order | Notebook | Main purpose |
|---:|---|---|
| 01 | `01_data_ingestion_raw_eda.ipynb` | Raw data loading, dataset inspection, initial exploratory analysis, timestamp checks, external SCADA structure review |
| 02 | `02_preprocessing_feature_engineering_analysis.ipynb` | Schema harmonization, leakage-safe preprocessing, normalization, lag/rolling/ramp feature generation, target construction, predictor–target analysis |
| 03 | `03_classical_ml_baselines.ipynb` | Training and evaluation of classical ML, linear, tree-based, boosting, and probabilistic baseline models |
| 04 | `04_deep_learning_and_compact_sota_models.ipynb` | Training and evaluation of neural, deep learning, temporal, and compact SOTA-style forecasting models |
| 05 | `05_proposed_pi_bhtf_model.ipynb` | Development and evaluation of the proposed PI-BHTF architecture with Bayesian uncertainty and conformal interval correction |
| 06 | `06_comparative_results_ablation_statistics.ipynb` | Comparative results, ablation analysis, external validation, statistical testing, robustness analysis, and final visualizations |

---

## Models Evaluated

The experiments compare the proposed model against a wide set of baselines.

### Classical Machine Learning and Statistical Models

- Persistence baseline
- Seasonal persistence baseline
- Rolling mean baseline
- Ridge regression
- ElasticNet
- Bayesian Ridge
- Gaussian Process reduced variant
- Random Forest
- Extra Trees

### Boosting Models

- HistGradientBoosting
- XGBoost
- LightGBM
- CatBoost

### Probabilistic and Quantile Models

- Quantile Gradient Boosting Median
- Linear Quantile Regression Median
- Probabilistic interval-based baselines

### Deep Learning and Compact SOTA-Style Models

- MLPBaseline
- ResidualMLP
- LSTM
- GRU
- TCN
- Transformer Encoder
- PatchTST-style compact model
- TFT-inspired attention model
- N-BEATS-style compact model
- FT-Transformer-style model
- TabTransformer-style model
- SAINT-style compact model

The compact SOTA-style implementations are used as architecture-inspired comparators under a controlled computational setup, not as full industrial-scale implementations.

---

## Proposed Model: PI-BHTF

The proposed model is named:

## Physics-Informed Bayesian Hybrid Temporal Forecaster

**PI-BHTF** is a hybrid architecture designed for calibrated multi-horizon renewable energy forecasting.

### Core architectural components

| Component | Purpose |
|---|---|
| Solar branch | Encodes irradiance-driven and solar-generation-related predictors |
| Wind branch | Encodes wind speed, air density, and wind power-curve-related predictors |
| Temporal-calendar branch | Encodes hour, seasonal, monthly, and annual cyclic patterns |
| Cross-context branch | Encodes meteorology, lag features, rolling summaries, and ramp features |
| Cross-energy gate | Models interaction between solar and wind encoded states |
| Hybrid fusion MLP | Combines branch representations into a shared latent forecasting state |
| Bayesian predictive heads | Produce mean forecasts and heteroscedastic uncertainty estimates |
| MC dropout | Approximates predictive uncertainty through stochastic forward passes |
| Conformal correction | Calibrates 90% prediction intervals using validation residuals |
| Hybrid stacking | Combines PI-BHTF with strong validation-selected base predictors |

### Model outputs

PI-BHTF produces:

- Point forecasts;
- 90% lower prediction interval bounds;
- 90% upper prediction interval bounds;
- Forecasts for all 15 target–horizon tasks.

---

## Evaluation Metrics

The evaluation uses point forecasting, probabilistic, interval, and calibration-oriented metrics.

| Metric | Role |
|---|---|
| MAE | Mean absolute error |
| RMSE | Root mean squared error |
| nRMSE | Normalized RMSE |
| MASE | Scale-independent forecasting error |
| R² | Explained variance / goodness of fit |
| PICP90 | Empirical coverage of 90% prediction intervals |
| MeanIntervalWidth | Average width of prediction intervals |
| Winkler90 | Interval score for 90% prediction intervals |
| Paired statistical tests | Significance of paired differences |
| Bootstrap confidence intervals | Uncertainty of paired improvement estimates |

---

## Main Experimental Findings

### Internal Test Results

On the internal test split, PI-BHTF and its ablation variants achieved the strongest overall mean MAE across target–horizon tasks.

| Model | Internal mean MAE |
|---|---:|
| PI-BHTF_ClassicalStack | 0.080 |
| PI-BHTF_NeuralAugmented | 0.080 |
| PI-BHTF | 0.080 |
| LightGBM | 0.082 |
| HistGradientBoosting | 0.083 |
| XGBoost | 0.084 |
| QuantileGradientBoostingMedian | 0.086 |
| ResidualMLP | 0.088 |

The strongest internal performance was observed for PI-BHTF variants, while LightGBM remained the closest classical boosting competitor.

### Locked External Validation Results

On the locked external SCADA validation dataset, PI-BHTF remained within the top-performing group.

| Model | External mean MAE |
|---|---:|
| PI-BHTF_NeuralAugmented | 0.069 |
| PI-BHTF_ClassicalStack | 0.069 |
| SAINTStyleCompact | 0.069 |
| PI-BHTF | 0.070 |
| GRU | 0.071 |
| CatBoost | 0.072 |
| NBEATSStyleCompact | 0.074 |
| QuantileGradientBoostingMedian | 0.075 |
| XGBoost | 0.077 |
| LightGBM | 0.080 |

PI-BHTF did not dominate every external target–horizon task, but it provided one of the strongest overall balances between accuracy, robustness, and calibrated uncertainty.

---

## Target–Horizon Results

The results show that model behavior differs substantially by target and horizon.

### Key observations

- Short horizons such as **H1** often benefit from lag and persistence effects.
- Medium horizons such as **H6** are more sensitive to meteorological regime changes.
- Long horizons such as **H24** benefit from daily cyclicity and physics-informed temporal structure.
- Wind generation remains the most difficult target on medium and long horizons.
- Total generation is more stable than individual solar or wind targets due to aggregation effects.

### PI-BHTF behavior

PI-BHTF was especially competitive for:

- Total generation forecasting;
- H24 external validation;
- Internal test MAE ranking;
- Narrower prediction intervals compared with several boosting and probabilistic baselines;
- Balanced point accuracy and interval coverage.

---

## Statistical Testing

Statistical testing was performed using:

- Paired t-test;
- Wilcoxon signed-rank test;
- Diebold–Mariano test with Newey–West correction;
- Holm correction for multiple comparisons;
- Paired bootstrap confidence intervals.

### Internal test comparison

Against LightGBM on internal test:

| Statistic | Value |
|---|---:|
| Mean MAE improvement | 0.001769 |
| Paired timestamps | 1,310 |
| Paired t-test p-value | 3.30e−07 |
| Holm-adjusted p-value | 9.91e−07 |
| Diebold–Mariano p-value | 0.0497 |
| Bootstrap CI | [−0.000046, 0.003578] |

The internal improvement was statistically detectable, although the bootstrap interval indicates that the practical effect size is moderate.

### External validation comparison

Against SAINTStyleCompact on locked external validation:

| Statistic | Value |
|---|---:|
| Mean MAE improvement | −0.000259 |
| Paired timestamps | 17,520 |
| Paired t-test p-value | 0.0808 |
| Holm-adjusted p-value | 0.1906 |
| Diebold–Mariano p-value | 0.5374 |
| Bootstrap CI | [−0.001073, 0.000584] |

The external difference between PI-BHTF and SAINTStyleCompact was not statistically stable at the aggregate level, which supports the need for target–horizon analysis rather than relying only on averaged MAE.

---

## Uncertainty Quantification and Interval Coverage

PI-BHTF produces 90% prediction intervals through Bayesian uncertainty estimation and conformal residual correction.

### External PICP90 results for PI-BHTF

| Target | H1 | H3 | H6 | H12 | H24 |
|---|---:|---:|---:|---:|---:|
| Solar | 0.77 | 0.94 | 0.89 | 0.97 | 0.97 |
| Wind | 0.65 | 0.95 | 0.97 | 0.99 | 0.99 |
| Total | 0.93 | 0.97 | 0.98 | 1.00 | 1.00 |

The model achieved high interval coverage for total generation and for medium-to-long horizons. However, short-term Solar H1 and Wind H1 intervals showed undercoverage, indicating that future calibration refinements are needed for short-horizon uncertainty under rapidly changing conditions.

---

## Main Contributions

This repository demonstrates the following contributions:

- A leakage-safe experimental pipeline for renewable generation forecasting;
- Multi-horizon target construction for solar, wind, and total generation;
- Physics-informed feature engineering based on irradiance, wind speed, and air density;
- A proposed hybrid Bayesian forecasting architecture, PI-BHTF;
- Comparison against classical ML, boosting, probabilistic, deep learning, and compact SOTA-style models;
- Locked external validation on independent Chinese State Grid SCADA data;
- Calibration and interval analysis using PICP90, interval width, and Winkler score;
- Statistical testing with paired comparisons, Holm correction, and bootstrap confidence intervals;
- Target–horizon analysis that avoids hiding model weaknesses behind averaged metrics.

---

## Limitations

The current study has several limitations:

- The number of open datasets is limited.
- The main and external datasets differ in feature availability and measurement structure.
- External SCADA validation involves domain shift in capacity, weather, and site composition.
- Compact SOTA-style models are architecture-inspired and not full-scale industrial implementations.
- Short-horizon interval calibration for Solar H1 and Wind H1 requires further refinement.
- Additional multi-site and multi-year validation is needed before operational deployment.

---

## Suggested Future Work

Future extensions may include:

- Evaluation on additional multi-year, multi-site renewable generation datasets;
- Integration of numerical weather prediction inputs;
- More adaptive conformal prediction for short-horizon wind and solar uncertainty;
- Separate calibration strategies for solar, wind, and total generation;
- Extension to probabilistic dispatch and grid-balancing applications;
- Comparison with full-scale transformer forecasting architectures under larger computational budgets.

---

## Repository File Structure

```text
pi-bhtf-renewable-forecasting/
│
├── 01_data_ingestion_raw_eda.ipynb
├── 02_preprocessing_feature_engineering_analysis.ipynb
├── 03_classical_ml_baselines.ipynb
├── 04_deep_learning_and_compact_sota_models.ipynb
├── 05_proposed_pi_bhtf_model.ipynb
├── 06_comparative_results_ablation_statistics.ipynb
└── README.md
