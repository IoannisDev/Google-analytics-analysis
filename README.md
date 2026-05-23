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

**CPM**: CPM has the highest revenue per visit , but the number visitor is lacking. There is significant difference between the value (Revenue per visit) and volume(Total number of visits), suggesting high visitor conversion , but lacking in number of visitor to the site.

**Referral**: While referral is the source of most of the visitors, but it lacks in quality of visitors , with ranking of 4 out of 5. Suggesting the need to implement referral bonus to increase conversion rate.

CPM and CPC are the only channels where value rank outpaces volume rank. This indicates that while they draw fewer total visitors, those visitors convert at a much higher rate.


---

## Conclusion

**North America is the dominant market** across every metric : engagement, conversion, and revenue  and should remain the primary focus for investment and optimization.

**South America presents a high-intent opportunity.** Despite weak overall engagement (ranked 17th in bounce rate), it holds a strong conversion rank of 3rd. Consumers arriving from South America tend to arrive with purchase intent already formed, making targeted marketing campaigns a cost effective lever for growth in the region.

**Eastern Asia shows balanced, stable behavior** with rankings consistently between 4th and 6th across all metrics, suggesting a reliable secondary market worth nurturing with localized content.

**Central Asia is a niche, high-volume buyer segment.** Its high bounce rate indicates most visitors leave quickly, but those who stay convert in larger volumes  pointing to a small but committed buyer base rather than a broad consumer audience.

**CPM is the highest-value traffic channel** by revenue per visit, but it suffers from low visitor volume. Scaling CPM spend could yield disproportionate revenue gains without a proportional increase in traffic costs.

**Referral drives the most visitors but the weakest conversion.** With the highest volume rank and a value rank of 4th out of 5, referral traffic is largely unqualified. Introducing referral incentive programs or better landing page targeting for referral sources could improve conversion quality.

**Direct traffic is the most reliable path to purchase.** Strategies that build brand recognition and encourage return visits  such as email campaigns, loyalty programs, and SEO  are likely to have the strongest impact on overall revenue.
