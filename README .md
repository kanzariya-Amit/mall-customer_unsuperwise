# Mall Customer Segmentation

**Unsupervised Learning – PR 1**

Customer segmentation for a shopping mall using three clustering algorithms — **K-Means**, **Agglomerative Hierarchical Clustering**, and **DBSCAN** — with a side-by-side comparison of results and business recommendations for mall management.

---

## Table of Contents

- [Overview](#overview)
- [Dataset](#dataset)
- [Project Structure](#project-structure)
- [Requirements](#requirements)
- [Getting Started](#getting-started)
- [Methodology](#methodology)
- [Results](#results)
- [Business Insights](#business-insights)
- [Key Takeaways](#key-takeaways)

---

## Overview

The goal of this project is to group mall customers into meaningful segments based on their purchasing behaviour, so that marketing efforts can be targeted more effectively. The notebook walks through the full workflow:

1. Data loading and exploratory data analysis (EDA)
2. Feature scaling and feature selection
3. K-Means clustering (Elbow Method + Silhouette Score)
4. Agglomerative hierarchical clustering (Ward linkage + dendrogram)
5. DBSCAN clustering (4-NN distance plot + parameter grid search)
6. Algorithm comparison and business insights

## Dataset

**File:** `Mall_Customers.csv` (200 rows × 5 columns, no missing values, no duplicates)

| Column | Description |
|---|---|
| `CustomerID` | Unique customer identifier (dropped before modelling) |
| `Gender` | Male / Female (label-encoded: Female = 0, Male = 1) |
| `Age` | Customer age (18–70) |
| `Annual Income (k$)` | Annual income in thousands of dollars (renamed to `Annual_Income`) |
| `Spending Score (1-100)` | Score assigned by the mall based on spending behaviour (renamed to `Spending_Score`) |

> The dataset is not bundled in this repository. Place `Mall_Customers.csv` in the same folder as the notebook (a common source is the Kaggle "Mall Customer Segmentation Data" dataset).

## Project Structure

```
.
├── UL_PR1_Mall_Customer_Segmentation.ipynb   # Main notebook
├── Mall_Customers.csv                        # Dataset (add this file)
└── README.md
```

## Requirements

- Python 3.9+ (developed with Python 3.13)
- numpy
- pandas
- matplotlib
- seaborn
- scikit-learn
- scipy
- jupyter / jupyterlab

Install everything with:

```bash
pip install numpy pandas matplotlib seaborn scikit-learn scipy jupyter
```

## Getting Started

```bash
# 1. Clone the repository
git clone <your-repo-url>
cd <your-repo-folder>

# 2. Install dependencies
pip install numpy pandas matplotlib seaborn scikit-learn scipy jupyter

# 3. Add Mall_Customers.csv to the project folder

# 4. Launch the notebook
jupyter notebook UL_PR1_Mall_Customer_Segmentation.ipynb
```

Run all cells from top to bottom (`Kernel → Restart & Run All`).

## Methodology

### Task 1 — Data Loading & EDA
- Inspected shape, data types, summary statistics, missing values, and duplicates.
- Dropped `CustomerID`, renamed columns, and encoded `Gender`.
- Visualised distributions (histograms + KDE), a pairplot, and a correlation heatmap.

### Task 2 — Feature Scaling & Selection
- Applied `StandardScaler` to `Age`, `Annual_Income`, and `Spending_Score` (distance-based algorithms are sensitive to feature scale).
- Selected **Annual Income** and **Spending Score** as the two features for primary clustering, which allows direct 2D visualisation without PCA.

### Task 3 — K-Means
- Elbow Method for k = 1–10 and Silhouette Score for k = 2–10.
- Best k selected from the Silhouette Score: **k = 5** (silhouette ≈ 0.555).
- Visualised clusters with centroids and built a mean-value profile per cluster.

### Task 4 — Agglomerative Hierarchical Clustering
- Built a Ward-linkage dendrogram.
- Fitted a 5-cluster model to compare directly with K-Means.

### Task 5 — DBSCAN
- Used a 4-NN distance plot to guide the choice of `eps`.
- Grid search over `eps` ∈ {0.2, 0.3, 0.4, 0.5, 0.6} and `min_samples` ∈ {3, 4, 5, 6}, recording cluster count, noise points, and silhouette score.
- Selected parameters: **eps = 0.2, min_samples = 4**.

### Task 6 — Comparison & Business Insights
- Three-panel scatter comparison of all algorithms.
- Metrics: Silhouette Score, Davies-Bouldin Index, Calinski-Harabasz Index (DBSCAN noise points excluded).

## Results

| Algorithm | Clusters | Silhouette ↑ | Davies-Bouldin ↓ | Calinski-Harabasz ↑ |
|---|:---:|:---:|:---:|:---:|
| K-Means | 5 | 0.5547 | 0.5722 | **248.65** |
| Hierarchical (Ward) | 5 | 0.5538 | 0.5779 | 244.41 |
| DBSCAN | 5 | **0.6173** | **0.5403** | 229.52 |

**Cluster sizes**

| Algorithm | Cluster sizes |
|---|---|
| K-Means | 81 / 39 / 22 / 35 / 23 |
| Hierarchical | 32 / 39 / 85 / 21 / 23 |
| DBSCAN | 73 noise points; clusters of 7 / 79 / 21 / 14 / 6 |

> **Note on DBSCAN:** its higher silhouette and lower Davies-Bouldin scores are computed *after excluding* the 73 noise points (36.5% of customers), so they are not directly comparable with K-Means and Hierarchical, which assign every customer to a cluster.

## Business Insights

The five customer segments found in the Income vs. Spending Score space, with suggested marketing actions:

| Segment | Profile | Suggested action |
|---|---|---|
| High Income + High Spending | Most valuable customers | Loyalty rewards, premium offers |
| High Income + Low Spending | Untapped purchasing potential | Personalised offers and outreach |
| Low Income + High Spending | Price-sensitive but active | Targeted discounts, budget promotions |
| Low Income + Low Spending | Low current spend | Basic promotions, awareness campaigns |
| Middle Income / Middle Spending | Regular customers | General loyalty and seasonal campaigns |

Segment names should be matched to the actual mean values in the K-Means cluster profile table generated by the notebook.

## Key Takeaways

- **K-Means** is the easiest to explain to stakeholders and gives the best Calinski-Harabasz score; it is a strong default for straightforward segmentation.
- **Hierarchical clustering** produces nearly identical segments and adds the dendrogram, which shows how groups merge.
- **DBSCAN** requires no preset number of clusters and flags outliers, but on this dataset it labels a large share of customers as noise, making it less practical as the sole segmentation method.
- The broad customer groups are stable across all three algorithms; differences appear mainly for customers near cluster borders.

## Possible Extensions

- Include `Age` and `Gender` in clustering (3D/4D) and visualise with PCA.
- Try Gaussian Mixture Models or K-Medoids.
- Assess cluster stability across random seeds and bootstrap samples.

---

*Made for an Unsupervised Learning course project.*
