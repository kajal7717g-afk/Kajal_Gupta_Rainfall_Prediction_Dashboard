# 🌧️ Dashboard for Rain Prediction in India

### An End-to-End Climatological Analysis & Predictive Modeling System (1901 – 2015 & Upcoming-Year Forecast)

---

## 1. Project Title
**Dashboard for Rain Prediction in India: Regional Patterns, Historical Trends, and Upcoming-Year Forecast**

---

## 2. Objective
The primary goals of this project are:
1. **Historical Climatological Analysis**: Uncover regional, seasonal, and decadal precipitation dynamics across India using 115 years (1901–2015) of observed meteorological data from the Indian Meteorological Department (IMD).
2. **Predictive Modeling**: Develop an explainable and rigorously evaluated statistical/machine-learning model to forecast **annual rainfall for the upcoming year (2016)** across all 36 meteorological subdivisions of India and nationally.
3. **Interactive Visual Dashboard**: Deliver a unified, interactive dashboard natively on the **Clario** AI canvas and directly in any web browser, equipped with KPI cards, multi-dimensional filters, trend visualizations, geographic mapping, and prediction blotters.

---

## 3. Datasets Used

The project integrates two official meteorological datasets:

| Dataset | Granularity | Records | Scope | Key Features |
|---|---|---|---|---|
| **`rainfall_india.csv`** | 36 Meteorological Subdivisions | 4,116 rows | 1901 – 2015 (115 years) | `SUBDIVISION`, `YEAR`, Monthly (`JAN`–`DEC`), `ANNUAL`, Seasonal (`Jan-Feb`, `Mar-May`, `Jun-Sep`, `Oct-Dec`) |
| **`district wise rainfall normal.csv`** | 641 Districts across 35 States / UTs | 641 rows | Climatological 30-year Normals | `STATE_UT_NAME`, `DISTRICT`, Monthly (`JAN`–`DEC`), `ANNUAL`, Seasonal groupings |

### Geographical Hierarchy:
- **National Level**: All-India aggregated annual and seasonal rainfall
- **Regional Level**: 7 Broad Geographical Zones (North, South, East, West, Central, North-East, Islands)
- **Subdivision Level**: 36 Meteorological subdivisions recognized by IMD
- **District / State Level**: 641 districts across 35 Indian States and Union Territories

---

## 4. Tools & Technologies Used

- **Primary Tool — Clario**:
  - Leveraged as the primary visual analytics and canvas system (`.clario/frames/dashboard.html` and `projects/project_rain/canvases/canvas_1_mvs85ftf.json`).
  - Utilized Clario's local iframe sandboxing, Plotly.js shimming, and live visual canvas architecture.
- **Python 3.13**:
  - `pandas` (v3.0.6) & `numpy` (v2.4.2): Data cleaning, anomaly identification, temporal aggregation, and feature engineering.
  - `scikit-learn` (v1.9.1): Predictive modeling, cross-validation, baseline comparisons, and evaluation metrics ($R^2$, $MAE$, $RMSE$, $MAPE$).
  - `joblib` (v1.6.0): Model persistence (`models/rainfall_predictor.joblib`).
- **Frontend & Visualization**:
  - **Plotly.js (v2.35.2)**: Interactive scatter, multi-bar, pie, choropleth geo bubble maps, and decadal charts.
  - **HTML5 / CSS3 / Vanilla JavaScript**: Zero-dependency, responsive, theme-consistent dashboard running client-side with embedded high-speed JSON data.

---

## 5. Data Cleaning Process

Before feeding data into analysis or predictive models, the following systematic steps were executed:

1. **Delimiter Handling**:
   - Resolved tab-separated delimiter formats (`\t`) across both datasets, preventing single-column truncation bugs.
2. **Missing Value Treatment**:
   - Monthly values in `rainfall_india.csv` had minor missing counts (4 in Jan, 3 in Feb, up to 11 in Nov). Missing monthly values were imputed using each specific subdivision's long-term historical median for that month.
   - Recalculated all composite season columns (`Jan-Feb`, `Mar-May`, `Jun-Sep`, `Oct-Dec`) and `ANNUAL` totals strictly from the verified monthly values, eliminating 26 missing rows in the raw `ANNUAL` column.
3. **Data Type Standardization**:
   - Stripped leading/trailing whitespace across column headers, subdivision names, state names, and district identifiers.
   - Enforced strict numerical floats for rainfall measurements and integers for calendar years.
4. **Geographical Normalization**:
   - Rectified typographical naming inconsistencies (e.g. `MATATHWADA` $\rightarrow$ `MARATHWADA`).
   - Mapped all 36 subdivisions to 7 standardized geographic zones (`North`, `South`, `East`, `West`, `Central`, `North-East`, `Islands`).
5. **Output Verification**:
   - Verified that cleaned files (`data/cleaned/rainfall_india_cleaned.csv` and `data/cleaned/district_rainfall_cleaned.csv`) have **0 null values**, zero duplicate rows, and 100% mathematical consistency.

---

## 6. Exploratory & Historical Rainfall Analysis

| Analytical Metric | Value / Observation | Historical Context / Significance |
|---|---|---|
| **All-India Annual Mean** | **1,417.2 mm** | Baseline climatological normal over 115 years |
| **Wettest Year (National)** | **1961 (1,717.1 mm)** | Extreme deluge year (+21.2% above normal) |
| **Driest Year (National)** | **1972 (1,146.5 mm)** | Major historical drought year (-19.1% deficit, severe El Niño) |
| **Monsoon Contribution** | **75.2% (1,066.0 mm)** | Southwest Monsoon (Jun–Sep) dominates the national water budget |
| **Pre-Monsoon Share** | **11.7% (165.9 mm)** | Convective thunderstorms / Kalbaisakhi (Mar–May) |
| **Post-Monsoon Share** | **9.6% (136.0 mm)** | Retreating monsoon / Northeast monsoon (Oct–Dec) |
| **Winter Share** | **3.5% (49.3 mm)** | Western disturbances in Northern India (Jan–Feb) |

### Regional Disparities:
- **Highest Rainfall Subdivision**: **Arunachal Pradesh** (3,435.0 mm/year), followed by **Coastal Karnataka** (3,408.4 mm/year) and **Konkan & Goa** (2,977.6 mm/year).
- **Lowest Rainfall Subdivision**: **West Rajasthan** (292.7 mm/year), followed by **Saurashtra & Kutch** (495.1 mm/year) and **Haryana Delhi & Chandigarh** (530.4 mm/year).
- **Highest Rainfall District**: **Tamenglong, Manipur** (7,229.3 mm/year) and **Jaintia Hills, Meghalaya** (6,379.9 mm/year).
- **Lowest Rainfall District**: **Ladakh (Leh)** (94.6 mm/year).

---

## 7. Prediction Methodology

### Model Selection & Feature Engineering
Rather than employing a black-box deep learning architecture unsuitable for annual tabular series, we engineered an **explainable time-series regression framework**:
- **Lag Features**: Previous 1-year (`LAG_1`), 2-year (`LAG_2`), and 3-year (`LAG_3`) annual rainfall.
- **Rolling Trends**: 3-year moving average (`ROLL_MEAN_3`) and 5-year moving average (`ROLL_MEAN_5`).
- **Volatility**: 5-year rolling standard deviation (`ROLL_STD_5`).
- **Subdivision Baseline**: Expanding historical mean (`SUB_HIST_MEAN`) capturing localized climate normal.
- **Calendar Trend**: Secular year trend index (`YEAR`).

### Out-of-Sample Model Comparison (Train: 1906–2005 [3,576 samples], Test: 2006–2015 [360 samples]):

| Model Architecture | MAE (mm) | RMSE (mm) | $R^2$ Score | MAPE (%) | Assessment |
|---|---|---|---|---|---|
| **5-Year Moving Average (Baseline)** | 222.29 | 293.03 | 0.8856 | 19.07% | Good baseline but lags inflection points |
| **Random Forest Regressor** | 225.29 | 297.69 | 0.8819 | 19.54% | Prone to leaf-boundary truncation on extremes |
| **Linear Regression** | 215.56 | 289.46 | 0.8884 | 18.00% | High performance, susceptible to collinearity |
| **Ridge Regression (Selected)** | **215.56** | **289.46** | **0.8884** | **18.00%** | **Optimal regularization, robust, fully explainable** |

### Upcoming-Year Prediction (2016):
- **All-India Forecast**: **1,387.9 mm** (slight deficit of -2.1% relative to the 115-year baseline of 1,417.2 mm).
- **Categorization Rule**:
  - **Excess / Above Normal**: Anomaly $> +10\%$
  - **Normal**: Anomaly between $-10\%$ and $+10\%$
  - **Deficient / Below Normal**: Anomaly $< -10\%$
- **Outlook**: 31 out of 36 subdivisions forecast to remain within Normal range; mild deficit predicted in Arunachal Pradesh, Assam & Meghalaya, and East Uttar Pradesh following back-to-back weak monsoons in 2014 and 2015.

---

## 8. Dashboard Features

The dashboard is delivered in `.clario/frames/dashboard.html` (and `dashboard.html`):
1. **Interactive Filter Bar**:
   - **Subdivision Selector**: Switch between All-India aggregated view and individual subdivisions.
   - **Geographic Zone Selector**: Slice data by North, South, East, West, Central, North-East, and Islands.
   - **Season Filter**: Dynamically filter all charts by Annual, Monsoon (Jun–Sep), Summer (Mar–May), Post-Monsoon (Oct–Dec), or Winter (Jan–Feb).
   - **Historical Year Slider (1901 – 2015)**: Adjust the time horizon to inspect trends across custom eras.
   - **Instant Reset**: Return to baseline in one click.
2. **5 Key Performance Indicator (KPI) Cards**:
   - Selected Average Rainfall
   - Historical Record Deluge / Peak
   - Highest Rainfall Region
   - Lowest Rainfall Region
   - Predicted Rainfall for Upcoming Year (2016) with status badge
3. **6 Coordinated Visualizations**:
   - *Century Time-Series Trend*: Observed annual line + 5-year rolling trendline + reference normal.
   - *Seasonal Distribution Donut*: Percentage breakdown across Monsoon, Pre-monsoon, Post-monsoon, and Winter.
   - *Historical Actual vs Predicted (Upcoming Year)*: Grouped comparison separating 2015 Actual, Historical Normal, and 2016 Predicted values.
   - *Decadal Climate Shifts*: Bar chart comparing 10-year climate epochs from 1900s through 2010s.
   - *Geographical Rainfall Distribution Map*: India map showing subdivision centroids with precipitation intensity color scale and tooltips.
   - *Subdivision Comparative Ranking*: Horizontal bar comparison showing wettest to driest subdivisions.
4. **Subdivision Blotter Table**:
   - Searchable, sortable table listing all 36 subdivisions with historical mean, 2015 actual, 2016 predicted, percentage anomaly, and categorical badge.

---

## 9. Key Insights Supported by Data

1. **Monsoon Reliance**: Across 115 years, the Southwest Monsoon consistently contributes **$75.2\%$** of India's annual rain. In states like Gujarat and Maharashtra, this exceeds **$88\%$**.
2. **Climate Anomaly Signals**:
   - **1961 Deluge**: Highest national rainfall on record (1,717.1 mm).
   - **1972 Drought**: Lowest national rainfall on record (1,146.5 mm), recording a national shortfall of nearly 20%.
3. **Regional Heterogeneity**: India exhibits extreme rainfall polarization: North-Eastern and Coastal Western Ghat regions receive 7 to 11 times more rainfall than Western Rajasthan and the rain-shadow Deccan plateau.
4. **Decadal Variation**: The 1930s–1950s experienced relatively wet monsoon epochs (decadal averages exceeding 1,440 mm), while the 1970s and 2000s exhibited pronounced dry cycles with heightened frequency of sub-1,350 mm years.
5. **Upcoming Year Outlook**: The Ridge Regression model forecasts an All-India rainfall of **1,387.9 mm for 2016**, representing an anticipated recovery from the severe 2014–2015 drought years.

---

## 10. How to Run and Use the Project

### Prerequisites:
- Python 3.10+ (installed at system level)
- Libraries: `pandas`, `numpy`, `scikit-learn`, `joblib`, `plotly`

### Step-by-Step Execution:

1. **Run Data Cleaning and Exploratory Analysis**:
   ```powershell
   python scripts/clean_and_eda.py
   ```
   *Generates `data/cleaned/rainfall_india_cleaned.csv`, `data/cleaned/district_rainfall_cleaned.csv`, and `output/eda_summary.json`.*

2. **Run Predictive Modeling**:
   ```powershell
   python scripts/predict_rainfall.py
   ```
   *Trains the Ridge Regression model, performs train-test evaluation, generates forecasts for the upcoming year 2016, and outputs `models/predictions_upcoming_year.csv` and `models/rainfall_predictor.joblib`.*

3. **Build Dashboard Payload & HTML**:
   ```powershell
   python scripts/build_dashboard_data.py
   python scripts/build_dashboard_html.py
   ```
   *Compiles `.clario/frames/dashboard.html` and `dashboard.html` with all charts and embedded JSON data.*

4. **View the Dashboard**:
   - **In Clario / VS Code / Antigravity**: Open the Clario Canvas (`Project.Rain` project canvas `canvas_1_mvs85ftf`) to interact with the live frame.
   - **In Any Browser**: Double-click `dashboard.html` in the workspace root, or open with Chrome/Edge/Firefox:
     ```powershell
     Start-Process "dashboard.html"
     ```

---

## 11. Limitations

1. **Temporal Horizon of Dataset**: The IMD historical dataset extends up to 2015. Predictions for the "upcoming year" correspond to the immediate subsequent cycle (2016).
2. **Absence of Atmospheric Predictors**: This dataset contains historical precipitation observations only. Teleconnection climate indices (ENSO / Niño 3.4 index, Indian Ocean Dipole, Madden-Julian Oscillation) are external and not present in the IMD rainfall record.
3. **District-Level Temporal Depth**: While subdivision data covers 115 continuous years, the district dataset provides climatological normals rather than year-by-year history. Therefore, time series modeling was properly executed at the subdivision level.

---

## 12. Final Conclusion

This project delivers a complete, statistically rigorous, and visually compelling analysis of Indian rainfall patterns spanning over a century. By transforming raw meteorological records into an interactive, multi-dimensional Clario dashboard and pairing it with a validated Ridge Regression forecasting engine ($R^2 = 0.888$), the assignment achieves all research, data cleaning, modeling, and visualization objectives with complete reproducibility and professional quality.

