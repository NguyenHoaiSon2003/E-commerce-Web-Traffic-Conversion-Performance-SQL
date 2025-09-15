# E-commerce Web traffic & conversion performance analyst | SQL

Utilizeed SQL in Google BigQuery to write and execute queries to find the desired data

**I. INTRODUCTION**

This project contains an eCommerce dataset that I will explore using SQL on Google BigQuery. The dataset is based on the Google Analytics public dataset and contains data from an eCommerce website.

**II. REQUIREMENTS**
- Google Cloud Platform account
- Project on Google Cloud Platform
- Google BigQuery API enabled
- SQL query editor or IDE

**III. DATASET ACCESS**

The eCommerce dataset is stored in a public Google BigQuery dataset. To access the dataset, follow these steps:
- Log in to your Google Cloud Platform account and create a new project.
- Navigate to the BigQuery console and select your newly created project.
- In the navigation panel, select "Add Data" and then "Search a project".
- Enter the project ID "bigquery-public-data.google_analytics_sample.ga_sessions" and click "Enter".
- Click on the "ga_sessions_" table to open it.

**IV. EXPLORING THE DATASET**

In this project, I will write 08 query in Bigquery base on Google Analytics dataset

**Query 1: Calculate total visit, pageview, transaction for Jan, Feb and March 2017 (order by month)**

* SQL code
    
```sql
SELECT
  format_date("%Y%m", parse_date("%Y%m%d", date)) as month,
  SUM(totals.visits) AS visits,
  SUM(totals.pageviews) AS pageviews,
  SUM(totals.transactions) AS transactions,
FROM `bigquery-public-data.google_analytics_sample.ga_sessions_2017*`
WHERE _TABLE_SUFFIX BETWEEN '0101' AND '0331'
GROUP BY 1
ORDER BY 1;
```
* Query result

    <img width="792" height="132" alt="image" src="https://github.com/user-attachments/assets/ce3a1493-c61d-4212-88ed-33c84a5902d7" />

**Query 2: Bounce rate per traffic source in July 2017 (Bounce_rate = num_bounce/total_visit) (order by total_visit DESC)**

* SQL code
   
```sql
SELECT trafficSource.source AS source
      ,SUM(totals.bounces) AS total_no_of_bounces
      ,SUM(totals.visits) AS total_visits
      ,ROUND(100*SUM(totals.bounces)/SUM(totals.visits),3) AS bounce_rate
FROM `bigquery-public-data.google_analytics_sample.ga_sessions_201707*` 
GROUP BY trafficSource.source
ORDER BY total_visits DESC
```

* Query result
<img width="790" height="540" alt="image" src="https://github.com/user-attachments/assets/49f66b4a-db32-4090-a7a8-6597883ee3f0" />

**Query 3: Revenue by traffic source by week, by month in June 2017**

* SQL code
```sql
   WITH month_data as(
      SELECT
            "Month" as time_type,
            format_date("%Y%m", parse_date("%Y%m%d", date)) as month,
            trafficSource.source AS source,
            SUM(p.productRevenue)/1000000 AS revenue
      FROM `bigquery-public-data.google_analytics_sample.ga_sessions_201706*`,
      unnest(hits) hits,
      unnest(product) p
      WHERE p.productRevenue is not null
      GROUP BY 1,2,3
      order by revenue DESC
),
week_data as(
      SELECT
            "Week" as time_type,
            format_date("%Y%W", parse_date("%Y%m%d", date)) as week,
            trafficSource.source AS source,
            SUM(p.productRevenue)/1000000 AS revenue
      FROM `bigquery-public-data.google_analytics_sample.ga_sessions_201706*`,
      unnest(hits) hits,
      unnest(product) p
      WHERE p.productRevenue is not null
      GROUP BY 1,2,3
      order by revenue DESC
)
select * from month_data
union all
select * from week_data
order by time_type
```
* Query result

    <img width="982" height="714" alt="image" src="https://github.com/user-attachments/assets/34baa1b0-0659-4424-83bc-0bee8f92c752" />

**Query 4: Average number of pageviews by purchaser type (purchasers vs non-purchasers) in June, July 2017**

* SQL code

```sql
with purchaser_data as(
  select
      format_date("%Y%m",parse_date("%Y%m%d",date)) as month,
      (sum(totals.pageviews)/count(distinct fullvisitorid)) as avg_pageviews_purchase,
  from `bigquery-public-data.google_analytics_sample.ga_sessions_2017*`
    ,unnest(hits) hits
    ,unnest(product) product
  where _table_suffix between '0601' and '0731'
  and totals.transactions>=1
  and product.productRevenue is not null
  group by month
),

non_purchaser_data as(
  select
      format_date("%Y%m",parse_date("%Y%m%d",date)) as month,
      sum(totals.pageviews)/count(distinct fullvisitorid) as avg_pageviews_non_purchase,
  from `bigquery-public-data.google_analytics_sample.ga_sessions_2017*`
      ,unnest(hits) hits
    ,unnest(product) product
  where _table_suffix between '0601' and '0731'
  and totals.transactions is null
  and product.productRevenue is null
  group by month
)
select
    pd.*,
    avg_pageviews_non_purchase
from purchaser_data pd
full join non_purchaser_data using(month)
order by pd.month;
```
* Query result

    <img width="891" height="101" alt="image" src="https://github.com/user-attachments/assets/d85935e8-869e-462f-9563-caeb8800ced9" />

**Query 5: Average number of transactions per user that made a purchase in July 2017**

* SQL code
```sql   
select
    format_date("%Y%m",parse_date("%Y%m%d",date)) as month,
    sum(totals.transactions)/count(distinct fullvisitorid) as Avg_total_transactions_per_user
from `bigquery-public-data.google_analytics_sample.ga_sessions_201707*`
    ,unnest (hits) hits,
    unnest(product) product
where  totals.transactions>=1
and product.productRevenue is not null
group by month;
```
* Query result

    <img width="616" height="67" alt="image" src="https://github.com/user-attachments/assets/30d370c8-7af8-4f7e-a449-af6255cd2679" />

**Query 6: Average amount of money spent per session. Only include purchaser data in July 2017**

* SQL code
```sql   
select
    format_date("%Y%m",parse_date("%Y%m%d",date)) as month,
    ((sum(product.productRevenue)/sum(totals.visits))/power(10,6)) as avg_revenue_by_user_per_visit
from `bigquery-public-data.google_analytics_sample.ga_sessions_201707*`
  ,unnest(hits) hits
  ,unnest(product) product
where product.productRevenue is not null
and totals.transactions>=1
group by month;
```

* Query result

    <img width="623" height="68" alt="image" src="https://github.com/user-attachments/assets/c2d1f61c-5046-44e0-b008-6b9a417bfc7e" />

**Query 7: Other products purchased by customers who purchased product "YouTube Men's Vintage Henley" in July 2017. Output should show product name and the quantity was ordered.**

* SQL code
```sql   
WITH user_id AS --Lay ra user_id co purchase Youtube Men
(
  SELECT DISTINCT fullVisitorId
  FROM `bigquery-public-data.google_analytics_sample.ga_sessions_201707*` ,
  UNNEST (hits)hits,
  UNNEST (hits.product)product
  WHERE v2ProductName ="YouTube Men's Vintage Henley"
        AND productRevenue IS NOT NULL
)
SELECT v2ProductName AS other_purchased_products
      ,SUM(productQuantity) AS quantity
FROM `bigquery-public-data.google_analytics_sample.ga_sessions_201707*`,
UNNEST (hits)hits,
UNNEST (hits.product)product
WHERE fullVisitorId IN (SELECT fullVisitorId FROM user_id)
      AND productRevenue IS NOT NULL
      AND v2ProductName <> "YouTube Men's Vintage Henley"
GROUP BY v2ProductName
ORDER BY quantity DESC
```
* Query result

    <img width="482" height="711" alt="image" src="https://github.com/user-attachments/assets/6bf498c9-5e3e-42f7-b980-e8860c391f92" />

**Query 8: Calculate cohort map from product view to addtocart to purchase in Jan, Feb and March 2017. For example, 100% product view then 40% add_to_cart and 10% purchase.**
Add_to_cart_rate = number product  add to cart/number product view. Purchase_rate = number product purchase/number product view. The output should be calculated in product level.

* SQL code
```sql   
with
product_view as(
  SELECT
    format_date("%Y%m", parse_date("%Y%m%d", date)) as month,
    count(product.productSKU) as num_product_view
  FROM `bigquery-public-data.google_analytics_sample.ga_sessions_*`
  , UNNEST(hits) AS hits
  , UNNEST(hits.product) as product
  WHERE _TABLE_SUFFIX BETWEEN '20170101' AND '20170331'
  AND hits.eCommerceAction.action_type = '2'
  GROUP BY 1
),

add_to_cart as(
  SELECT
    format_date("%Y%m", parse_date("%Y%m%d", date)) as month,
    count(product.productSKU) as num_addtocart
  FROM `bigquery-public-data.google_analytics_sample.ga_sessions_*`
  , UNNEST(hits) AS hits
  , UNNEST(hits.product) as product
  WHERE _TABLE_SUFFIX BETWEEN '20170101' AND '20170331'
  AND hits.eCommerceAction.action_type = '3'
  GROUP BY 1
),

purchase as(
  SELECT
    format_date("%Y%m", parse_date("%Y%m%d", date)) as month,
    count(product.productSKU) as num_purchase
  FROM `bigquery-public-data.google_analytics_sample.ga_sessions_*`
  , UNNEST(hits) AS hits
  , UNNEST(hits.product) as product
  WHERE _TABLE_SUFFIX BETWEEN '20170101' AND '20170331'
  AND hits.eCommerceAction.action_type = '6'
  and product.productRevenue is not null   
  group by 1
)

select
    pv.*,
    num_addtocart,
    num_purchase,
    round(num_addtocart*100/num_product_view,2) as add_to_cart_rate,
    round(num_purchase*100/num_product_view,2) as purchase_rate
from product_view pv
left join add_to_cart a on pv.month = a.month
left join purchase p on pv.month = p.month
order by pv.month;
```

* Query result

    <img width="1103" height="131" alt="image" src="https://github.com/user-attachments/assets/d5618cfa-3e63-4337-ad57-6d34cf17dc37" />

**V. CONCLUSION**
- In conclusion, my exploration of the eCommerce dataset using SQL on Google BigQuery based on the Google Analytics dataset has revealed several interesting insights.
- By exploring eCommerce dataset, I have gained valuable information about total visits, pageview, transactions, bounce rate, and revenue per traffic source,.... which could inform future business decisions.
- Overall, this project has demonstrated the power of using SQL and big data tools like Google BigQuery to gain insights into large datasets.


 
    

   
