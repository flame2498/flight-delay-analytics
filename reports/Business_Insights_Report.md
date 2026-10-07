# Business Insights Report: Flight Delay Analytics

**Prepared for:** Airline management | **Data:** 50,000 flight records (Flight_Delays_50K_cleaned.csv) | **Cancelled flights:** 1.59%

## 1. Executive Summary

Customer complaints about delays are real, but "average delay" is a misleading headline. The mean arrival delay is 4.0 minutes, yet the median is -5.0 minutes and 60.4% of flights arrive early. The problem is a tail of severe delays: 2,610 flights (5.3%) arrive more than 60 minutes late. These delays are created on the ground, by late aircraft and airline-controlled causes, not by weather. Delays build through the day, peak in the evening, and differ sharply by carrier.

## 2. Key Findings

| # | Finding | Evidence |
|---|---|---|
| 1 | Most flights are fine; a small tail does the damage | 82.1% on-time/minor (≤15 min), 12.6% moderate, 5.3% severe (>60 min) |
| 2 | Delays are created before departure | Departure vs arrival delay correlation is 0.94 |
| 3 | Weather is a small factor | Share of delay minutes: late aircraft 39.6%, airline 32.2%, air system 23.1%, weather 5.1% |
| 4 | Evenings are the problem window | Departures 16:00-21:59: mean delay 9.1 min and 24.5% delayed, vs 1.7 min and 14.9% otherwise |
| 5 | Carriers differ significantly | NK averages 14.5 min (CI 11.8-17.2) vs 3.8 for all others; DL averages 0.3 min. ANOVA and Kruskal-Wallis on the top six airlines both p < 0.001 |
| 6 | Cancellation causes differ by carrier | National Air System causes are 36% of EV and 28% of MQ cancellations, vs 5-12% for AA, OO, UA, WN (chi-square p < 0.001) |
| 7 | Busy hubs add delay | Nine high-volume origin airports: 5.0 min mean delay vs 3.5 min elsewhere |
| 8 | Long flights recover time | Long-haul (>1,500 mi) mean arrival delay 0.7 min vs 4.8 for short-haul, and cancellation rate 0.6% vs 2.2% |

Sprint 2 findings that support these: late aircraft delay is the leading cause of delay minutes in essentially every month, time of day is the dominant effect with Monday evening the worst slot, and Saturday's advantage holds across time blocks.

## 3. Airline Performance

Airline Performance Score = on-time rate × (1 − cancellation rate) × 100.

| Tier | Airlines (score) |
|---|---|
| Strong | HA 89.1, AS 86.5, DL 86.5 |
| Middle | AA 81.8, OO 81.4, US 80.7, WN 80.6, UA 79.2, VX 78.3, EV 78.1, B6 78.0 |
| Weak | F9 75.1, MQ 73.8, NK 68.3 |

## 4. Recommendations

**Improve on-time performance and reduce delays**
- Focus on departure punctuality: turnaround, boarding and ground-handling procedures. Since delay carries from one flight into the next, fixing departures improves arrivals.
- Prioritize late-aircraft and airline-controlled causes (72% of delay minutes) over weather response (5%).

**Optimize scheduling**
- Add buffer time and spare aircraft to 16:00-21:59 departures, and avoid tight aircraft rotations in the evening.
- Use the long-flight recovery pattern: schedule realistic block times so short-haul evening flights are not squeezed.

**Optimize airport operations**
- Add gate and ground-crew capacity at the nine high-volume hubs during evening hours.
- Review ORD air-traffic-slot management and connecting-flight buffers for MQ and EV (ORD has more air-system cancellations than the other nine top airports combined, per Sprint 2).

**Carrier-specific actions**
- Run a punctuality review for NK, F9, MQ and EV; use DL and AS practices as internal benchmarks.
- Investigate National Air System-related cancellations at EV and MQ; prepare weather contingency plans for AA, OO and WN, where weather dominates cancellations.

**Reduce cancellations and improve passenger satisfaction**
- Use the DELAY_RISK index (evening + busy hub + below-median airline) for early warnings and proactive rebooking. High-risk flights are delayed 23.4% of the time vs 12.7% for Low-risk flights.

## 5. Expected Impact and Limitations

- Moving the evening peak toward the daytime delay level (mean 1.7 min) is the single largest lever identified.
- Findings are from a 50,000-flight sample; small carriers (NK, VX, F9, HA) have wide confidence intervals and should be interpreted cautiously.
- Delay-cause columns are populated only for flights delayed 15+ minutes, so cause shares describe major delays, not all lateness.
- About 8.5% of airport codes are numeric DOT IDs; only the nine busiest hubs' IDs were mapped, so other airport-level findings exclude or under-represent those rows.
- DELAY_RISK is a descriptive screening index, not a predictive model, and the airline score is built from the same data it is evaluated on.
