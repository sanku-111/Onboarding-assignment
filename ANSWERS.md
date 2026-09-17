# Onboarding Experiment Investigation

## Executive Summary

The naive overall comparison shows a **6.61 percentage-point** treatment lift, but the segment-level analysis shows that this is not a uniform improvement across users.

The mix-adjusted lift is only **1.63 percentage points**, substantially below the naive **6.61 pp**. The difference is explained by a strong imbalance in treatment/control composition for the `organic` and `paid_search` segments.

The segment with the clearest positive evidence is **`app_store`**: treatment conversion is **20.00%** versus **8.76%** for control, a lift of **11.24 pp**, with 960 treatment users and 925 control users. The supplementary 95% confidence interval for the difference is approximately **8.13 to 14.36 pp**.

## Investigation Process

- Loaded `experiment_results.csv` with Pandas and inspected its dimensions and schema.
- Checked missing values; no missing values were found.
- Checked duplicate `user_id` values; no duplicates were found.
- Verified that the data contains the expected five segments, two variants, and binary conversion outcomes.
- Calculated overall treatment and control conversion rates and the naive percentage-point lift.
- Calculated treatment/control sample sizes and conversion rates separately for every segment.
- Investigated whether the largest observed segment lift was supported by enough observations; this prevented choosing a segment based on lift alone.
- Calculated the mix-adjusted lift by weighting each segment's treatment-control lift by its share of the total user population, exactly as requested.
- Checked treatment/control allocation percentages within every segment.
- Performed an additional two-proportion uncertainty check to support interpretation of segment-level effects; this was supplementary to the calculations explicitly requested.

## Q1. Overall (Naive) Difference

Control users: **7,136**

Treatment users: **6,864**

Control conversions: **1,414**

Treatment conversions: **1,814**

Control conversion rate: **19.8150%**

Treatment conversion rate: **26.4277%**

Naive lift:

**26.4277% - 19.8150% = 6.61 percentage points**

Therefore, the naive overall treatment lift is **6.61 percentage points**.

## Q2. Segment-Level Analysis

| Segment     |   Control N |   Control CR (%) |   Treatment N |   Treatment CR (%) |   Lift (pp) |
|:------------|------------:|-----------------:|--------------:|-------------------:|------------:|
| app_store   |         925 |             8.76 |           960 |              20.00 |       11.24 |
| influencer  |         119 |            23.53 |           131 |              16.79 |       -6.74 |
| organic     |        1298 |            35.29 |          2917 |              35.07 |       -0.21 |
| paid_search |        3353 |            15.18 |          1459 |              14.39 |       -0.79 |
| referral    |        1441 |            23.46 |          1397 |              26.27 |        2.81 |

### Segment with an impressive lift that should not be trusted

**None.**

There is no segment with both an unusually large positive lift and a small sample that would justify calling the positive result unreliable. `app_store` has the largest positive lift (**11.24 pp**) but also has **925 control** and **960 treatment** users, with near-even assignment. `influencer` has only **119 control** and **131 treatment** users, but its observed lift is actually negative (**-6.74 pp**), so it does not fit the requested "impressive positive but untrustworthy" pattern.

The important caution is that raw lift alone should not be used to choose a segment; sample size and assignment balance must also be considered.

## Q3. Mix-Adjusted Overall Lift

For each segment:

**segment lift = treatment conversion rate - control conversion rate**

The requested mix-adjusted calculation weights each segment's lift by its share of **all users**, not by the number of users assigned to treatment.

- `app_store`: 13.4643% population share × 11.2432 pp = 1.5138 pp
- `influencer`: 1.7857% population share × -6.7355 pp = -0.1203 pp
- `organic`: 30.1071% population share × -0.2148 pp = -0.0647 pp
- `paid_search`: 34.3714% population share × -0.7870 pp = -0.2705 pp
- `referral`: 20.2714% population share × 2.8146 pp = 0.5706 pp

Summing these contributions gives:

**Mix-adjusted overall lift = 1.63 percentage points**

The mix-adjusted result is different from the naive result because the actual treatment and control groups do not have the same segment composition. In particular, `organic` is much more represented in treatment, while `paid_search` is much more represented in control. Since these segments have different baseline conversion rates and their within-segment lifts are close to zero or slightly negative, the raw overall comparison is affected by the composition imbalance.

## Q4. Segment with a Real, Meaningful Positive Effect

**`app_store`**

Evidence:

- Control: **925 users**, conversion rate **8.76%**
- Treatment: **960 users**, conversion rate **20.00%**
- Observed lift: **+11.24 percentage points**
- Population share: **13.46%**
- Within-segment assignment: **49.07% control / 50.93% treatment**
- Supplementary two-proportion 95% CI: approximately **8.13 to 14.36 pp**
- Supplementary two-proportion test p-value: **< 0.0001**

This is stronger evidence than simply selecting the segment with the largest percentage lift: the `app_store` segment has a substantial sample, nearly balanced treatment/control assignment, and a large positive conversion difference. The supplementary uncertainty check also places the estimated lift clearly above zero.

## Q5. Treatment vs Control Assignment

| Segment     |   Control (%) |   Treatment (%) |
|:------------|--------------:|----------------:|
| app_store   |         49.07 |           50.93 |
| influencer  |         47.60 |           52.40 |
| organic     |         30.79 |           69.21 |
| paid_search |         69.68 |           30.32 |
| referral    |         50.78 |           49.22 |

The assignment is **not evenly balanced within every segment**.

The two most notable imbalances are:

- `organic`: **30.79% control / 69.21% treatment**
- `paid_search`: **69.68% control / 30.32% treatment**

By contrast, `app_store`, `referral`, and `influencer` are much closer to an even split.

This matters because the naive overall treatment/control comparison is therefore partly influenced by who is represented in each variant. The overall assignment across the entire dataset is close to even (**50.97% control / 49.03% treatment**), but the segment-level allocation is not.

## Additional Investigation / Dead End

A tempting approach would have been to simply select the segment with the largest treatment lift. I did not use that rule because it ignores sample size and treatment/control allocation. The `app_store` result is more convincing because its sample is large and its treatment/control split is close to 50/50, whereas the small `influencer` segment does not show a positive effect at all.

## Conclusion

The new onboarding flow has a **+6.61 pp naive overall lift**, but this substantially overstates the standardized segment-level effect. After weighting each segment according to its share of the total population, the lift is **+1.63 pp**.

The main reason for the difference is the strong segment-level assignment imbalance: treatment contains a much larger share of `organic` users, while control contains a much larger share of `paid_search` users. Within those two segments, treatment does not have a meaningful positive lift.

The clearest segment-level positive effect is **`app_store`**, where conversion increases from **8.76% to 20.00% (+11.24 pp)** with nearly balanced assignment and a substantial sample.
