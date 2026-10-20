# Beijing PM2.5 Time-Series Forecasting

This project explores one-hour-ahead PM2.5 forecasting with recurrent neural networks. It uses hourly Beijing air-pollution and weather observations from 2010–2014 to compare a persistence baseline, a SimpleRNN, LSTM, and bidirectional LSTM models in both univariate and multivariate settings.

The notebook is written as a guided analysis: it explains each modeling choice, visualizes the data, avoids time-series leakage, trains the models with PyTorch, and evaluates them on a chronologically later test period.

## Project structure

```text
.
├── data/
│   └── LSTM-Multivariate_pollution.csv
├── notebooks/
│   └── air_pollution.ipynb
├── presentation/
│   └── Beijing_PM25_Forecasting_Presentation.pptx
├── .gitignore
├── README.md
└── requirements.txt
```

## What the notebook covers

- Exploratory analysis of pollution levels, seasonality, autocorrelation, and weather correlations
- Chronological training/test split to prevent future information from leaking into training
- Training-only min-max scaling and 24-hour sliding input windows
- Univariate and multivariate sequence forecasting
- Comparison of naive persistence, SimpleRNN, LSTM, and BiLSTM models
- Error analysis for ordinary periods and high-pollution spikes

## Dataset

The CSV contains 43,800 hourly observations with these fields:

| Field | Description |
|---|---|
| `date` | Observation timestamp |
| `pollution` | PM2.5 concentration in µg/m³ |
| `dew` | Dew point |
| `temp` | Temperature |
| `press` | Atmospheric pressure |
| `wnd_dir` | Wind direction |
| `wnd_spd` | Cumulative wind speed |
| `snow` | Cumulative snow hours |
| `rain` | Cumulative rain hours |

The notebook supports the included local CSV and the Kaggle path for the **LSTM Datasets (Multivariate & Univariate)** dataset. If you redistribute the data, check the source dataset's current license and attribution requirements.

## Setup and usage

Python 3.11 is recommended.

```bash
python -m venv .venv
```

Activate the environment:

```bash
# Windows PowerShell
.venv\Scripts\Activate.ps1

# macOS/Linux
source .venv/bin/activate
```

Install the dependencies and start Jupyter:

```bash
python -m pip install -r requirements.txt
jupyter lab
```

Open `notebooks/air_pollution.ipynb` and run the cells from top to bottom. The notebook automatically checks for the dataset in `data/` and `../data/`, so it works whether Jupyter starts from the repository root or from the `notebooks` directory.

Model training runs on CPU or CUDA when available. Runtime and final scores can vary with hardware and random initialization.

## Saved results

The committed notebook produced the following test-set results:

| Model | MAE (µg/m³) | RMSE (µg/m³) |
|---|---:|---:|
| Naive persistence | 12.43 | 24.53 |
| SimpleRNN, univariate | 14.26 | 25.34 |
| LSTM, univariate | 12.22 | 23.44 |
| BiLSTM, univariate | 12.60 | 23.62 |
| LSTM, multivariate | 12.38 | **23.20** |
| BiLSTM, multivariate | **12.09** | 23.54 |

The multivariate BiLSTM achieved the lowest MAE, while the multivariate LSTM achieved the lowest RMSE. The improvements over the naive forecast were modest, which reinforces the value of comparing neural networks with a strong baseline.

The error analysis also found that the univariate LSTM's MAE rose from 12.22 overall to 24.16 during the highest 10% of pollution observations. Sudden pollution spikes remain the main forecasting challenge.

## Methodological limitations

- The experiment uses a training/test split without a separate validation period.
- Hyperparameters are educational choices rather than results of systematic tuning.
- A stronger study should use chronological train/validation/test splits or walk-forward validation.
- Results from stochastic neural-network training may differ slightly between runs.

The accompanying PowerPoint in `presentation/` summarizes the project and its conclusions.
