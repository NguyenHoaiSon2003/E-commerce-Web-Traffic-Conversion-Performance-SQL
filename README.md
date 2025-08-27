# -SQL-Explore-ecommerce-dataset
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
1. Query 1: Calculate total visit, pageview, transaction for Jan, Feb and March 2017 (order by month)

   **- SQL CODE**
   
    <img width="624" height="180" alt="image" src="https://github.com/user-attachments/assets/e5ba26fb-4326-4281-bd5a-70f1f53ffcf7" />

   **- QUERY RESULT**

    <img width="792" height="132" alt="image" src="https://github.com/user-attachments/assets/ce3a1493-c61d-4212-88ed-33c84a5902d7" />

2. Query 2: Bounce rate per traffic source in July 2017 (Bounce_rate = num_bounce/total_visit) (order by total_visit DESC)

   **- SQL CODE**
   
    <img width="665" height="141" alt="image" src="https://github.com/user-attachments/assets/459a9273-e967-45d0-8b34-f19f4672f53f" />

   **- QUERY RESULT**
<img width="790" height="540" alt="image" src="https://github.com/user-attachments/assets/49f66b4a-db32-4090-a7a8-6597883ee3f0" />

3. Query 3: Revenue by traffic source by week, by month in June 2017

   **- SQL CODE**
   
    <img width="761" height="599" alt="image" src="https://github.com/user-attachments/assets/90e9697d-6cd9-44ab-bb1a-ddd6091444b3" />

   **- QUERY RESULT**

    <img width="982" height="714" alt="image" src="https://github.com/user-attachments/assets/34baa1b0-0659-4424-83bc-0bee8f92c752" />

4. Query 4: Average number of pageviews by purchaser type (purchasers vs non-purchasers) in June, July 2017

   **- SQL CODE**
   
    <img width="843" height="623" alt="image" src="https://github.com/user-attachments/assets/92607b28-c505-49fa-82e9-4cafaf0f1698" />

   **- QUERY RESULT**

    <img width="891" height="101" alt="image" src="https://github.com/user-attachments/assets/d85935e8-869e-462f-9563-caeb8800ced9" />

5. Query 5: Average number of transactions per user that made a purchase in July 2017

   **- SQL CODE**
   
    <img width="888" height="178" alt="image" src="https://github.com/user-attachments/assets/8d479fa4-a2e0-4e61-8b78-bbe12ba85a1a" />

   **- QUERY RESULT**

    <img width="616" height="67" alt="image" src="https://github.com/user-attachments/assets/30d370c8-7af8-4f7e-a449-af6255cd2679" />\

6. Query 6: Average amount of money spent per session. Only include purchaser data in July 2017

   **- SQL CODE**
   
    <img width="947" height="184" alt="image" src="https://github.com/user-attachments/assets/5ec345ae-6ac0-40e5-a3cb-cf26d04c7e3c" />

   **- QUERY RESULT**

    <img width="623" height="68" alt="image" src="https://github.com/user-attachments/assets/c2d1f61c-5046-44e0-b008-6b9a417bfc7e" />

7. Query 7: Other products purchased by customers who purchased product "YouTube Men's Vintage Henley" in July 2017. Output should show product name and the quantity was ordered.

   **- SQL CODE**
   
    <img width="734" height="379" alt="image" src="https://github.com/user-attachments/assets/b20bc85d-1caf-4ee4-b193-06d2f786e32e" />

   **- QUERY RESULT**

    <img width="482" height="711" alt="image" src="https://github.com/user-attachments/assets/6bf498c9-5e3e-42f7-b980-e8860c391f92" />

8. Query 8: Calculate cohort map from product view to addtocart to purchase in Jan, Feb and March 2017. For example, 100% product view then 40% add_to_cart and 10% purchase.

   **- SQL CODE**
   
   <img width="669" height="480" alt="image" src="https://github.com/user-attachments/assets/56539063-a680-4db6-bc3d-0f5d54ae979e" />
   <img width="671" height="466" alt="image" src="https://github.com/user-attachments/assets/f84facbb-6bc3-41b7-825c-e3078b1150da" />


   **- QUERY RESULT**

    <img width="1103" height="131" alt="image" src="https://github.com/user-attachments/assets/d5618cfa-3e63-4337-ad57-6d34cf17dc37" />

**V. CONCLUSION**
- In conclusion, my exploration of the eCommerce dataset using SQL on Google BigQuery based on the Google Analytics dataset has revealed several interesting insights.
- By exploring eCommerce dataset, I have gained valuable information about total visits, pageview, transactions, bounce rate, and revenue per traffic source,.... which could inform future business decisions.
- To deep dive into the insights and key trends, the next step will visualize the data with some software like Power BI, Tableau,...
- Overall, this project has demonstrated the power of using SQL and big data tools like Google BigQuery to gain insights into large datasets.


 
    

   
