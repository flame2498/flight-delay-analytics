# ✈️ Flight Delay Analytics using Python

An end-to-end, sprint-based data analytics project that identifies the main factors behind flight delays and cancellations and turns them into business recommendations for an airline.

**Course:** Data Analytics using Python | **Domain:** Aviation | **Dataset:** 50,000 flight records (source: Kaggle)

## 🛠️ Tech Stack
Python · NumPy · Pandas · Matplotlib · Seaborn · SciPy

## 📂 Repository Structure

```
├── sprint1.ipynb                       # Data understanding, cleaning, outliers, validation
├── sprint2.ipynb                       # EDA: univariate, bivariate, multivariate, groupby, pivot, crosstab, correlation
├── Sprint2_Visualizations.ipynb        # Visualizations and storytelling
├── Sprint3_Statistical_Analysis.ipynb  # Statistics, hypothesis tests, confidence intervals, feature engineering
├── Flight_Delays_50K.csv               # Raw dataset (not modified)
├── Flight_Delays_50K_cleaned.csv       # Cleaned dataset (output of Sprint 1)
├── Flight_Delays_50K_featured.csv      # Cleaned dataset + engineered features (output of Sprint 3)
└── reports/
    ├── Business_Insights_Document.docx
    ├── Summary_of_Findings.docx
    ├── Group_Based_Analysis_Report.docx
    ├── Pivot_Table_Crosstab_Report.docx
    ├── Statistical_Analysis_Report.md
    ├── Business_Insights_Report.md
    └── Sprint3_Reports.md              # Hypothesis testing report, feature documentation, final recommendations
```

## 🚀 Project Phases

| Sprint | Focus | Main outputs |
|---|---|---|
| 1 | Data understanding and preparation | Cleaned dataset, missing-value, duplicate and outlier handling, validation |
| 2 | Exploratory data analysis and visualization | EDA notebook, visualizations, group-based and pivot/crosstab reports, business insights |
| 3 | Advanced statistics and feature engineering | Hypothesis tests, confidence intervals, six engineered features, final recommendations |

## 🔑 Key Findings

- The mean arrival delay is 4.0 minutes, but the median is -5.0 minutes: most flights arrive early, and the problem is a tail of severe delays (5.3% of flights are over 60 minutes late).
- Departure and arrival delay correlate at r = 0.94, so delays are created on the ground rather than recovered in the air.
- Late-aircraft and airline-controlled causes drive most delay minutes; weather is about 5% of delay minutes but the leading cause of cancellations (54.4%).
- Evening departures (16:00-21:59) average 9.1 min of delay vs 1.7 min otherwise.
- Airlines differ significantly: NK averages 14.5 min vs 3.8 min for the rest (Welch t-test and Mann-Whitney U, p < 0.001).
- Cancellation causes depend on airline (chi-square p < 0.001); EV and MQ are the most affected by air-system cancellations, linked to ORD.

## 🧪 Statistical Methods
Covariance and correlation (Pearson and Spearman), Shapiro-Wilk normality test, one-sample and Welch T-tests, Mann-Whitney U, one-way ANOVA, Kruskal-Wallis, chi-square test of independence, and 95% confidence intervals.

## ⚙️ Engineered Features
`DELAY_CATEGORY`, `DISTANCE_CATEGORY`, `PEAK_HOUR`, `BUSY_AIRPORT`, `AIRLINE_PERFORMANCE_SCORE`, `DELAY_RISK`

## ▶️ How to Run

```bash
pip install numpy pandas matplotlib seaborn scipy jupyter
jupyter notebook
```

Open the notebooks in order (Sprint 1 → Sprint 2 → Sprint 3). Keep the notebooks and CSV files in the same folder, since the notebooks load the data using relative paths.

## ⚠️ Limitations
- About 8.5% of airport codes are numeric DOT IDs; only the nine busiest hubs were mapped to IATA codes.
- Delay-cause columns are filled only for flights delayed 15+ minutes.
- The findings are descriptive; the risk index is a screening rule, not a predictive model.
