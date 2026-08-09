# Olist E-Commerce: Delivery Performance & Seller Segmentation

Analysis of the Olist Brazilian e-commerce dataset to identify what drives late deliveries,
quantify its effect on customer satisfaction, and segment sellers by performance so the
business knows who to support and who to invest in for growth.

## Key Findings

- **~8% of all orders** arrive later than their estimated delivery date.
- **Carrier transit time is the strongest driver of delay** (Spearman ρ = 0.235), ahead of
  seller-customer distance (ρ ≈ 0.18) and seller dispatch/processing time (ρ = 0.173).
  Package volume has a negligible effect (ρ = -0.02).
- Delivery delay has a statistically significant but weak negative relationship with review
  scores (ρ = -0.178) — delay hurts satisfaction, but isn't the dominant driver of it.
- Orders paid via installments (EMI) average **~65% higher order value** than single-payment
  orders.
- K-Means clustering (k=3, silhouette score 0.40) splits ~3,095 sellers into three segments:

  | Segment | Sellers | Avg. Revenue | Avg. Rating | Delivery Rate | Avg. Order Volume |
  |---|---|---|---|---|---|
  | Fundamentally Strong & Stable | 1,165 | $10,807 | 4.07 | 90% | 79.1 |
  | Strong Future-Growth | 1,598 | $543 | 4.34 | 95% | 4.4 |
  | Weak Sellers | 332 | $590 | 1.98 | 25% | 2.3 |

- Cross-validated the clustering against an independently-built composite KPI score: **97.1%**
  of the lowest-KPI-scoring sellers were also labeled "Weak Sellers" by the clustering,
  confirming the segmentation captures a real, robust pattern rather than noise.

## Recommendations

1. Prioritize carrier/logistics optimization on high-transit-time routes over package-size
   restrictions — transit time is the strongest measured lever on delay.
2. Target the "Weak Sellers" segment (332 sellers, 25% on-time delivery, 1.98 avg rating) with
   retention/support programs rather than growth investment.
3. Invest in the "Strong Future-Growth" segment (1,598 sellers, 95% delivery rate, 4.34 rating,
   but low volume) — logistics support and marketing placement, since they've already proven
   reliable and have room to scale.
4. Test whether expanding installment/EMI availability increases order value causally — this
   analysis shows correlation only.

**Limitation:** all correlations in this analysis are weak-to-moderate (ρ < 0.25). Delay and
satisfaction are multi-causal; no single driver tested here fully explains outcomes on its own.
Recommend pairing these findings with a controlled experiment before acting on them at scale.

## Methodology

- **SQL (SQLite)** for all aggregation and feature-building queries — chosen for auditability
  over chained pandas operations.
- **Spearman rank correlation** (not Pearson) to test delay drivers, since delay is not
  normally distributed and Spearman is robust to outliers/non-linearity.
- **K-Means clustering** on log-transformed revenue/volume + rating + delivery rate
  (log-transformed to prevent a handful of high-revenue sellers from dominating the distance
  metric). k selected via elbow method + silhouette score (k=3 won at 0.4048).
- **PCA** (2 components) used only for visualizing the clusters, not for the clustering itself.

## Repo Structure

```
.
├── Olist_analysis___seller_clustering.ipynb   # main analysis notebook
├── requirements.txt                            # Python dependencies
├── README.md
├── LICENSE
└── data/                                        # not committed — see below
```

## How to Run

1. Download the [Olist Brazilian E-Commerce dataset](https://www.kaggle.com/datasets/olistbr/brazilian-ecommerce)
   from Kaggle.
2. Place the 8 CSV files into a `data/` folder in the repo root.
3. Install dependencies:
   ```
   pip install -r requirements.txt
   ```
4. Open `Olist_analysis___seller_clustering.ipynb` and run all cells (Kernel → Restart & Run All).

Raw data isn't committed to this repo (large files, and Kaggle's terms don't permit
redistribution) — step 1–2 above regenerates it locally.

## Tech Stack

Python · pandas · SQLite · scikit-learn (KMeans, PCA, StandardScaler) · SciPy (Spearman
correlation) · seaborn/matplotlib

## Dataset

[Olist Brazilian E-Commerce Public Dataset](https://www.kaggle.com/datasets/olistbr/brazilian-ecommerce) —
~100k orders from 2016–2018 across multiple marketplaces in Brazil.
