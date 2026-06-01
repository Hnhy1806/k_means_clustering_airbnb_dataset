# Airbnb Clustering Analysis

## Project Overview

This project applies **K-Means clustering** to an Airbnb dataset to group listings with similar characteristics. Instead of predicting a target variable, the goal is to discover natural patterns among Airbnb listings based on location, room type, price, review activity, host listing count, and yearly availability.

The analysis focuses on understanding how listings can be segmented into different groups, which can support business decisions such as market positioning, pricing strategy, and identifying listing behavior patterns.

## Dataset

The dataset contains **48,895 Airbnb listings** with 16 original columns, including listing information, host details, location, room type, price, reviews, and availability.

Key variables used for clustering:

- `neighbourhood_group`
- `room_type`
- `price`
- `minimum_nights`
- `number_of_reviews`
- `reviews_per_month`
- `calculated_host_listings_count`
- `availability_365`

These features were selected because they describe important listing characteristics such as where the listing is located, what type of accommodation it offers, how expensive it is, how active it is in reviews, how many listings the host manages, and how often the listing is available.

## Data Cleaning and Preprocessing

The preprocessing process included the following steps:

### 1. Handling Missing Values

The column `reviews_per_month` contained missing values, which were filled with `0`. This is reasonable because listings with no recorded reviews likely have no monthly review activity.

Other missing columns such as `name`, `host_name`, and `last_review` were not used in the clustering model.

### 2. Feature Selection

Only clustering-relevant columns were selected. Identification columns and text-based columns were excluded because they do not directly help measure similarity between listings.

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

Categorical variables were converted into numerical form using **OneHotEncoder**:

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

## Choosing the Number of Clusters

The **Elbow Method** was used to compare different values of `k` from 2 to 9. The Sum of Squared Errors (SSE) was calculated for each value of `k`.

Based on the elbow plot, the final number of clusters was selected as:

```text
k = 7
```

The final model converged successfully and produced a final SSE of:

```text
23780.87
```

## Final Cluster Distribution

The final 7 clusters had the following number of listings:

| Cluster | Number of Listings |
|---|---:|
| 6 | 13,321 |
| 1 | 12,931 |
| 4 | 6,424 |
| 0 | 5,666 |
| 5 | 3,056 |
| 3 | 3,017 |
| 2 | 2,045 |

The cluster sizes show that some listing types are very common in the market, while others represent smaller and more specific groups of Airbnb listings.

## Visualization

Principal Component Analysis (PCA) was used only for visualization. The high-dimensional clustering result was projected into two dimensions so the clusters and centroids could be displayed on a scatter plot.

PCA was not used as the main clustering input. It was mainly used to make the final clustering result easier to understand visually.

## Main Findings

The clustering analysis suggests that Airbnb listings can be grouped into several meaningful segments based on listing behavior and listing characteristics.

The most important factors used to separate listings include:

- Location group
- Room type
- Price level
- Minimum night requirement
- Review activity
- Host listing count
- Availability during the year

The larger clusters likely represent common Airbnb listing patterns, while the smaller clusters may represent more specialized listing types, such as listings with unusual availability, higher prices, more review activity, or hosts managing many listings.

## Conclusion

This project demonstrates how unsupervised machine learning can be used to segment Airbnb listings without a predefined target variable. By applying K-Means clustering, the dataset was divided into 7 groups of listings with similar characteristics.

The analysis shows that clustering can help identify different types of Airbnb listings in the market. These insights can be useful for understanding customer options, comparing listing strategies, and supporting business decisions related to pricing, availability, and market segmentation.

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

## Future Improvements

Future improvements could include:

- Using built-in `KMeans` from Scikit-learn for comparison
- Adding cluster profiling to describe each cluster in business terms
- Testing other clustering methods such as DBSCAN or Hierarchical Clustering
- Including latitude and longitude to improve geographic clustering
- Creating visual summaries of average price, availability, and room type distribution by cluster
- Evaluating cluster quality using silhouette scores