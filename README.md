<div align="center">
    <h1>Run and scale time-series machine learning in the Cloud</h1>
<br />

![functime](https://github.com/indexhub-ai/functime/raw/main/static/images/functime_banner.png)

[![Python](https://img.shields.io/pypi/pyversions/functime-client)](https://pypi.org/project/functime-client/)
[![PyPi](https://img.shields.io/pypi/v/functime-client?color=blue)](https://pypi.org/project/functime-client/)
[![Code style: black](https://img.shields.io/badge/code%20style-black-000000.svg)](https://github.com/psf/black)
[![GitHub Publish to PyPI](https://github.com/indexhub-ai/functime/actions/workflows/publish.yml/badge.svg)](https://github.com/indexhub-ai/functime/actions/workflows/publish.yml)
[![GitHub Build Docs](https://github.com/indexhub-ai/functime/actions/workflows/docs.yml/badge.svg)](https://github.com/indexhub-ai/functime/actions/workflows/docs.yml)

</div>

---
**functime** is a powerful and easy-to-use [Cloud service](https://functime.ai) for AutoML forecasting and time-series embeddings.
This `functime-client` Python library provides a scikit-learn interface and command-line tool to interact with `functime` Cloud.

Want to use `functime` for seamless time-series analytics across your data team?
Looking for fully-managed production-grade AI/ML forecasting and time-series search that scales?
Book a [15 minute discovery call](https://calendly.com/functime-indexhub) to learn more about `functime`'s Team / Enterprise plans.

## Highlights
- **Fast:** Forecast 100,000 time series in seconds *on your laptop*
- **Efficient:** Embarrassingly parallel feature engineering for time-series using [`Polars`](https://www.pola.rs/)
- **Battle-tested:** Automated machine learning algorithms that deliver real business impact and win competitions
- Every forecaster supports **exogenous features**
- **Backtesting** with expanding window and sliding window splitters
- Automated lags and **hyperparameter tuning** using [`FLAML`](https://github.com/microsoft/FLAML)
- Utilities to add calendar effects, special events (e.g. holidays), weather patterns, and economic trends
- Supports recursive, direct, and ensemble forecast strategies

View detailed [list of features](https://docs.functime.ai/features/) including forecasters, preprocessors, feature extractors, and time-series splitters.

## Getting Started
1. First, install `functime` via the [pip](https://pypi.org/project/functime-client) package manager.
```bash
pip install "functime-client"
```
2. Then sign-up for a free `functime` Cloud account via the command-line interface (CLI).
```bash
functime login
```
3. That's it! You can begin forecasting at scale using the `scikit-learn` fit-predict interface.
```python
import polars as pl
from functime.cross_validation import train_test_split
from functime.forecasting import LightGBM
from functime.metrics import mase

# Load example data
y = pl.read_parquet("https://bit.ly/commodities-data")
entity_col, time_col = y.columns[:2]

# Time series split
y_train, y_test = y.pipe(train_test_split(test_size=3))

# Fit-predict
model = LightGBM(freq="1mo", lags=24, max_horizons=3, strategy="ensemble")
model.fit(y=y_train)
y_pred = model.predict(fh=3)

# Score forecasts in parallel
scores = mase(y_true=y_test, y_pred=y_pred, y_train=y_train)
```
All predictions and scores are returned as `Polars` DataFrames.
```
>>> y_pred
shape: (213, 3)
┌────────────────┬─────────────────────┬─────────────┐
│ commodity_type ┆ time                ┆ price       │
│ ---            ┆ ---                 ┆ ---         │
│ str            ┆ datetime[ns]        ┆ f64         │
╞════════════════╪═════════════════════╪═════════════╡
│ Wheat, US HRW  ┆ 2023-01-01 00:00:00 ┆ 240.337497  │
│ Wheat, US HRW  ┆ 2023-02-01 00:00:00 ┆ 250.851552  │
│ Wheat, US HRW  ┆ 2023-03-01 00:00:00 ┆ 252.102028  │
│ Beef           ┆ 2023-01-01 00:00:00 ┆ 4.271976    │
│ …              ┆ …                   ┆ …           │
│ Coconut oil    ┆ 2023-03-01 00:00:00 ┆ 1140.930346 │
│ Copper         ┆ 2023-01-01 00:00:00 ┆ 7329.806663 │
│ Copper         ┆ 2023-02-01 00:00:00 ┆ 7484.565165 │
│ Copper         ┆ 2023-03-01 00:00:00 ┆ 7486.160195 │
└────────────────┴─────────────────────┴─────────────┘

>>> scores.sort("mase")
shape: (71, 2)
┌──────────────────────┬────────────┐
│ commodity_type       ┆ mase       │
│ ---                  ┆ ---        │
│ str                  ┆ f64        │
╞══════════════════════╪════════════╡
│ Rice, Viet Namese 5% ┆ 0.308148   │
│ Palm kernel oil      ┆ 0.554886   │
│ Coconut oil          ┆ 1.051424   │
│ Cocoa                ┆ 1.32211    │
│ …                    ┆ …          │
│ Sugar, US            ┆ 73.346233  │
│ Sugar, world         ┆ 81.304941  │
│ Phosphate rock       ┆ 85.936644  │
│ Sugar, EU            ┆ 170.319435 │
└──────────────────────┴────────────┘
```

## Deployment
`functime` deploys and trains your forecasting models the moment you call any `.fit` method.
Run the `functime list` CLI command to list all deployed models.
To view data and forecasts usage, run the `functime usage` CLI command.
![Example CLI usage](static/gifs/functime_cli_usage.gif)

You can reuse a deployed model for predictions anywhere using the `stub_id` variable.
```python
forecaster = LinearModel.from_deployment(stub_id)
y_pred = forecaster.predict(fh=3)
```

## License
`functime` is distributed under [AGPL-3.0-only](LICENSE). For Apache-2.0 exceptions, see [LICENSING.md](https://github.com/indexhub-ai/functime/blob/HEAD/LICENSING.md).
