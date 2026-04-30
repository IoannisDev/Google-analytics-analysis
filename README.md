# Google Analytics Data Analysis

## Research Questions

1. What geographic and traffic sources are driving consumer spending on products?
2. What are some areas to focus on for further expansion?

**Data Source:** Google Analytics data (BigQuery)

**Tools Used:**
1. BigQuery
2. SQL
3. Python
4. Matplotlib
5. Pandas

---

### Which Sub Continent ranked in market metrics?

![alt text](assets/sub_continent.png)

> **NOTE:** In the above chart, lower means better.

> **Methodology:** Used BigQuery to query data and used window function to rank sub continent by bounce rate, conversion rate, time spent by customer's on the site, and number of views.

**North America:** North America ranks 1st in every metric, where consumers are engaging with the website and making purchases. This is the highest-performing market and should be prioritized.

**Central Asia:** In Central Asia, the majority of the consumers aren't browsing the website, ranking 19th in bounce ranking, with the few consumers who are staying making purchases. Indicating that only a few consumers make purchases in larger volume.

**Eastern Asia:** Eastern Asian consumers are balanced, with rankings ranging from 4 to 6 in the categories, suggesting balanced consumer behavior.

**South America:** South America shows weak engagement, being ranked 17th, but a stronger conversion rate, with a rank of 3. This suggests that the majority of the consumers have already made the choice of the product to purchase. This suggests that there should be a focus on marketing to target intent-driven visitors.

---

### Which consumer group provided more value by volume?

> **Methodology:**
> Used bigquery to query data , with the use of window function and CTE to calculate the total revenue per visit

```sql
WITH high_value AS (
  SELECT
    IFNULL(SUM(totals.transactionRevenue), 0) / 1000000 AS total_revenue,
    IFNULL(SUM(totals.visits), 0) AS total_visits,
    IFNULL((SUM(totals.transactionRevenue) / 1000000) / NULLIF(SUM(totals.visits), 0), 0) AS revenue_per_visit,
    trafficSource.medium AS traffic_medium
  FROM `bigquery-public-data.google_analytics_sample.ga_sessions_*`
  WHERE _TABLE_SUFFIX BETWEEN '20160801' AND '20170131' 
    AND trafficSource.medium NOT IN ('(none)', '(not set)')
  GROUP BY trafficSource.medium
)
SELECT *,
  RANK() OVER(ORDER BY revenue_per_visit DESC) AS value_rank,
  RANK() OVER(ORDER BY total_visits DESC) AS volume_rank
FROM high_value;
```

### Volume and value comparison by traffic medium

![alt text](assets/Volume_value.png)

**CPM**: CPM has the highest revenue per visit , but the number visitor is lackinng. There is significant difference between the value (Revenue per visit) and volume(Total number of visits), suggesting high visitor conversion , but lacking in number of visitor to the site.

**Referral**: While referral is the source of most of the visitors, but it lacks in quality of visitors , with ranking of 4 out of 5. Suggesting the need to implement refferal bonus to increase conversion rate.

CPM , CPC. are the only medium where the value rank is higher incomparison to volume rank, suuggestion both lacks the number of visitors , but higher visitor conversion than others.


### Customer Segmentation:
![alt text](assets/customer_segment.png)

From Kmeans Clustering(k=3), identified  that majority of sessions falls into low or moderate engagement with little to no conversion. With only the customer segment with highest engagement made most transactions than others.

We can distinguish three different segment of customers 

1. Engaged browsers : The customers who engage with the website but make minimal purchases 
2. Minimal engageers : The customers who engage less, with little to no purchase

3. Buyers : These are the active customer segment , who engages and makes purchases, having the highest engagement metric and purchase 

### Customer Segment by Subcontinent (% of each segment)

| Subcontinent | Buyers | Engaged Browsers | Minimal Engagers |
|-------------|--------|-----------------|-----------------|
| Northern America | 97.11% | 65.34% | 43.14% |
| South America | 0.85% | 2.26% | 4.38% |
| Eastern Asia | 0.57% | 4.22% | 4.22% |
| Central America | 0.34% | 1.03% | 1.99% |
| Western Asia | 0.23% | 1.60% | 5.45% |

> North America accounrs for 97.11% of the buyers ,while other region make less than 1% of purchases, indicating that the purchase intent is heavily concentrated in North America

### Traffic Source by Segment

| Medium | Buyers | Engaged Browsers | Minimal Engagers |
|--------|--------|-----------------|-----------------|
| Direct (none) | 68.16% | 48.52% | 51.14% |
| Organic | 16.46% | 11.56% | 11.14% |
| Referral | 10.68% | 36.63% | 34.66% |
| Affiliate | 2.16% | 1.76% | 1.37% |
| CPC | 1.36% | 0.80% | 0.81% |
|CMP| 1.17%| 0.72%| 0.88%|

> Kmeans Clustering also identified that direct sources  accounts for 68.16% of buyers customer segments, while refferal make up of 34.66% percent of Minimal engagers, with 10.68% of  buyers suggesting that the conversion rate is lower despite driving high volume.