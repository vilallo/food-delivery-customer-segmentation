# Food Delivery Customer Segmentation

![Python](https://img.shields.io/badge/Python-3.10%2B-3776AB?logo=python&logoColor=white)
![scikit-learn](https://img.shields.io/badge/scikit--learn-F7931E?logo=scikitlearn&logoColor=white)
![Jupyter](https://img.shields.io/badge/Jupyter-F37626?logo=jupyter&logoColor=white)

Multi-perspective customer segmentation for **ABCDEats**, a fictional food delivery platform. About 31,700 customers are grouped into **four behavioural segments** by what they order, when they order and how much they are worth. One-time buyers are analysed separately so they do not distort the core segmentation.

> Academic project for the Data Mining course at NOVA IMS (2024/2025).

## Problem

ABCDEats had three months of order data from customers in three cities but no clear view of who those customers were. Marketing was generic, and the company could not tell its loyal, high-value customers apart from occasional ones. The goals were to:
- Find distinct customer groups based on ordering behaviour, cuisine preferences and spending.
- Give marketing a concrete profile for each group so campaigns can be targeted.
- Understand one-time customers on their own and find levers to bring them back.

## Approach

The work follows **CRISP-DM** and is split into six notebooks, run in order.

**1. Data understanding** · [`01_eda.ipynb`](01_eda.ipynb)
- Explored 50+ features: demographics, region, order day and hour, spend per cuisine, payment method and promotions.
- Found missing values in `customer_age`, `first_order` and `HR_0`.
- Found that **22% of customers (6,908) ordered only once**. These customers were set aside for their own analysis.

**2. Data preparation, part 1** · [`02_data_preparation_part1.ipynb`](02_data_preparation_part1.ipynb)
- Imputed `customer_age` with the mean and set a missing `first_order` to 0 (first and last order on the same day). Corrected `HR_0` using the gap between hourly and daily order counts.
- Checked that day-of-week and hour-of-day counts were consistent with each other.
- Grouped the cuisines into Asian, Western, Healthy, Street Food/Snacks, Desserts & Beverages and Other, and the hours into midday, evening and late-night buckets.

**3. One-time customers** · [`03_one_time_customers.ipynb`](03_one_time_customers.ipynb)
- Profiled the 6,908 single-order customers: an average order of **€14.18**, with **Asian** cuisine by far the most ordered.
- More than **35%** used a delivery promotion, which makes promotions the main lever for reactivation campaigns.

**4. Data preparation, part 2** · [`04_data_preparation_part2.ipynb`](04_data_preparation_part2.ipynb)
- Treated outliers with the IQR rule on the remaining 24,829 repeat customers.
- Built value features: `total_spent`, `avg_spent_per_order`, `customer_lifetime` and `avg_days_between_orders`.
- Reduced redundant features and scaled with `RobustScaler`, which handles the heavily skewed spend distributions.

**5. Modelling** · [`05_modelling.ipynb`](05_modelling.ipynb)
- Clustered two separate perspectives:
  - **Preferences:** spend per cuisine, food diversity and time of day.
  - **Value:** total spend, order frequency, vendor diversity and customer lifetime.
- Tested **K-Means**, **Mean-Shift** and **hierarchical clustering (Ward)** on each perspective.

**6. Evaluation** · [`06_evaluation.ipynb`](06_evaluation.ipynb)
- Merged the two perspectives in two steps: first by business logic, then with hierarchical clustering on the cluster centroids.
- Checked feature importance with R² and a decision tree, and visualised the final clusters with **t-SNE**.
- Profiled each segment by cuisine, time of day, region and spend.

## Results

Algorithm comparison per perspective:

| Algorithm | Perspective | R² | Calinski-Harabasz | Silhouette |
|-----------|-------------|----|-------------------|------------|
| K-Means | Preferences | 0.292 | 5,846 | 0.388 |
| K-Means | Value | 0.479 | 9,737 | 0.327 |
| Mean-Shift | Preferences | 0.364 | 4,046 | 0.386 |
| Mean-Shift | Value | 0.056 | 1,263 | 0.474 |
| **Hierarchical (Ward)** | **Preferences** | **0.454** | **5,867** | **0.267** |
| **Hierarchical (Ward)** | **Value** | **0.526** | **7,826** | **0.225** |

Ward hierarchical clustering was chosen for both perspectives. It had the highest R² on each and the best overall balance of metrics. Mean-Shift scored a high silhouette on the value perspective, but it explained almost none of the variance (R² 0.056).

The merged solution has four segments:

| Segment | Profile | Marketing focus |
|---------|---------|-----------------|
| **Diverse Big Spenders** | Most orders and highest total spend. Western and Asian food, mostly in the evening. Widest range of vendors and longest customer lifetime. | Retain and reward: loyalty tiers, early access to new restaurants |
| **Healthy Niche** | The only segment that orders Healthy cuisine. Second-highest spend, orders spread evenly across the day. | Partner with healthy restaurants, curated healthy bundles |
| **Late-Night Occasionals** | Lowest spend, late-night orders only, little variety. Long gaps between orders and shortest lifetime. | Re-engagement with late-night promotions and free delivery |
| **Street Food Regulars** | Specialise in street food and snacks, with the highest spend per order. Late-night weekday orders, concentrated in region 8000. | Local street-food deals and weekday combos in their region |

## Repository structure

```
food-delivery-customer-segmentation/
├── 01_eda.ipynb                        # Data understanding and exploration
├── 02_data_preparation_part1.ipynb     # Missing values, consistency checks, first features
├── 03_one_time_customers.ipynb         # Analysis of single-order customers
├── 04_data_preparation_part2.ipynb     # Outliers, value features, scaling
├── 05_modelling.ipynb                  # K-Means, Mean-Shift and hierarchical clustering
├── 06_evaluation.ipynb                 # Merging perspectives, t-SNE, segment profiles
├── requirements.txt
└── README.md
```

> **Note:** The dataset (`DM2425_ABCDEats_DATASET.csv`) was provided privately for the course and is not included in this repository.

## Setup

```bash
pip install -r requirements.txt
```

Place `DM2425_ABCDEats_DATASET.csv` in a `Data/` folder one level above the notebooks (`../Data/`), then run the notebooks in order (01 to 06). Notebooks 02 and 04 write the intermediate files (`food_df_dataset.csv`, `food_df_preprocessed.csv`) that the later notebooks read.

## Tech stack

Python · pandas · NumPy · scikit-learn (K-Means, Mean-Shift, Agglomerative Clustering, t-SNE) · SciPy · Matplotlib · Seaborn

## Author

**Vinícius Pinto** · [GitHub](https://github.com/vilallo)
