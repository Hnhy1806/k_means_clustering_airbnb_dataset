# Airbnb Clustering Analysis

## Project Overview

This project applies **K-Means clustering** to an Airbnb dataset to group listings with similar characteristics. Instead of predicting a target variable, the goal is to discover natural patterns among Airbnb listings based on location, room type, price, review activity, host listing count, and yearly availability.

The analysis focuses on understanding how listings can be segmented into different groups, which can support business decisions such as market positioning, pricing strategy, and identifying listing behavior patterns.

## Dataset

The dataset contains **48,895 Airbnb listings** with 16 original columns, including listing information, host details, location, room type, price, reviews, and availability.

Key variables used for clustering:

- `neighbourhood_group`
- `latitude`
- `longitude`
- `room_type`
- `price`
- `minimum_nights`
- `number_of_reviews`
- `reviews_per_month`
- `calculated_host_listings_count`
- `availability_365`

These features were selected because they describe important listing characteristics such as where the listing is located (including precise geographic coordinates), what type of accommodation it offers, how expensive it is, how active it is in reviews, how many listings the host manages, and how often the listing is available.

## Data Cleaning and Preprocessing

The preprocessing process included the following steps:

### 1. Handling Missing Values

The column `reviews_per_month` contained missing values, which were filled with `0`. This is reasonable because listings with no recorded reviews likely have no monthly review activity.

Other missing columns such as `name`, `host_name`, and `last_review` were not used in the clustering model.

### 2. Feature Selection

Only clustering-relevant columns were selected. Identification columns and text-based columns were excluded because they do not directly help measure similarity between listings. `latitude` and `longitude` were included to capture geographic patterns more precisely than `neighbourhood_group` alone.

### 3. Outlier Removal

Extreme outliers were found in several numerical variables, especially:

- `price`
- `minimum_nights`
- `number_of_reviews`
- `reviews_per_month`
- `calculated_host_listings_count`

To improve clustering stability, values above the **99th percentile** were removed. After outlier removal, the dataset was reduced from **48,895 rows** to **46,460 rows**.

This step helped prevent extreme listings from dominating the distance calculations used in K-Means clustering.

### 4. Correlation Check

A correlation heatmap was used to check relationships between numerical features. The analysis showed that dimensionality reduction was not required because the dataset contained a small number of selected features and did not show severe multicollinearity.

### 5. Encoding Categorical Variables

Categorical variables were converted into numerical form using **OneHotEncoder** (with `drop='first'` to avoid redundant columns):

- `neighbourhood_group`
- `room_type`

This allowed categorical information to be included in the clustering algorithm.

### 6. Feature Scaling

Because K-Means is distance-based, all features were scaled using **MinMaxScaler**. Scaling was necessary to prevent large-value variables, such as price or availability, from having too much influence on the clustering result.

## Clustering Method

The project used **K-Means clustering**, implemented step by step instead of only relying on a built-in model.

The clustering process included:

1. Scaling the data
2. Initializing random centroids
3. Assigning each data point to the nearest centroid
4. Updating centroids based on cluster means
5. Repeating the process until the centroids stopped changing

This manual implementation helped demonstrate the logic behind how K-Means works.

### Handling Empty Clusters

The centroid-update step guards against empty clusters: if a random initialization produces a centroid that ends up with no points assigned to it, that cluster is reseeded with a random data point instead of being silently dropped. Without this guard, an empty cluster would shift the cluster indexing and corrupt the SSE calculation.

### Multiple Random Initializations

A single random initialization can cause K-Means to converge to a poor local minimum. To address this, each run of K-Means is repeated for **10 random initializations** (`n_init=10`), and the run with the lowest SSE is kept. This is applied consistently both when comparing values of `k` and when fitting the final model.

## Choosing the Number of Clusters

Two metrics were compared across `k = 2` to `k = 9`:

- **Elbow Method** — Sum of Squared Errors (SSE) for each value of `k`
- **Silhouette Score** — computed on a random sample of up to 5,000 points per `k` (full-dataset silhouette scoring is computationally expensive on ~46,000 rows)

Based on the combination of the elbow plot and the silhouette scores, the final number of clusters was selected as:

```text
k = 7
```

The final model was fit using 10 random initializations and converged with a final SSE of:

```text
13137.72
```

## Final Cluster Distribution

The final 7 clusters had the following number of listings:

| Cluster | Number of Listings |
|---|---:|
| 3 | 9,878 |
| 2 | 9,624 |
| 1 | 8,512 |
| 5 | 7,660 |
| 6 | 4,032 |
| 0 | 3,982 |
| 4 | 2,772 |

## Cluster Profiling

Each cluster was profiled using the **original, unscaled** feature values (rather than the scaled values used for clustering) to make the results interpretable in business terms.

| Cluster | Avg. Price ($) | Avg. Min. Nights | Avg. Reviews | Avg. Reviews/Month | Avg. Host Listings | Avg. Availability (days) | Count | Dominant Borough | Dominant Room Type |
|---|---:|---:|---:|---:|---:|---:|---:|---|---|
| 0 | 233.75 | 15.47 | 23.96 | 1.01 | 19.72 | 282.25 | 3,982 | Manhattan | Entire home/apt |
| 1 | 198.60 | 4.52 | 14.26 | 0.77 | 1.51 | 25.80 | 8,512 | Manhattan | Entire home/apt |
| 2 | 161.09 | 5.38 | 24.62 | 1.05 | 1.98 | 97.98 | 9,624 | Brooklyn | Entire home/apt |
| 3 | 72.13 | 4.68 | 18.69 | 0.91 | 2.55 | 98.56 | 9,878 | Brooklyn | Private room |
| 4 | 131.15 | 4.20 | 24.36 | 1.42 | 1.88 | 139.84 | 2,772 | Queens | Entire home/apt |
| 5 | 105.93 | 4.38 | 22.22 | 1.03 | 2.88 | 98.78 | 7,660 | Manhattan | Private room |
| 6 | 67.11 | 4.44 | 21.72 | 1.24 | 5.02 | 154.65 | 4,032 | Queens | Private room |

Based on these profiles, the clusters can be interpreted as:

- **Cluster 0 — Premium, professionally managed listings (Manhattan):** highest average price and host listing count, long minimum-stay requirements, and high availability, suggesting hosts who manage many properties as a business.
- **Cluster 1 — High-price, low-availability entire homes (Manhattan):** the second-highest price but the lowest availability and fewest reviews, consistent with owner-occupied or infrequently rented listings.
- **Cluster 2 — Mid-range entire homes (Brooklyn):** moderate price and availability, the second-largest segment.
- **Cluster 3 — Budget private rooms (Brooklyn):** the largest segment overall, with the lowest minimum-stay requirement among entire-home clusters and low price.
- **Cluster 4 — High-engagement entire homes (Queens):** smallest cluster, but with the highest reviews-per-month, suggesting frequently booked, actively reviewed listings.
- **Cluster 5 — Budget private rooms (Manhattan):** private rooms in Manhattan at a lower price point than the entire-home clusters in the same borough.
- **Cluster 6 — Budget private rooms with multi-listing hosts (Queens):** lowest average price, but a notably higher average host listing count than the other private-room clusters, suggesting smaller-scale hosts managing a handful of budget listings.

## Visualization

Principal Component Analysis (PCA) was used only for visualization. The high-dimensional clustering result was projected into two dimensions so the clusters and centroids could be displayed on a scatter plot.

PCA was not used as the main clustering input. It was mainly used to make the final clustering result easier to understand visually.

## Main Findings

The clustering analysis shows that Airbnb listings split into segments that align closely with **price tier, room type, and borough**, with host listing count and availability further distinguishing professionally managed listings from individually hosted ones.

The most important factors used to separate listings include:

- Location (borough, latitude, longitude)
- Room type
- Price level
- Host listing count (a proxy for professional vs. individual hosts)
- Availability during the year
- Review activity

The two largest clusters (3 and 2) represent budget-to-mid-range listings in Brooklyn, together accounting for roughly 42% of all listings after outlier removal. The smallest cluster (4) represents a niche group of highly active, frequently reviewed entire-home listings in Queens.

## Conclusion

This project demonstrates how unsupervised machine learning can be used to segment Airbnb listings without a predefined target variable. By applying K-Means clustering — with safeguards against empty clusters and multiple random initializations to avoid poor local minima — the dataset was divided into 7 groups of listings with distinct, interpretable characteristics.

The cluster profiling step ties each segment back to concrete, real-world listing traits (price, borough, room type, host scale, and availability), turning the clustering output into insights that can support business decisions such as pricing strategy, identifying professional hosts, and understanding market segmentation by borough.

## Tools and Libraries Used

- Python
- Pandas
- NumPy
- Matplotlib
- Seaborn
- Scikit-learn
  - OneHotEncoder
  - MinMaxScaler
  - PCA
  - `silhouette_score`

## Future Improvements

Future improvements could include:

- Using built-in `KMeans` from Scikit-learn for comparison
- Testing other clustering methods such as DBSCAN or Hierarchical Clustering
- Plotting cluster centers on an actual map using `latitude`/`longitude` to visualize geographic segmentation directly
- Creating visual summaries (bar charts) of average price, availability, and room type distribution by cluster, to complement the cluster profiling table
- Tuning `n_init` and comparing against k-means++-style initialization instead of uniform random sampling
