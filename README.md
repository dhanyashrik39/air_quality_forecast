# Los Angeles Air Quality: Analysis and Next-Day AQI Forecasting

## Problem
Air quality in Los Angeles is shaped by seasonal ozone, wildfire smoke, and weather.
This project asks: **what drives daily AQI, and how well can we forecast tomorrow's AQI?**
It also looks at how the January 2025 wildfires affected air quality.

## Data
| Source | Content | Period |
|---|---|---|
| [EPA AirData](https://www.epa.gov/outdoor-air-quality-data) | Daily AQI for the Los Angeles-Long Beach CBSA | 2019-2025 |
| [Open-Meteo](https://open-meteo.com/) | Daily temperature, humidity, rain, wind | 2019-2025 |

Both sources are loaded directly in the notebook; no manual download is needed
(see notes below if the EPA download fails).

## Approach
1. **Data cleaning:** merged AQI and weather by date, checked for missing days
   (0 found), interpolated short gaps (up to 3 days), and kept real AQI spikes
   (wildfire days are events, not errors).
2. **Feature engineering:** month, day of week, lagged AQI (1, 2, 7 days),
   7-day rolling mean, lagged wind, and the main pollutant of the day.
3. **EDA:** seasonality, pollutant mix by month, weather correlations, yearly trends.
4. **Statistical test:** Mann-Whitney U test comparing January 2025 (post-fire)
   with previous Januaries.
5. **Modeling:** persistence baseline vs. Random Forest vs. XGBoost, using a
   time-based split (train 2019-2023, test 2024-2025).
6. **Error analysis:** examined the largest misses and error distribution.

## Results
| Model | MAE | RMSE | Correct AQI category |
|---|---|---|---|
| Baseline (yesterday's AQI) | 17.6 | 24.3 | 65% |
| Random Forest | 17.0 | 22.8 | 68% |
| XGBoost | 17.1 | 23.6 | 69% |

**Wildfire impact:** mean AQI after Jan 7, 2025 was 85 vs 71 in previous
Januaries (Mann-Whitney p = 0.5836).

![AQI timeline](images/aqi_timeli<img width="1032" height="384" alt="aqi_timeline" src="https://github.com/user-attachments/assets/ce77e238-d588-4b50-bb0c-97d768b18b38" />
ne.png)
![Permutation Importanc]<img width="1210" height="495" alt="predicted" src="https://github.com/user-attachments/assets/d5075e9d-fdba-45a6-8afc-66188e662401" />
<img width="721" height="447" alt="permutation" src="https://github.com/user-attachments/assets/ba981abf-b910-4e01-8110-3ba0314bec1a" />
(images/permutation.png)
![Actual vs predicted](images/<img width="721" height="447" alt="permutation" src="https://github.com/user-attachments/assets/81da5c4f-358b-44f1-911f-02d0ae05e230" />
<img width="1210" height="495" alt="predicted" src="https://github.com/user-attachments/assets/2035ca4d-0145-48c4-a41b-316ffc014384" />
predicted.png)

## Key findings
- [Seasonal pattern, e.g. ozone-driven summers and PM2.5-driven winters]
- [How much the model improves on the baseline, or an honest note if it barely does]
- [Wildfire effect size]
- Largest errors occur on sudden-event days (e.g. fire onset) and when high-AQI
  streaks end, because the model relies heavily on recent AQI values.

## Limitations
- Single metro area; results may not generalize to other cities.
- Persistence is a strong baseline for next-day AQI, so gains are modest.
- Sudden events (wildfires) are not predictable from weather and lag features alone.
- EPA reports the highest AQI among monitors in the area, not an average,
  so it reflects the worst local conditions.

## Next steps
- Add fire-detection or satellite smoke data to address sudden-event misses
- Reframe as classification (unhealthy day: yes/no) with a recall-focused threshold
- Estimate the wildfire effect causally (regression with weather controls or
  comparison cities)
- Deploy a Streamlit app showing tomorrow's forecast

## How to run
1. Click the "Open in Colab" badge above (or upload the notebook to Colab).
2. Run all cells (Runtime > Run all).
3. If the EPA download fails, download the `daily_aqi_by_cbsa_YYYY.zip` files from
   EPA's "Pre-generated Data Files" page, upload them to Colab, and read them
   with `pd.read_csv`.

**Requirements:** see `requirements.txt` (pandas, numpy, scipy, matplotlib,
seaborn, plotly, scikit-learn, xgboost, requests).

## Author
Dhanyashri K 
