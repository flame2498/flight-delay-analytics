# Sprint 3 Reports: Flight Delay Analytics

Dataset: Flight_Delays_50K_cleaned.csv (50,000 flights, 35 columns). Significance level α = 0.05 for all tests.

---

## 1. Hypothesis Testing Report

Delay columns are severely non-normal (Shapiro-Wilk p < 0.001 for ARRIVAL_DELAY, DEPARTURE_DELAY and DISTANCE). Every mean comparison was therefore paired with a non-parametric test, and large sample sizes support the Central Limit Theorem for the parametric versions.

### Test 1: One-sample T-test (is average arrival delay different from 0?)

| Item | Result |
|---|---|
| Null Hypothesis | True mean arrival delay = 0 minutes |
| Alternate Hypothesis | True mean arrival delay ≠ 0 minutes |
| Test Statistic | t = 23.469 (n = 49,085, sample mean = 4.00 min) |
| p-value | 3.91e-121 |
| Decision | Reject H0 |
| Business Interpretation | Flights are late on average. The effect is small in practical terms (Cohen's d ≈ 0.11) and the median flight arrives early, so the mean is pulled up by a long tail of severe delays. |

### Test 2: Welch's T-test and Mann-Whitney U (NK vs all other airlines)

| Item | Result |
|---|---|
| Null Hypothesis | NK's arrival delay is the same as all other airlines |
| Alternate Hypothesis | NK's arrival delay differs from all other airlines |
| Test Statistic | Welch t = 7.782; Mann-Whitney U = 28,476,099 |
| p-value | 1.76e-14 (Welch); 7.47e-30 (Mann-Whitney) |
| Decision | Reject H0 (both tests agree) |
| Business Interpretation | NK averages 14.51 min against 3.79 min for the rest, and its median is 2.0 min against -5.0 min. NK is worse for the typical flight, not only in the tail. Note NK has only 977 valid flights. |

### Test 3: One-way ANOVA and Kruskal-Wallis (top 6 airlines by volume)

Airlines: WN, DL, AA, OO, EV, UA. Group means: WN 4.39, DL 0.26, AA 2.25, OO 5.10, EV 6.51, UA 4.34.

| Item | Result |
|---|---|
| Null Hypothesis | All six airlines have the same mean arrival delay |
| Alternate Hypothesis | At least one airline's mean differs |
| Test Statistic | ANOVA F = 21.591; Kruskal-Wallis H = 414.494 |
| p-value | 1.19e-21 (ANOVA); 2.23e-87 (Kruskal-Wallis) |
| Decision | Reject H0 (both tests agree) |
| Business Interpretation | Punctuality differs by carrier even among the large ones. DL is best and EV is worst of the six. This test shows a difference exists but does not say which pairs differ. |

### Test 4: Chi-square test of independence (airline vs cancellation reason)

Reasons: A = Airline, B = Weather, C = National Air System. Reason D (1 flight) excluded. Airlines: AA, EV, MQ, OO, UA, WN (665 cancelled flights).

| Item | Result |
|---|---|
| Null Hypothesis | Cancellation reason is independent of airline |
| Alternate Hypothesis | Cancellation reason depends on airline |
| Test Statistic | χ² = 83.14, dof = 10 (all expected counts ≥ 11) |
| p-value | 1.21e-13 |
| Decision | Reject H0 |
| Business Interpretation | Cancellation causes differ by carrier (Cramér's V ≈ 0.25, a moderate effect). National Air System cancellations are 36% of EV's and 28% of MQ's, versus 5-12% for AA, OO, UA and WN. |

---

## 2. Confidence Interval Summary

| Variable | Mean | 95% CI |
|---|---|---|
| ARRIVAL_DELAY | 4.00 | (3.67, 4.33) |
| DEPARTURE_DELAY | 9.09 | (8.77, 9.40) |
| DISTANCE | 824.2 | (818.8, 829.6) |

Per-airline intervals: NK (11.83, 17.19) does not overlap any other airline. DL (-0.55, 1.07), AS (-2.52, 0.18) and HA (-0.20, 2.37) include 0, so they cannot be distinguished from on-time on average. WN has the tightest interval (width 1.23) because of its volume; VX and F9 have the widest because of small samples.

---

## 3. Feature Documentation

| Feature | Definition | Business purpose | Evidence |
|---|---|---|---|
| DELAY_CATEGORY | Arrival delay: ≤15 = On-time/Minor, 16-60 = Moderate, >60 = Severe; cancelled flights left blank | Converts a heavily skewed number into classes management can act on; 15 minutes is the standard on-time threshold | 82.1% / 12.6% / 5.3% of flights |
| DISTANCE_CATEGORY | Short (<500 mi), Medium (500-1500), Long (>1500) | Separates flight types with different delay recovery and cancellation behaviour | Mean arrival delay 4.84 / 4.30 / 0.67 min; cancel rate 2.2% / 1.4% / 0.6% |
| PEAK_HOUR | 1 if scheduled departure hour is 16-21, else 0 | Delays build up through the day, so evening departures need separate treatment | Mean delay 9.07 vs 1.71 min; 24.5% vs 14.9% delayed |
| BUSY_AIRPORT | 1 if the origin is one of the 9 highest-volume airports (ATL, ORD, DFW, LAX, DEN, PHX, SFO, IAH, LAS), including rows coded with numeric DOT IDs | Flags congestion-prone hubs | Mean delay 5.01 vs 3.47 min; 18.9% vs 17.3% delayed |
| AIRLINE_PERFORMANCE_SCORE | On-time rate × (1 − cancellation rate) × 100, per airline | One number to rank carriers on reliability | HA 89.1 (best) to NK 68.3 (worst); median 79.9 |
| DELAY_RISK | Points from PEAK_HOUR + BUSY_AIRPORT + below-median airline score: 0 = Low, 1 = Medium, 2-3 = High | Simple flight-level risk index for scheduling and passenger communication | Mean delay 0.14 / 4.06 / 8.62 min; 12.7% / 18.3% / 23.4% delayed |

Feature evaluation: DELAY_RISK separates flights cleanly (ANOVA and Kruskal-Wallis both p < 0.001), and PEAK_HOUR is the strongest single flag. DISTANCE_CATEGORY does not change the share of delayed flights (17-18% in every band); its value is that long flights recover time in the air. Limitation: about 8.5% of airport codes are numeric DOT IDs; only the nine busy hubs' IDs were mapped to IATA codes (verified by matching volumes), and other numeric IDs are treated as not busy. DELAY_RISK is a rule-based screening index, not a predictive model, and the airline score is built from the same data it is evaluated on.

---

## 4. Final Business Recommendations

Summary of findings: the average arrival delay is 4.0 minutes but the typical flight arrives early, so the problem lies in a tail of severe delays (5.3% of flights are over 60 minutes). Departure delay and arrival delay are almost perfectly linked (r = 0.94), so delays are created on the ground rather than en route. Late aircraft and airline-controlled causes drive delay minutes far more than weather (arrival delay correlates 0.62 with late-aircraft delay and 0.28 with weather delay).

| Goal | Recommendation | Evidence |
|---|---|---|
| Improve on-time performance | Focus on departure punctuality: tighten turnaround and boarding procedures | Departure vs arrival delay r = 0.94 |
| Optimize scheduling | Add schedule buffer and spare aircraft for 16:00-21:59 departures, and avoid scheduling tight aircraft rotations in the evening | Peak-hour mean delay 9.1 vs 1.7 min; delays climb from negative before 9am to about 10 min by 20:00 |
| Reduce operational delays | Target late-aircraft and airline-caused delays first, since weather is a small share | Late-aircraft r = 0.62, weather r = 0.28 |
| Airline focus | Prioritize a punctuality review for NK, F9, MQ and EV; use DL and AS practices as internal benchmarks | NK mean 14.5 min (CI 11.8-17.2); DL 0.3 min; EV highest of the top six |
| Reduce cancellations | Investigate National Air System-related cancellations at EV and MQ (regional operations); prepare weather contingency plans for AA, OO and WN | EV 36% and MQ 28% reason C; overall cancellation rate 1.6% |
| Optimize airport operations | Add gate and ground-crew capacity at the nine high-volume hubs during evening hours | Busy-airport mean delay 5.0 vs 3.5 min |
| Improve passenger satisfaction | Use the DELAY_RISK index to give early warnings and rebooking options on High-risk flights (evening, busy hub, low-scoring airline) | High-risk flights: 23.4% delayed vs 12.7% for Low |
