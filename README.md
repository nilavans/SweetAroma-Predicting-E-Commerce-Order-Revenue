# SweetAroma: Predicting E-Commerce Order Revenue

A regression analysis of 10,000 orders from SweetAroma, a UK-based high-end perfume retailer. The project answers two business questions: **what drives order revenue**, and **can we predict revenue for new orders?**

Built for the *Business Analytics and Decision Science* module at the University of Leeds (Business School).

---

## Business Questions

| # | Type | Question |
|---|------|----------|
| 1 | Explanatory | Which factors influence how much customers spend: advertising channel, voucher usage, past purchasing behaviour, or time on the website? |
| 2 | Predictive | Can we build a model that accurately predicts order revenue, so high-value customers can be targeted? |

## Key Findings

- **Time on website is the strongest driver of revenue.** Each extra second adds about £0.37 to the order (r = 0.666 with revenue, p < 0.001).
- **Past spend has a small but significant effect** (about £0.02 per £1 of past spend, p = 0.012).
- **Advertising channel and voucher usage were not significant.** Mean revenue is nearly identical across channels (£55.3–£55.6).
- **Linear Regression beat the more complex models**, with a test RMSE of **£10.23** and a test/train ratio of 1.04. Random Forest overfitted (ratio 1.70).
- The model explains about **41% of revenue variance** on unseen data (test R² = 0.41).

## Results

### Explanatory model (OLS)

R² = 0.447, F = 1076.5 (p < 0.001), n = 8,000 training rows.

| Variable | Coefficient | p-value | Significant? |
|----------|------------:|--------:|:------------:|
| Intercept | 10.28 | < 0.001 | *** |
| `time_web` | 0.369 | < 0.001 | *** |
| `past_spend` | 0.021 | 0.012 | * |
| `voucher` | -0.448 | 0.147 | No |
| `ad_Free_SEO` | -0.008 | 0.980 | No |
| `ad_Online_Ad` | -0.165 | 0.594 | No |
| `ad_Paid_Search` | -0.032 | 0.914 | No |

### Model comparison (predictive)

| Model | Train RMSE | Test RMSE | CV RMSE (5-fold) | Test R² | Overfitting? |
|-------|-----------:|----------:|-----------------:|--------:|:------------:|
| **Linear Regression** | £9.86 | **£10.23** | £9.87 | 0.410 | No |
| Random Forest | £6.54 | £11.12 | £10.85 | 0.302 | Yes |
| Gradient Boosting | £9.01 | £10.35 | £10.06 | 0.396 | No |

## Methodology

The notebook follows a CRISP-DM-style workflow:

1. **Data loading:** 10,000 orders, 6 raw columns.
2. **Data understanding:** summary statistics, missing-value analysis, correlation matrix, and EDA plots.
3. **Data preparation**
   - *Logical imputation:* if `number_past_order = 0`, then `past_spend = 0`, and vice versa (92 values).
   - *Remaining gaps:* median for numeric columns, mode for categorical ones. Revenue is deliberately not used for imputation, to avoid data leakage.
   - *Outliers:* flagged with the IQR rule but kept, because they are genuine high-value customers.
   - *Encoding:* `ad_channel` is one-hot encoded, with "No Advertisement" as the reference category.
   - *Multicollinearity:* `number_past_order` is dropped (r = 0.70 with `past_spend`).
4. **Modelling:** OLS regression written from scratch (standard errors, t-stats, p-values, F-statistic) for the explanatory question. Linear Regression, Random Forest and Gradient Boosting are compared for the predictive question on an 80/20 split with 5-fold CV.
5. **Evaluation:** OLS assumption checks (linearity, homoscedasticity, residual normality, VIF < 5) and predictive validation (overfitting ratio, CV stability, residual analysis, prediction range).
6. **Scoring:** the final model scores 20 new customers and saves the predictions to CSV.

## Repository Structure

```
.
├── sweet_aroma.ipynb              # Full analysis pipeline
└── README.md
```

## Dataset

| Column | Description |
|--------|-------------|
| `number_past_order` | Number of previous orders by the customer |
| `time_web` | Time spent on the website in the session (seconds) |
| `voucher` | Whether a voucher was used (0/1) |
| `past_spend` | Customer's previous spend (£) |
| `ad_channel` | 1 = No Advertisement, 2 = Paid Search, 3 = Free SEO, 4 = Online Ad |
| `revenue` | **Target:** order revenue (£) |

> The data files are not included in this repository. Place them in a `data/` folder using the filenames above.

## Getting Started

**Requirements:** Python 3.12 with `pandas`, `numpy`, `scipy`, `scikit-learn`, `matplotlib`, `seaborn` and `jupyter`.

```bash
git clone https://github.com/nilavans/sweetaroma-predicting-e-commerce-order-revenue.git
cd sweetaroma-predicting-e-commerce-order-revenue
pip install pandas numpy scipy scikit-learn matplotlib seaborn jupyter
jupyter notebook sweet_aroma.ipynb
```

Run all cells. The final cell calls `main()`, which runs the whole pipeline and writes both output CSVs to `data/`.

## Recommendations

1. **Prioritise website engagement.** Invest in features that extend session time, such as rich product content, personalised recommendations, and fragrance finders.
2. **Optimise advertising for efficiency.** All channels attract similar-value customers, so favour the lowest cost-per-acquisition channel.
3. **Deploy predictive targeting.** Use predicted revenue to prioritise personalised offers and retargeting.

## Limitations

- Based on a single month (July 2025), so seasonality is not captured.
- About 59% of revenue variance is unexplained. Product category, demographics, and promotions are not in the data.
- Single imputation was used, so uncertainty from missing values is not propagated.
- Dropping `number_past_order` was not tested against keeping it.

## Tech Stack

Python · pandas · NumPy · SciPy · scikit-learn · Matplotlib · Seaborn · Jupyter

## Author

**Nilavan** · University of Leeds
