# Predicting Short-Duration Rainfall Events under Rising Temperatures

**A machine learning approach using meteorological data and climate change scenarios in Zaria, Nigeria (11.11°N, 7.72°E)**

🌐 **Live site:** `https://thekal33d.github.io/Predicting-Short-Duration-Rainfall-in-Zaria/`

A static web application that predicts daily rainfall occurrence and typical wet-day intensity for Zaria using trained machine learning models, and presents CMIP6-based projections (SSP2-4.5 and SSP5-8.5) for 2021–2080. It runs entirely in the browser: no server, database or API keys.

---

## Features

| Tab | What it does |
|---|---|
| **Daily forecast** | Fetches live daily weather for Zaria (7 days back, 16 days ahead) and predicts rain probability, wet/dry outcome, typical amount and heavy-rain flag for each day |
| **Manual prediction** | Enter any day's temperature, humidity, pressure, wind and get the same outputs |
| **Climate projections** | Annual rainfall by horizon (near-term, mid-century, late-century) for SSP2-4.5 and SSP5-8.5 |
| **Predictor importance** | Relative importance of each meteorological variable for occurrence and intensity |
| **Method & limits** | Model description and limitations |

## Repository structure

```
├── index.html     # The website (HTML, CSS and JavaScript in one file)
├── model.json     # Exported trained models, scaler, calibration and study results
└── README.md      # This file
```

Both `index.html` and `model.json` must be in the **same folder**.

## Deploying on GitHub Pages (no command line needed)

1. Sign in to GitHub and click **New repository**. Choose a name (for example `zaria-rainfall`) and set it to **Public**.
2. On the repository page, click **Add file → Upload files**.
3. Drag in `index.html`, `model.json` and `README.md`, then click **Commit changes**.
4. Open **Settings → Pages**.
5. Under **Build and deployment**, set **Source** to *Deploy from a branch*, choose branch **main** and folder **/ (root)**, then click **Save**.
6. Wait 1–2 minutes. The live address appears at the top of the Pages screen.

> **Important:** open the site through the GitHub Pages link. Double-clicking `index.html` on your computer will not work, because browsers block local files from loading `model.json`.

### Updating the site later
Open the repository, click the file, choose the pencil/upload option (or **Add file → Upload files** and upload a file with the same name), and commit. The site refreshes within a minute or two.

### Troubleshooting

| Problem | Fix |
|---|---|
| Page says "model.json not found" | Make sure `model.json` is in the same folder as `index.html` and is spelled exactly the same |
| Page shows a 404 | Pages is not enabled yet, or you need to wait a couple of minutes after enabling it |
| Daily forecast table is empty | The weather service could not be reached; check your connection and refresh. The Manual tab still works |
| Old version still showing | Hard-refresh (Ctrl+F5 / Cmd+Shift+R) |

## How it works

**Two-stage model**
1. **Occurrence:** a Random Forest classifier estimates the probability that a day is wet (≥ 1 mm, the WMO wet-day threshold).
2. **Intensity:** for wet days, an Extra Trees regressor trained on log-transformed rainfall predicts the amount, followed by empirical quantile-mapping calibration.

**Predictors:** temperature, relative humidity, surface pressure, specific humidity, wind speed, wind direction, plus engineered *moisture flux* (humidity × wind speed) and *thermal instability* (temperature × relative humidity). Inputs are standardised with the training scaler.

**Climate scenarios:** projections come from the study's CMIP6-ML pipeline, which applies regional warming rates (SSP2-4.5: 0.035 °C/yr; SSP5-8.5: 0.065 °C/yr) with Clausius–Clapeyron moisture scaling (+7% per °C) and feeds the result through the trained models.

**Live inputs:** daily means from the [Open-Meteo](https://open-meteo.com/) forecast API. Specific humidity is derived from dew point and pressure, and 10 m wind speed is scaled to 2 m (×0.72) to match the training variable.

## Model performance (held-out test period)

| Component | Model | Key results |
|---|---|---|
| Rainfall occurrence | Random Forest | Accuracy 0.863 · Precision 0.762 · Recall 0.958 · F1 0.849 · ROC-AUC 0.940 |
| Rainfall intensity | Extra Trees (calibrated) | MAE 12.5 mm · RMSE 28.8 mm · R² 0.009 |

Occurrence is predicted well. Daily rainfall *amount* has little skill from these predictors alone, so the site presents it as a typical wet-day value with low confidence rather than a precise forecast.

## Limitations

- Tree-based models cannot extrapolate beyond conditions seen in training, so predicted intensities are compressed and extreme events are under-represented.
- Projected anomalies are measured against the observed baseline and therefore include model bias. The change from near-term to late-century is the cleaner indicator of the scenario-driven signal.
- Scenario warming rates are assumed regional values rather than direct CMIP6 model output.
- Live inputs come from a different source than the training data, which adds uncertainty.
- Outputs are statistical estimates, **not official weather forecasts**.

## Data sources

- Training data: daily meteorological records for Zaria (NASA POWER-style variables).
- Live weather inputs: Open-Meteo API.
- Climate scenarios: SSP2-4.5 and SSP5-8.5 (IPCC AR6 / CMIP6 framework).

## Technology

Plain HTML, CSS and JavaScript. No frameworks, build step or backend. Random Forest and Extra Trees models are exported to JSON and evaluated directly in the browser.

## Author

**<Your name>** · <Department, University> · <Year>
Supervisor: <Supervisor name>

## Citation

> <Your name> (<year>). *Predicting Short-Duration Rainfall Events under Rising Temperatures: A Machine Learning Approach Using Meteorological Data and Climate Change Scenarios in Zaria.* <Institution>.

## License

Add a license of your choice (for example MIT) or remove this section.
