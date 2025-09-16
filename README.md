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
* Findings:

    * Visits slightly decreased in February but bounced back in March.

    * Pageviews remained stable with an upward trend.

    * Transactions consistently increased, with the highest growth in March.

→ Overall performance improved steadily, especially in March with stronger engagement and sales.

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

* Findings:

    * Google and Direct are the top traffic sources by visit volume.

    * YouTube and Facebook have relatively high bounce rates (>60%), indicating weaker engagement.

    * Some smaller sources (e.g., reddit, sites.google.com) show much lower bounce rates (<30–40%), suggesting more qualified traffic.

→ High-volume sources drive most visits but also higher bounce rates. Smaller niche sources, though lower in traffic, deliver more engaged users.

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

* Findings:

    * Direct traffic generates the highest revenue by far (over 97K in June, with strong weekly contributions up to 30K).
    
    * Google is the second-largest contributor (~18.7K), followed by DFA (~8.8K) and Mail.Google (~2.5K).
    
    * Other sources such as YouTube, Yahoo, Bing, and niche domains contribute very little revenue (<100).

→ Revenue is highly concentrated in Direct and Google sources. Smaller channels bring minimal impact, while DFA shows strong potential as an additional high-value source.

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

* Findings:

    * Purchasers viewed significantly fewer pages on average compared to non-purchasers.

        * June 2017: ~94 vs. ~317 pageviews.
        
        * July 2017: ~124 vs. ~334 pageviews.

    * Non-purchasers consistently required 3x more pageviews than purchasers, suggesting they browse extensively without converting.

    * Purchasers showed a moderate increase in engagement (from 94 → 124 pageviews), which may indicate more deliberate exploration before purchase.

→ Purchasers demonstrate more focused browsing behavior, while non-purchasers’ high pageview counts reflect inefficiency in conversion. Optimizing the user journey (e.g., clearer CTAs, simplified checkout) could help reduce unnecessary browsing and improve conversion rate.

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

* Finding:

    * In July 2017, purchasing users made on average ~4.16 transactions each.

→ Users who purchase tend to buy multiple times within the same month, showing strong repeat purchase behavior.

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

* Finding:

    * In July 2017, purchasers spent on average ~$43.86 per session.

→ Each purchasing session generated a relatively high revenue, indicating strong monetization per visit.

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

* Findings:

    * Google Sunglasses (20 units) and Google Women’s Vintage Hero T-shirts (7 units) are the top co-purchased products with YouTube Men’s Vintage Henley.

    * Other items such as lip balm, short sleeve shirts, and various Google/YouTube branded accessories appear in small quantities (1–2 units each).

    * Purchases span across different categories (apparel, accessories, mugs, decals), indicating a diverse interest among these customers.

→ Customers who buy YouTube Men’s Vintage Henley tend to cross-purchase other Google/YouTube lifestyle products, with sunglasses and T-shirts standing out as the strongest cross-sell opportunities.

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

* Findings:

    * Jan 2017: 100% product views → 28.47% add-to-cart → 8.31% purchase
    
    * Feb 2017: 100% product views → 34.25% add-to-cart → 9.59% purchase
    
    * Mar 2017: 100% product views → 37.29% add-to-cart → 12.64% purchase

→ The add-to-cart rate increased steadily from January to March (28% → 37%), and the purchase rate also rose (8% → 12%), indicating improving conversion efficiency over time.

**V. CONCLUSION**
- In conclusion, my exploration of the eCommerce dataset using SQL on Google BigQuery based on the Google Analytics dataset has revealed several interesting insights.
- By exploring eCommerce dataset, I have gained valuable information about total visits, pageview, transactions, bounce rate, and revenue per traffic source,.... which could inform future business decisions.
- Overall, this project has demonstrated the power of using SQL and big data tools like Google BigQuery to gain insights into large datasets.


 
    

   
