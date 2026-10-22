# Energy Commodity Price Modelling: ARMA–GARCH, Forecast Evaluation and Copulas

## Overview

This project applies time-series econometrics in R to the modelling and probabilistic forecasting of energy commodity prices, focusing on natural gas, oil and coal.

The analysis explores autoregressive moving-average models with generalized autoregressive conditional heteroskedasticity (ARMA–GARCH), compares predictive distributions with an AR(1) benchmark, and investigates dependence modelling using copulas.

**Research question:** How effectively can ARMA–GARCH models capture and forecast energy commodity price dynamics, and how does their probabilistic forecast performance compare with a simpler AR(1) benchmark?

## Economic motivation

Energy commodity prices influence production costs, household expenditure and inflation. Their volatility creates uncertainty for firms, policymakers and market participants.

Modelling time-varying volatility and evaluating predictive distributions can help assess how well statistical models represent this uncertainty. However, good in-sample fit does not necessarily imply good out-of-sample forecasting performance.

This project therefore combines econometric model estimation with forecast evaluation.

## Research objectives

1. Estimate ARMA–GARCH specifications for natural gas, oil and coal series.
2. Select candidate model orders using the Akaike Information Criterion (AIC).
3. Generate one-step-ahead probabilistic forecasts.
4. Compare ARMA–GARCH forecasts against an AR(1) benchmark using the Continuous Ranked Probability Score (CRPS).
5. Explore probability integral transforms and copula-based dependence modelling.
6. Evaluate the strengths and limitations of the modelling approach.

## Methods

### 1. ARMA–GARCH estimation

The mean equation is modelled using an ARMA specification, while a GARCH process captures time-varying conditional variance.

The current estimation code selects ARMA orders using `auto.arima()` and compares candidate ARCH/GARCH orders using AIC. The implemented variance specification uses a standard GARCH model with normally distributed innovations.

### 2. Forecasting

The forecasting script uses rolling one-step-ahead forecasts with a recursive estimation window. The code begins the forecast evaluation after an initial estimation sample of 2,500 observations.

Forecast distributions are represented by their estimated means and standard deviations.

### 3. Benchmark comparison

An AR(1) model provides a simpler benchmark. The project calculates CRPS to compare probabilistic forecasts.

CRPS evaluates both the calibration and concentration of a predictive distribution; lower scores indicate better performance when the same scoring convention and evaluation observations are used.

### 4. Dependence modelling

Additional scripts investigate probability integral transforms and fit alternative copula specifications, including Gaussian, Student-t and Gumbel copulas.

These components are intended to explore dependence between the commodity series. Their final interpretation should be based on verified diagnostics and comparative results.

## Data

- **Series:** Natural gas, oil and coal.
- **Observation frequency:** [Specify frequency.]
- **Sample period:** [Add start and end dates.]
- **Price units:** [Specify units and currencies, where applicable.]
- **Data source:** [Add the original provider and dataset link.]
- **Transformations:** [Document returns, differences, scaling, missing-value treatment and any other preprocessing actually applied.]

The analysis uses an initial estimation sample of 2,500 observations and evaluates forecasts over a subsequent 200-observation period in the current scripts. Confirm that the available data and final model inputs match these settings.

## Repository structure

| File | Purpose |
|---|---|
| `1_fit_armagarch_cond_var.R` | Select candidate orders, estimate ARMA–GARCH models and extract conditional variances. |
| `2_forecast_armagarch.R` | Generate rolling one-step-ahead forecasts and forecast distributions. |
| `3_CRPS_AR_ARMAGARCH.R` | Compare ARMA–GARCH and AR(1) forecasts using CRPS. |
| `4_quantile.R` | Quantile-related analysis; document the exact procedure after reviewing the script. |
| `5_pit_trs.R` | Probability integral transform analysis; document the exact procedure after reviewing the script. |
| `6_fit_copulas.R` | Fit candidate copula models to transformed series. |
| `7_energy_score.R` | Energy-score analysis; document the inputs and evaluation procedure after reviewing the script. |
| `report.pdf` | Existing project report. |

## Software and reproducibility

The analysis is written in R. The scripts rely on packages for time-series modelling, GARCH estimation, data manipulation, plotting and copula estimation.

Before running the analysis:

1. Install R and the required packages.
2. Obtain the original dataset and document its source.
3. Load and prepare the commodity data using the expected column names.
4. Run the scripts in dependency order, checking that objects created in earlier scripts exist before proceeding.
5. Save the resulting model summaries, forecast evaluations, tables and figures.

**Important:** The repository currently uses shared objects such as `commodities`, fitted models and transformed series across scripts. A fresh R session may not run the entire workflow without additional setup. Add a dedicated data-loading script and explicit package-loading instructions before describing the workflow as fully reproducible.

## Results

| Commodity | AR(1) mean CRPS | ARMA–GARCH mean CRPS |
|---|---:|---:|
| Natural gas | 0.1943 | 0.1948 
| Oil | 0.745 | 0.739 |
| Coal | 0.4620 | 0.4668 |


## Limitations

- Forecast performance depends on the sample period, data quality and model assumptions.
- The current GARCH specification assumes normally distributed innovations; alternative distributions may be worth investigating.
- Model selection by AIC does not guarantee the best out-of-sample forecast.
- Forecast comparisons should use consistent observations, horizons and scoring conventions.
- Dependence estimates and copula conclusions require appropriate diagnostic checks.
- Statistical associations and forecast performance do not establish causal effects on inflation, production or other economic outcomes.

## Conclusion

This project demonstrates an applied econometric workflow combining time-series model selection, conditional volatility estimation, probabilistic forecasting and forecast evaluation in R. Its main empirical conclusions will be drawn from validated out-of-sample results rather than assumed in advance.

## Author

[Your name or GitHub profile]

[Optional: link to CV, portfolio or contact page]
