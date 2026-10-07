# Statistical Analysis Report: Flight Delay Analytics

**Data:** Flight_Delays_50K_cleaned.csv (50,000 rows, 35 columns) | **Libraries:** NumPy, Pandas, SciPy | **Significance level:** α = 0.05

## 1. Central Tendency and Dispersion

| Variable | n | Mean | Median | Std dev | Skewness | Min | Max |
|---|---|---|---|---|---|---|---|
| ARRIVAL_DELAY | 49,085 | 4.00 | -5.0 | 37.75 | 6.39 | -70 | 1,574 |
| DEPARTURE_DELAY | 49,234 | 9.09 | -2.0 | 35.46 | 7.27 | -55 | 1,515 |
| DISTANCE | 50,000 | 824.2 | 646 | 611.89 | 1.45 | 31 | 4,983 |

**Interpretation:** For both delay variables the mean is far above the median and skewness exceeds 6. A small number of extreme delays pulls the mean up, so the median describes the typical flight better. The standard deviation is about nine times the mean arrival delay, showing very high variability. Distance is moderately right-skewed.

## 2. Distribution Analysis (Shapiro-Wilk, 5,000-row random samples)

| Variable | W statistic | p-value | Decision |
|---|---|---|---|
| ARRIVAL_DELAY | 0.5874 | 4.75e-76 | Reject normality |
| DEPARTURE_DELAY | 0.4456 | 2.14e-82 | Reject normality |
| DISTANCE | 0.8780 | 1.29e-52 | Reject normality |

**Interpretation:** All three variables are decidedly non-normal (W = 1 would be perfectly normal). Consequently every mean comparison in the hypothesis tests was paired with a non-parametric equivalent (Mann-Whitney U for the T-test, Kruskal-Wallis for ANOVA), while large sample sizes support the Central Limit Theorem for the parametric versions.

## 3. Covariance and Correlation

| Pair | Covariance | Pearson correlation |
|---|---|---|
| DEPARTURE_DELAY vs ARRIVAL_DELAY | 1,251.9 | 0.941 |
| AIR_TIME vs DISTANCE | 43,833.8 | 0.985 |
| ARRIVAL_DELAY vs LATE_AIRCRAFT_DELAY | 466.6 | 0.623 |
| ARRIVAL_DELAY vs AIRLINE_DELAY | 485.3 | 0.620 |
| ARRIVAL_DELAY vs AIR_SYSTEM_DELAY | 207.1 | 0.434 |
| ARRIVAL_DELAY vs WEATHER_DELAY | 90.9 | 0.278 |

**Interpretation:**
- Covariance gives direction only (all positive here). Its size depends on variable scale: AIR_TIME vs DISTANCE has a covariance about 35 times larger than DEPARTURE_DELAY vs ARRIVAL_DELAY, but a correlation only slightly higher (0.985 vs 0.941), because DISTANCE has a variance of 374,407. Relationships should be ranked by correlation, not covariance.
- Departure delay is almost perfectly linked to arrival delay, meaning delays are largely created before takeoff.
- Among delay causes, late aircraft (0.623) and airline-controlled delay (0.620) relate most strongly to arrival delay; weather (0.278) is weakest.

**Sign check, arrival delay vs flight length:**

| | AIR_TIME | SCHEDULED_TIME | DISTANCE |
|---|---|---|---|
| ARRIVAL_DELAY (Pearson) | -0.008 | -0.033 | -0.028 |
| ARRIVAL_DELAY (Spearman) | -0.040 | -0.095 | -0.070 |
| DEPARTURE_DELAY (Pearson) | 0.025 | 0.028 | 0.025 |
| DEPARTURE_DELAY (Spearman) | 0.084 | 0.086 | 0.093 |

Both methods agree, and all values are very small. Departure delay rises very slightly with flight length while arrival delay falls very slightly, consistent with longer flights having more time in the air to recover lost minutes. The effects are too weak to be operationally important.

## 4. Hypothesis Tests

| Test | Hypotheses (H0 / H1) | Statistic | p-value | Decision |
|---|---|---|---|---|
| One-sample T | Mean arrival delay = 0 / ≠ 0 | t = 23.469 | 3.91e-121 | Reject H0 |
| Welch T, NK vs others | Equal mean delay / different | t = 7.782 | 1.76e-14 | Reject H0 |
| Mann-Whitney U, NK vs others | Same distribution / different | U = 28,476,099 | 7.47e-30 | Reject H0 |
| One-way ANOVA, top 6 airlines | All means equal / at least one differs | F = 21.591 | 1.19e-21 | Reject H0 |
| Kruskal-Wallis, top 6 airlines | Same distribution / differ | H = 414.494 | 2.23e-87 | Reject H0 |
| Chi-square, airline × cancellation reason | Independent / dependent | χ² = 83.14, dof = 10 | 1.21e-13 | Reject H0 |

**Interpretation:**
- **Statistical vs practical significance:** the 4.0-minute mean is highly significant only because n is large; Cohen's d ≈ 0.11 is small, and the median flight arrives early.
- NK's mean (14.51 min) vs others (3.79 min) is supported by both parametric and non-parametric tests, and its median (2.0 vs -5.0 min) confirms the typical NK flight is worse.
- Punctuality differs among the six largest carriers (WN, DL, AA, OO, EV, UA). ANOVA does not say which pairs differ; a Tukey post-hoc test would.
- Cancellation cause depends on carrier (Cramér's V ≈ 0.25, a moderate effect). All expected cell counts were at least 11, so the chi-square assumptions hold. Reason D (1 flight) was excluded.

## 5. Confidence Intervals (95%, t-based)

| Variable | Mean | 95% CI |
|---|---|---|
| ARRIVAL_DELAY | 4.00 | (3.67, 4.33) |
| DEPARTURE_DELAY | 9.09 | (8.77, 9.40) |
| DISTANCE | 824.2 | (818.8, 829.6) |

Selected airlines, mean arrival delay:

| Airline | n | Mean | 95% CI |
|---|---|---|---|
| DL | 7,442 | 0.26 | (-0.55, 1.07) |
| WN | 10,796 | 4.39 | (3.77, 5.00) |
| EV | 4,802 | 6.51 | (5.39, 7.64) |
| NK | 977 | 14.51 | (11.83, 17.19) |

**Interpretation:** We are 95% confident the true mean arrival delay is between 3.7 and 4.3 minutes; zero lies far outside, consistent with the T-test. Interval width shrinks with sample size (WN is tightest; VX and F9 are widest). NK's interval does not overlap any other airline, DL, AS and HA include 0, and so cannot be distinguished from on-time on average.

## 6. Engineered Feature Evaluation

| Feature | Groups | Mean arrival delay | % delayed >15 min |
|---|---|---|---|
| PEAK_HOUR | 0 / 1 | 1.71 / 9.07 | 14.9% / 24.5% |
| BUSY_AIRPORT | 0 / 1 | 3.47 / 5.01 | 17.3% / 18.9% |
| DISTANCE_CATEGORY | Short / Medium / Long | 4.84 / 4.30 / 0.67 | 17.7% / 18.2% / 17.0% |
| DELAY_RISK | Low / Medium / High | 0.14 / 4.06 / 8.62 | 12.7% / 18.3% / 23.4% |

**Interpretation:** DELAY_RISK increases steadily across Low, Medium and High, and its differences are significant under both ANOVA and Kruskal-Wallis (ANOVA F = 178.6, Kruskal-Wallis H = 363.9, both p < 0.001). PEAK_HOUR is the strongest single flag. DISTANCE_CATEGORY does not change the share of delayed flights but does change the delay size, since long flights recover time in the air.

## 7. Conclusions and Limitations

- Delays are highly skewed and non-normal, so medians and non-parametric tests give the more reliable picture.
- Departure delay, late-aircraft delay and airline-controlled delay explain arrival delay far better than weather does.
- Airline, time of day and airport traffic all show statistically significant effects on delay.
- Limitations: about 8.5% of airport codes are numeric DOT IDs (only the nine busiest hubs were mapped for BUSY_AIRPORT); a sample of 50,000 flights; small carriers have wide intervals; Shapiro-Wilk was run on 5,000-row samples; the risk index is descriptive, not predictive.
