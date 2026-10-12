# Marketing Campaign Performance: Ad vs PSA (A/B Test Analysis)

**Tools:** Power BI · Power Query · DAX · A/B Testing

An end-to-end analysis of a marketing A/B test in Power BI. The goal is to measure whether showing an advertisement increases purchases compared with a control group, and to understand when and how much users are exposed to ads.

---

## Project Overview

Users were split into two groups:

- **ad (treatment):** users who saw the advertisement.
- **psa (control):** users who saw a public service announcement instead of the ad.

The dataset covers **588,101 users**. Because the PSA group did not see the ad, it shows what purchasing looks like without the campaign. Comparing the two groups isolates the effect of the ad.

## Business Questions

1. Did the ad increase the purchase rate compared with the PSA control group?
2. By how much (absolute gap and relative lift)?
3. How are users and ad impressions split between the two groups?
4. On which days and hours were users most exposed to ads?

## Dataset

Table: `marketing_AB` (588,101 rows, one row per user).

| Column | Description |
|---|---|
| `user id` | Unique user identifier |
| `test group` | `ad` (saw the ad) or `psa` (control) |
| `Purchased` | True if the user bought the product, otherwise False (originally `converted`) |
| `Impressions` | Number of ads the user saw (originally `total ads`) |
| `Peak Impression Day` | Day on which the user saw the most ads (originally `most ads day`) |
| `Peak Ad Hour` | Hour of day (0-23) in which the user saw the most ads (originally `most ads hour`) |

Source: [https://www.kaggle.com/datasets/faviovaz/marketing-ab-testing]

## Data Preparation

- Renamed columns to clear, business-friendly names.
- Verified row count and group sizes (about 96% ad, 4% PSA).
- Added `Day Number` in Power Query so weekdays sort naturally (Saturday = 1 ... Friday = 7) instead of alphabetically.
- Added `Peak Impression Hour`, a text column in `HH:mm` format, for a readable hour axis.
- Set summarization to "Don't summarize" for identifier and category columns.

## Key DAX Measures

```DAX
Total Users = DISTINCTCOUNT(marketing_AB[user id])

AD Group Users =
CALCULATE([Total Users], marketing_AB[test group] = "ad")

Psa Group Users =
CALCULATE([Total Users], marketing_AB[test group] = "psa")

Total Purchasers =
CALCULATE([Total Users], marketing_AB[Purchased] = TRUE())

Purchase Rate = DIVIDE([Total Purchasers], [Total Users])

Ad Purchase Rate = DIVIDE([Total Ad Purchasers], [AD Group Users])

PSA Purchase Rate = DIVIDE([Total Psa Purchasers], [Psa Group Users])

Purchase Rate Difference = [Ad Purchase Rate] - [PSA Purchase Rate]

Purchase Rate Lift =
DIVIDE([Ad Purchase Rate] - [PSA Purchase Rate], [PSA Purchase Rate])```

**Why rates instead of counts?** The two groups differ a lot in size, so comparing raw purchase counts would be unfair. Each group's purchase rate is calculated on its own users, then the rates are compared.

## Dashboard

![Dashboard](imagesdashboard)

The report includes:

- **KPI cards:** total users, total purchasers, total impressions, users per group, overall purchase rate, and purchase rate lift.
- **Group comparison:** purchase rate (ad vs PSA), purchase vs non-purchase split, impressions by group, and group share.
- **Exposure timing:** impressions by day of week and by hour of day.
- **Slicers:** test group, day, and hour.

## Key Findings

- The experiment covers **588,101 users**: about **565K in the ad group (96%)** and **24K in the PSA group (4%)**, with roughly **15K purchasers** (overall purchase rate around 3%).
- The ad group purchased at a higher rate than the PSA control group, a **purchase rate lift of 43%** relative to the control.
  - Ad purchase rate: **[3%]** vs PSA purchase rate: **[2%]**.
- Total impressions are about **15M**, of which the ad group accounts for about 14.01M and the PSA group about 0.58M. This mainly reflects group size, not behavior.
- Ad exposure peaks on **Friday** (about 2.46M impressions) and is lowest on **Tuesday** (about 1.85M).
- Exposure is highest around **midday to early afternoon** and lowest in the early morning hours.

## Recommendations

- The ad group purchased at a higher rate than the control group, so the ad appears effective. Weigh the lift against campaign cost before scaling.
- Concentrate ad delivery around the high-exposure days and hours, then test whether purchase rate (not just exposure) is also higher at those times.
- Run a follow-up test on exposure frequency to find the point where extra impressions stop adding purchases.

## Limitations

- The PSA group is small (about 4%), so rates for narrow slices (a single hour, for example) are noisy. Focus on overall trends.
- `Peak Impression Day` and `Peak Ad Hour` describe when a user saw the most ads, not when the purchase happened.
- The relationship between number of impressions and purchasing is correlation. Users who see more ads may simply be more active.
- The data has no cost or revenue fields, so ROI cannot be calculated.

## Next Steps

- Add purchase rate by day, hour, and exposure band (impression ranges).
- Test the statistical significance of the lift and add a confidence interval.
- Add cost and revenue data to estimate campaign ROI.

## Repository Structure

```
├── README.md
├── marketing_ab_dashboard.pbix
├── data/
│   └── marketing_AB.csv
└── images/
    └── dashboard.png
```

## How to Use

1. Download the `.pbix` file.
2. Open it in Power BI Desktop.
3. If prompted, point the data source to `data/marketing_AB.csv`.

## Author

**[Nagham Hsham Hamed**
Data Analyst · Excel · SQL · Power BI
[LinkedIn](https://www.linkedin.com/in/nagham-hesham1357/) · [GitHub](https://github.com/Nagham-ai)
