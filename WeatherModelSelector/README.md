# 🌦️ WeatherModelSelector

### Machine Learning-Based Weather Forecast Model Selection and Verification

**WeatherModelSelector** is a Python and Streamlit application that compares forecasts from four numerical weather prediction models—**ECMWF IFS, GFS, ICON, and GEM**—and uses **Random Forest regression** to select the model expected to have the lowest error for a given location, weather parameter, and forecast-valid hour.

> **Important:** This system selects among existing forecasts; it does not generate weather forecasts from scratch. ML-estimated errors are not observed errors. The historical evaluation below uses **ERA5 reanalysis as a reference**, not independent station ground truth.

## 👥 Project Team

| Name | Roll Number |
|---|---|
| Utkarsha Ghanvat | 24141014 |
| Shivani Kolekar | 24141031 |
| Prajwal Gore | 24141016 |

## 🎯 Problem Statement

Weather forecasting models can disagree, and the model with the lowest error can depend on location, weather variable, and forecast hour. Using the same model for every situation may be suboptimal. This project aims to select a suitable model dynamically based on patterns learned from archived forecasts and historical reference data.

## ✅ Objectives

- Collect and compare forecasts from ECMWF, GFS, ICON, and GEM.
- Evaluate five weather parameters: **temperature, relative humidity, precipitation, wind speed, and mean sea-level pressure**.
- Use historical forecast errors to train an ML-based model selector.
- Choose forecasts based on predicted error, with a validation-tuned threshold for switching away from ECMWF.
- Visualize model comparisons, maps, estimated errors, and historical performance in an interactive dashboard.
- Begin independent forecast verification using IMD and airport METAR observations.

## 📊 Dataset

| Item | Description |
|---|---|
| Historical period | 1 October 2025 – 30 September 2026 |
| Geographical coverage | 31 Indian locations |
| Weather models | ECMWF IFS, GFS, ICON, GEM |
| Variables | Temperature, humidity, precipitation, wind speed, MSL pressure |
| Temporal resolution | Hourly |
| Archived forecast rows | **1,086,240** (31 locations × 8,760 hours × 4 models) |
| ERA5 reference rows | **271,560** (31 locations × 8,760 hours) |
| Historical forecast source | Open-Meteo **Previous Runs API**, using `previous_day1` forecasts |
| Historical reference | ERA5 reanalysis accessed for the project through the reference-data pipeline |

**Primary files** (in the `data/` folder):

- `historical_multivariable_forecast_12months_previous_day1.csv` — historical forecasts from the four models.
- `era5_multivariable_reference_12months.csv` — hourly reference values for five variables.
- `observations/metar_observation_history.csv` — accumulating independent airport observations, where available.
- `forecast_archive/` — timestamped prospective forecast snapshots.

Missing forecasts are excluded from a four-model comparison rather than being treated as zero error or zero weather values. Coordinates, timestamps, units, and observation definitions must be aligned before verification.

## 🔄 System Pipeline

```text
Open-Meteo archived forecasts (ECMWF / GFS / ICON / GEM)
                           +
                 ERA5 reference data
                           ↓
       Clean and align by location + valid timestamp
                           ↓
    Compute |model forecast − ERA5 reference| for each model
                           ↓
     Build 11 forecast-available input features per variable
                           ↓
       Train four Random Forest error regressors
                           ↓
          Tune switching threshold on validation data
                           ↓
     Test ML-selected forecast MAE vs ECMWF-only baseline
                           ↓
     Fetch live model forecasts for the selected location
                           ↓
      Predict model errors → apply switching threshold
                           ↓
          Display selected forecast in Streamlit
                           ↓
   Archive forecasts → compare later with station observations
```

## 🧠 Machine Learning Methodology

### Target

For each weather parameter and forecasting model, the training target is its absolute error relative to the historical ERA5 reference:

```text
absolute_error = abs(model_forecast - ERA5_reference)
```

### Eleven Input Features

1. ECMWF forecast value
2. GFS forecast value
3. ICON forecast value
4. GEM forecast value
5. Forecast mean across four models
6. Forecast spread (maximum minus minimum)
7. Latitude
8. Longitude
9. Hour of day
10. Day of week
11. Month

The final deployed **forecast-only v5 approach** deliberately excludes lagged ERA5 error features so its inputs can be constructed from forecast, location, and time data at inference.

### Algorithm

The system trains **four `RandomForestRegressor` models per parameter** (20 regressors in total), one for each model's expected absolute error. It selects the smallest estimated-error model only when its advantage over the ECMWF estimate exceeds a parameter-specific threshold tuned on validation data; otherwise, it keeps ECMWF.

### Chronological Splits

| Split | Period |
|---|---|
| Training | October 2025 – June 2026 |
| Validation | July 2026 |
| Testing | August – September 2026 |

The chronological split helps avoid random mixing of earlier and later weather conditions. The original final-test period has been used to compare multiple experimental approaches; independent, prospective verification is therefore still important.

## 📈 Historical Results — Forecast-Only v5

Held-out evaluation against **ERA5**, using **45,384 complete location-hour comparisons per parameter**:

| Parameter | ML-selected MAE | ECMWF-only MAE | MAE reduction |
|---|---:|---:|---:|
| Temperature (°C) | 0.7504 | 0.7636 | **1.73%** |
| Humidity (% RH) | 4.2902 | 4.3155 | **0.59%** |
| Precipitation (mm) | 0.2490 | 0.3354 | **25.76%** |
| Wind speed (km/h) | 1.9869 | 2.0435 | **2.77%** |
| Mean sea-level pressure (hPa) | 0.4946 | 0.5474 | **9.64%** |

```text
MAE = mean(abs(forecast − reference))
MAE reduction (%) = 100 × (ECMWF MAE − ML-selected MAE) / ECMWF MAE
```

These figures measure **historical error reduction against ERA5**, **not live prediction accuracy percentages**. ERA5 is model-assisted reanalysis, and its relationship to ECMWF may affect comparative results.

## 🖥️ Dashboard Features

The Streamlit frontend (`app_model_verification_with_stations.py`) provides:

- Filters for weather variable, location/region, forecast period, and displayed horizon.
- A headline selection with the forecast value, latitude, and longitude.
- Four-model comparisons and an ML-estimated error leaderboard.
- An interactive location map and selected-location marker.
- An hourly model forecast chart.
- A backend decision trace: candidate forecasts, predicted errors, ranking, ECMWF switching threshold, and final selection.
- Historical ERA5-based performance cards, separate from live predicted errors.
- Preliminary station-reference and observational-verification information.
- CSV export of forecast results.

**Dashboard interpretation:** A lead-time-looking selection in a live saved snapshot may represent an offset from the displayed forecast hours, not the original NWP model initialization lead time. The snapshot is not continuously refreshed until the forecast collection script runs again.

## 📁 Main Project Structure

```text
WeatherModelSelector/
├── .streamlit/                           # Streamlit theme settings
├── app.py                                # Original dashboard
├── app_model_verification_with_stations.py  # Current presentation dashboard
├── data/
│   ├── historical_multivariable_forecast_12months_previous_day1.csv
│   ├── era5_multivariable_reference_12months.csv
│   ├── observations/
│   │   └── metar_observation_history.csv
│   └── forecast_archive/
├── models/
│   ├── temperature_forecast_only_selector_v5.pkl
│   ├── humidity_forecast_only_selector_v5.pkl
│   ├── precipitation_forecast_only_selector_v5.pkl
│   ├── wind_speed_forecast_only_selector_v5.pkl
│   └── pressure_forecast_only_selector_v5.pkl
├── results/
│   ├── step48_forecast_only_results.csv
│   ├── step49B_live_31locations_predictions.csv
│   └── step53_metar_actual_errors.csv
├── scripts/
│   ├── Step48_forecast_only_selector.py
│   ├── Step49B_live_31locations.py
│   ├── Step53_compare_metar_forecasts.py
│   ├── Step54_archive_forecasts.py
│   └── Step55_metar_history.py
└── README.md
```

Some additional preparation and experimentation scripts may be present. Large CSV and `.pkl` files may be stored locally rather than committed to GitHub.

## ⚙️ Setup and Running Locally

**Requirements:** Python 3.10+ recommended, an internet connection when downloading forecasts/observations, and installed Python packages.

```powershell
cd "C:\Projects\WeatherModelSelector"
python -m pip install pandas numpy scikit-learn streamlit plotly requests joblib
```

If your project has `requirements.txt`, prefer installing from it:

```powershell
python -m pip install -r requirements.txt
```

**Launch the dashboard:**

```powershell
streamlit run app_model_verification_with_stations.py --server.port 8509
```

Open `http://localhost:8509`.

**Refresh live forecast CSV** (requires API connectivity):

```powershell
python -u .\scripts\Step49B_live_31locations.py
```

**Archive a fresh set of forecasts:**

```powershell
python -u .\scripts\Step54_archive_forecasts.py
```

**Collect more independent METAR observations:**

```powershell
python -u .\scripts\Step55_metar_history.py
```

**Reproduce forecast-only training** (requires the historical CSV files; compute intensive):

```powershell
python -u .\scripts\Step48_forecast_only_selector.py
```

**Path configuration:** This project was moved from a OneDrive Desktop folder to `C:\Projects\WeatherModelSelector`. Confirm that `BASE`/`BASE_PATH` variables in scripts point to your local copy (or refactor them to resolve paths relative to the script) before executing. The existing dashboard uses saved local CSVs; opening it does not itself prove forecasts were refreshed.

## 🌍 Independent Observation Verification

The project has explored preliminary IMD station comparisons and downloaded airport **METAR** observations. In the available initial METAR comparison, usable temperature/wind matches were obtained for:

| Location | Station code | Approx. station-to-project distance |
|---|---|---:|
| Mumbai | VABB | 3.31 km |
| Delhi | VIDP | 10.39 km |
| Bengaluru | VOBL | 28.53 km |

The initial matching contained **three hours per city per parameter**, which is insufficient to establish reliable operational skill. Full verification requires more observations and strict matching of forecast issue time, valid time, station/grid location, wind definitions, and rainfall accumulation periods.

The project also archives forecasts prospectively in `data/forecast_archive/` so they can later be scored against observations that arrive after collection. The recorded collection-time horizon is not automatically identical to each weather model's initialization lead time.

## 🔮 Future Scope

- Extend independently verified performance to more IMD AWS/ARG, airport METAR, and other quality-controlled observing stations.
- Calculate prospective station-based MAE, RMSE, bias, temperature-within-tolerance rates, and confidence/sample-size indicators.
- Add rainfall verification using correctly matched accumulation windows, POD, FAR, and CSI.
- Evaluate actual NWP model initialization lead times separately and monitor performance over rolling 7-day/30-day periods.
- Experiment with station-specific bias correction and weighted ensembles.
- Improve automated data collection and deploy a regularly updated dashboard.

## 📚 References

- [Open-Meteo Weather Forecast API](https://open-meteo.com/en/docs)
- [Open-Meteo Previous Runs API](https://open-meteo.com/en/docs/previous-runs-api)
- [ECMWF ERA5 Reanalysis](https://www.ecmwf.int/en/forecasts/dataset/ecmwf-reanalysis-v5)
- [Aviation Weather Center API](https://aviationweather.gov/data/api/)
- [India Meteorological Department](https://mausam.imd.gov.in/)
- [Scikit-learn Random Forest Regressor](https://scikit-learn.org/stable/modules/generated/sklearn.ensemble.RandomForestRegressor.html)
- [Streamlit](https://streamlit.io/)

## 🔗 GitHub Repository

[**WeatherModelSelector — GitHub**](https://github.com/Utkarsha117/Machine-Learning/tree/main/WeatherModelSelector)

---

**Academic project note:** Report historical ERA5 comparisons as reanalysis-based evaluation, and independently measured METAR/IMD scores only when the available observation data supports them.
