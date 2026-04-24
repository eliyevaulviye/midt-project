## 1. Problem Definition


### What we built:
A machine learning system that predicts cotton yield (in tonnes) for 29 districts of Azerbaijan for the year 2025, and also estimates how risky each growth stage of the cotton season will be.


## 2. Data Sources


Source 1 — Cotton Production Dataset  

- 29 districts × 25 years (2000–2024) = 725 observations
- Values are annual cotton yield in tonnes per district
- Had 191 missing values which we filled using each region's own historical average so we didn't lose those rows


Source 2 — Open-Meteo Historical Weather API 

- We pulled daily weather for 5 weather station locations across Azerbaijan: Ganja, Sabirabad, Lankaran, Shamkir, Nakhchivan
- Variables collected: max/min/mean temperature, precipitation, humidity, wind speed, evapotranspiration (ET0)
- 25 years × 365 days × 5 locations = ~45,000 daily rows



## 3. The Core Problem — Joining Two Datasets


### Solution — Region to Weather Station Mapping
#### We geographically mapped every district to its nearest weather station: 


- Ganja station covers 16 central and western districts
- Sabirabad station covers 7 Kür-Araz lowland districts
- Lankaran station covers 4 southern districts
- Jabrayil and Lachin were mapped to nearest available stations



## 4. Feature Engineering — The Bridge Between Weather and Yield


#### Raw daily weather data cannot go directly into a machine learning model meaningfully



### Solution — Growth Stage Aggregation
#### Cotton has 4 distinct biological growth stages, and weather affects yield differently in each one:


| Stage         | Months              | What matters                                      |
|--------------|--------------------|--------------------------------------------------|
| Planting     | March–April        | Temperature must be warm enough to germinate     |
| Growing      | May–August         | Heat accumulation, drought stress, GDD           |
| Boll Forming | August–September   | Extreme heat damages bolls                       |
| Harvest      | September–November | Rain and humidity ruin picking conditions        |



#### For each stage we calculated aggregated features like mean temperature, total rainfall, heat stress days (days above 35°C), dry streaks, humidity and most importantly Growing Degree Days (GDD).




```
planting_avg_temp    = mean(daily temp, March–April)
planting_total_rain  = sum(daily rain, March–April)
growing_avg_temp     = mean(daily temp, June–August)
growing_total_rain   = sum(daily rain, June–August)
growing_heat_stress  = count(days where temp > 35°C, June–August)
harvest_dry_days     = count(days where rain < 1mm, Sep–Nov)
... etc.
```

### What is GDD? 


What is GDD?
GDD is the standard agricultural measure of heat energy accumulated by a crop. For each day: **GDD = ((max_temp + min_temp) / 2) - 15.5°C**. The total GDD across the growing season tells us **how much energy the cotton plant received to develop**. It's far more meaningful than raw temperature alone.


##### This process turned 365 daily rows per location per year into roughly **52 meaningful features per district per year**  — one row in our final ML dataset.


## 5. Risk Label Creation


```
Year 2025, Ganja Region:
  🟡 Planting Stage     — 34% risk  (slightly cold, low moisture)
  🔴 Growing Stage      — 78% risk  (heat stress very likely)
  🟢 Boll Formation     — 12% risk  (conditions look normal)
  🟡 Harvest Stage      — 45% risk  (rain probability moderate)

  → Predicted Yield: 3,800 tonnes  (↓ 18% below average)
```


##### How we defined risk:
##### We used two approaches together:

##### Threshold based: agronomic thresholds like "more than 10 days above 35°C during growing season = risky"
##### Yield based: a year was labeled risky if the yield dropped more than 0.5 standard deviations below that district's own historical average



## 6. Machine Learning Architecture


```
We built 5 models total:
4 Risk Classifiers (one per growth stage)

Algorithm: Random Forest Classifier
Input: weather features for that stage
Output: probability of that stage being risky (0–100%)
These run first and their output becomes additional features for the yield model

1 Yield Regression Model

Algorithm: XGBoost Regressor (won against Random Forest)
Input: all 52 weather features + 4 risk probability scores from classifiers
Output: predicted yield in tonnes 
```


```
Train/test split:
We trained on years 2000–2021 and tested on 2022–2024. We deliberately split by year rather than randomly because you cannot train on future data and test on past data — that would be data leakage and give artificially perfect results.
```


## 7. Model Performance


```
Is this good?
For a model using only weather data with no soil quality, irrigation, fertilizer or farming practice data — R²=0.576 is a solid result. The remaining 42% of variance is likely explained by non-weather factors we don't have access to.
```


## 8. 2025 Predictions


```
Overall:

Total predicted yield across all 29 districts: 494 tonnes
Historical average: 518 tonnes
Predicted change: -4.5% decline
```


```
Key findings:

Ganja and Sabirabad zones face 92–96% growing season risk due to heat stress — the dominant threat for 2025
Lankaran zone (southern humid region) shows a completely different pattern — low heat risk but 64% harvest risk from rainfall and humidity
Jalilabad district faces the biggest predicted drop at -24.6%
Aghdara district is the top producer at 37 tonnes predicted
```


## 9. Limitations 


```
Every good project acknowledges its limitations:

Only weather features — no soil data, irrigation records, fertilizer use or farming practice data which all affect yield significantly
5 stations for 29 districts — districts sharing a weather station get identical weather features, which loses regional variation within zones
Small dataset — 725 rows is limited for machine learning; more years or more crops would improve the model
Risk classifier imbalance — planting and harvest risk classifiers had very few risky cases in the 2022–2024 test period, so their test accuracy is misleading
2025 future months — since we're predicting a full year but the growing season isn't finished, the future months were filled using climatological averages from 2024 rather than actual forecasts
```

## 10. What Makes This Project Valuable


```
It combines real government agricultural data with real-time weather API data
It is explainable — you can tell a farmer not just "yield will be low" but specifically "because growing season heat stress is at 96% risk"
It covers all 29 cotton-producing districts of Azerbaijan simultaneously
The pipeline is reusable — next year you run one script and get updated predictions
It can be extended to other crops (wheat, vegetables) by changing growth stage definitions and retraining
```





















