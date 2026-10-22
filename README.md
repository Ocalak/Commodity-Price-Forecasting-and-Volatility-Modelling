# Energy Commodity Price Modelling: ARMA–GARCH, Probabilistic Forecast Evaluation and Copulas

Time-series econometrics in R for natural gas, oil and coal. The project fits ARMA–GARCH models, produces rolling one-step-ahead probabilistic forecasts, benchmarks them against an AR(1) model with the Continuous Ranked Probability Score (CRPS), backtests 5% quantile forecasts, and models cross-commodity dependence with copulas evaluated by the energy score.

![R](https://img.shields.io/badge/language-R-276DC3)
![Topic](https://img.shields.io/badge/topic-volatility%20modelling%20%26%20probabilistic%20forecasting-blue)

---

## Research question

> How well do ARMA–GARCH models capture and forecast energy commodity price dynamics, and how does their probabilistic forecast performance compare with a simple AR(1) benchmark?

Energy prices feed into production costs, household spending and inflation, and their volatility creates uncertainty for firms and policymakers. Good in-sample fit does not guarantee good out-of-sample forecasts, so the project pairs model estimation with out-of-sample forecast evaluation.

## Pipeline

| Step | Script | What it does |
|------|--------|--------------|
| 1 | `1_fit_armagarch_cond_var.R` | Selects the ARMA order with `auto.arima()` (max p = 2, q = 2, stationary, AIC). Grid-searches GARCH(ARCH 1–2, GARCH 1–2) by AIC. Fits the final sGARCH model with normal innovations on the first 2,500 observations and extracts coefficients and conditional variances. |
| 2 | `2_forecast_armagarch.R` | Rolling one-step-ahead forecasts with `ugarchroll()` (recursive window, refit at every step, start at observation 2,500, 200 forecasts). Stores forecast mean and sigma, computes 5% VaR, and draws ridge-density plots of the predictive distributions. |
| 3 | `3_CRPS_AR_ARMAGARCH.R` | Refits an AR(1) benchmark each step, then computes the closed-form Gaussian CRPS for both models over the 200 forecast days and plots the results. |
| 4 | `4_quantile.R` | Extracts the 5% forecast quantiles and computes the share of realized values falling below them (a coverage backtest). |
| 5 | `5_pit_trs.R` | Standardizes the ARMA–GARCH residuals and applies the probability integral transform (`pobs`) to get pseudo-observations. |
| 6 | `6_fit_copulas.R` | Fits three-dimensional copulas by maximum likelihood: Student-t, Gaussian, and an independence-type copula (`gumbelCopula` with the independence option). |
| 7 | `7_energy_score.R` | Simulates joint forecast distributions from each copula (1,000 draws per day), pushes them through the normal marginals, and scores them with the energy score over the 200 forecast days. |

## Methods in brief

- **Mean equation:** ARMA(p, q) with p, q ≤ 2, chosen by AIC.
- **Variance equation:** standard GARCH with orders chosen by AIC over a 2×2 grid; Gaussian innovations.
- **Forecasting:** recursive-window, one-step-ahead, with refitting at every step. Forecast distributions are Normal(μ̂, σ̂).
- **CRPS:** for a Normal forecast N(μ, σ²) and observation x, the closed form is used. Lower CRPS is better.
- **Backtesting:** a 5% quantile is extracted from each forecast distribution and compared with realized values.
- **Dependence:** PIT-transformed standardized residuals are used to fit copulas. The joint forecast is scored with the energy score (`es_sample`).

## Data

- **Series:** natural gas, oil, coal
- **Frequency and period:** daily, 2010-03-16 to 2020-10-27
- **Split:** first 2,500 observations for the initial estimation window, then 200 one-step-ahead forecasts (2,501 to 2,700)
- **Expected input:** a data frame named `commodities` with columns `date`, `NGas`, `oil`, `coal`

The raw data file is not included in this repository. Add the source and a loading script (see [Reproducibility](#reproducibility)).

## Results

Mean CRPS over the 200 out-of-sample days (lower is better):

| Commodity | AR(1) | ARMA–GARCH |
|-----------|------:|-----------:|
| Natural gas | 0.1943 | 0.1948 |
| Oil | 0.745 | 0.739 |
| Coal | 0.4620 | 0.4668 |

**Reading the table:** the two approaches are very close. ARMA–GARCH is marginally better for oil, and AR(1) is marginally better for natural gas and coal. The differences are small, so the extra complexity of the GARCH variance model buys little on this window. A formal test (for example Diebold–Mariano on the CRPS series) would be needed before claiming either model is better.

Full discussion and figures are in [`report.pdf`](report.pdf).

## Repository structure

```
.
├── 1_fit_armagarch_cond_var.R
├── 2_forecast_armagarch.R
├── 3_CRPS_AR_ARMAGARCH.R
├── 4_quantile.R
├── 5_pit_trs.R
├── 6_fit_copulas.R
├── 7_energy_score.R
├── report.pdf
└── README.md
```

## Reproducibility

Run the scripts in numeric order in a single R session. Later scripts reuse objects created earlier (`commodities`, `order_*`, `fit_*`, `mod_*`, `pit_trs`, `fit_*_cop`), so they will not run standalone.

Packages used across the scripts:

```r
install.packages(c(
  "forecast",       # auto.arima
  "rugarch",        # ugarchspec, ugarchfit, ugarchroll
  "tidyverse",      # tibble, tidyr (expand_grid), ggplot2
  "magrittr",       # %<>%
  "lubridate",      # date handling
  "ggridges",       # ridge density plots
  "copula",         # fitCopula, pobs, rCopula
  "scoringRules"    # es_sample (energy score)
))
```

Before the first script, load the data into `commodities` and attach the libraries above.

## Limitations

- The GARCH specification assumes normally distributed innovations. Student-t or skewed distributions are a natural extension for commodity returns.
- AIC-based order selection does not guarantee the best out-of-sample forecast.
- The evaluation window is only 200 days from a single period (2020), which includes unusually volatile markets.
- Differences in CRPS between the two models are small and untested for significance.
- Copula marginals are taken as normal, and the copulas are fitted to in-sample standardized residuals.
- Statistical forecast performance does not establish causal effects on inflation, production or other outcomes.

## Possible extensions

- Heavier-tailed innovations and asymmetric GARCH variants (EGARCH, GJR)
- Formal forecast comparison tests and quantile coverage tests (Kupiec, Christoffersen)
- A single `run_all.R` with a data-loading step and a pinned package list (for example with `renv`)

## Author

**Öcal Kaptan**
[GitHub](https://github.com/Ocalak) · [LinkedIn](https://linkedin.com/in/ocalkaptan123) · [Portfolio](https://portfolio-ocal.com)

## Disclaimer

For academic and educational purposes only. Nothing here is financial advice.
