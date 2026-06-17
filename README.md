# A/B Test Analysis — Landing Page Conversion

A end-to-end statistical analysis of a website A/B test, determining whether a new landing page design significantly improved user conversion rates.
---

## Business Context

An e-commerce website ran an A/B test to evaluate whether a redesigned landing page would increase the proportion of users who convert (make a purchase or complete a sign-up). Users were randomly assigned to either the existing page (control) or the new design (treatment) over a period of several weeks in January 2017.

The business question: **should the new landing page be shipped to all users?**

---

## Hypothesis

| | Statement |
|---|---|
| **H₀ (null)** | The new landing page has no effect on conversion rate &nbsp;*(p_treatment = p_control)* |
| **H₁ (alternative)** | The new landing page increases conversion rate &nbsp;*(p_treatment > p_control)* |
| **Significance level** | α = 0.05 |
| **Test type** | One-tailed two-proportion z-test + chi-square cross-check |

---

## Dataset

**Source:** [Kaggle — A/B Testing Dataset](https://www.kaggle.com/datasets/zhangluyuan/ab-testing)  
**File:** `ab_data.csv` (not included in this repo — download from Kaggle)  
**Size:** ~294,000 rows  

| Column | Description |
|---|---|
| `user_id` | Unique user identifier |
| `timestamp` | Time of visit |
| `group` | `control` or `treatment` |
| `landing_page` | `old_page` or `new_page` |
| `converted` | `1` = converted, `0` = did not convert |

Place `ab_data.csv` in the project root before running the notebook.

---

## Methodology

### Data Cleaning
- Removed **mismatched assignments** — users assigned to treatment but shown the old page (and vice versa), as these received a different treatment than intended
- Removed **duplicate user IDs** — kept only the first visit per user, sorted by timestamp
- Performed **sample ratio mismatch check** — confirmed groups were approximately 50/50

### Statistical Tests

**Two-proportion z-test** (primary)
- Appropriate for binary outcomes (converted: 0/1) at large sample sizes
- One-tailed: testing whether treatment is *greater than* control
- Implemented via `statsmodels.stats.proportion.proportions_ztest`

**Chi-square test** (cross-check)
- Tests independence between group assignment and conversion
- Two-tailed — detects any association regardless of direction
- Used to validate z-test conclusion

**Confidence intervals**
- 95% Wilson confidence intervals for each group's true conversion rate
- Wilson method preferred over normal approximation for better accuracy

---

## Results

| Metric | Value |
|---|---|
| Control conversion rate | 12.04% |
| Treatment conversion rate | 11.88% |
| Uplift | -1.31% |
| Z-statistic | -1.3109 |
| Z-test p-value | 0.9050 |
| Chi-square p-value | 0.1899 |
| Significance level (α) | 0.05 |
| **Decision** | **Fail to reject H₀** |

> p = 0.9050 > α = 0.05 — the result is not statistically significant.

### Confidence Intervals (95%)

| Group | Lower | Upper |
|---|---|---|
| Control | 11.87% | 12.21% |
| Treatment | 11.72% | 12.05% |

The confidence intervals overlap substantially, consistent with no meaningful difference between the two pages.

---

## Business Recommendation

The new landing page showed no statistically significant improvement in conversion rate (uplift: -1.31%, p = 0.9050). The observed difference is well within the range of random variation and cannot be attributed to the page change with any confidence.

**Recommendation: do not ship the new landing page.**

Next steps:
- Commission further UX research to identify friction points on the existing page
- Design a new variant with more substantial, hypothesis-driven changes
- Consider segmenting future tests by device type or traffic source to uncover subgroup effects

---

## Dashboard

**[View Live Dashboard →](https://datastudio.google.com/reporting/0d2a58ad-e17d-4ef3-8087-51f3e64d79f3)**

Built in Looker Studio. Visuals include:

- KPI scorecards — conversion rate per group, uplift %, p-value
- Bar chart — conversion rate by group with 95% CI reference table
- Time series — daily conversion rate per group (novelty effect check)
- Decision text — plain-English business recommendation

---

## Project Structure

```
abtest/
├── ab_test_analysis.ipynb   ← main analysis notebook
├── requirements.txt         ← Python dependencies
├── .gitignore
└── README.md
```

**Generated outputs** (gitignored — reproduced by running the notebook):
```
output_summary.csv           ← conversion rates + CI per group
output_stats.csv             ← z-test, chi-square, uplift results
output_daily.csv             ← daily conversion rate time series
output_clean.csv             ← full cleaned dataset
plot_daily_conversion.png    ← trend line chart export
```

---

## How to Run

```bash
# 1. Clone the repo
git clone https://github.com/YOUR_USERNAME/ab-test-analysis.git
cd ab-test-analysis

# 2. Install dependencies
pip install -r requirements.txt

# 3. Download ab_data.csv from Kaggle and place it in the project root

# 4. Open the notebook
jupyter notebook ab_test_analysis.ipynb

# 5. Run all cells top to bottom
```

---

## Tools

| Tool | Purpose |
|---|---|
| Python | Data cleaning, statistical testing, exports |
| pandas | Data manipulation |
| statsmodels | Two-proportion z-test, confidence intervals |
| scipy | Chi-square test |
| matplotlib / seaborn | Python-side charts |
| DuckDB | SQL-based exploratory analysis |
| Looker Studio | Interactive dashboard |
| GitHub | Version control and project publishing |

---

