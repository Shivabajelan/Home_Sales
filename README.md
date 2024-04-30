# Home_Sales
## Overview of the Analysis
This project uses SparkSQL to analyse home sales data from 2019 to 2022. The data is loaded from an S3 bucket . The goal is to determine key metrics about home sales, such as average prices and view ratings, and comparing the cached runtime and parqueit runtime. The project involves creating temporary views, partitioning the data, caching and uncaching a temporary table, and verifying that the table has been uncached.
The analysis is done in the Home_Sales.ipynb notebook.

## Results
I analysed the data to answer the following questions:


1.What is the average price for a four-bedroom house sold for each year?

    ```sql
    SELECT 
        YEAR(date) AS year,
        ROUND(AVG(price),2) As avg_price
    FROM home_sales
    WHERE bedrooms=4
    GROUP BY year
    ORDER BY year
    ```
    
    
![Q1 data](Images/Q1.png)

2. What is the average price of a home for each year it was built that has three bedrooms and three bathrooms?
    ```sql
    SELECT
        date_built, 
        ROUND(AVG(price), 2) AS avg_price
    FROM home_sales
    WHERE bedrooms=3 AND bathrooms=3
    GROUP BY date_built
    ORDER BY date_built
    ```
![Q2 data](Images/Q2.png)

3. What is the average price of a home for each year that has three bedrooms, three bathrooms, two floors, and is greater than or equal to 2,000 square feet?
    ```sql
    SELECT
        date_built, 
        ROUND(AVG(price), 2) AS avg_price
    FROM home_sales
    WHERE bedrooms=3 AND bathrooms=3
    GROUP BY date_built
    ORDER BY date_built
    ```
![Q3 data](Images/Q3.png) 

4. What is the "view" rating for the average price of a home where the homes are greater than or equal to $350,000?
    ```sql
    SELECT 
        view,
        ROUND(AVG(price), 2) AS avg_price
    FROM home_sales
    GROUP BY view
    HAVING avg_price >= 350000
    ORDER BY view DESC
    ```
![Q4 data](Images/Q4.png)

5. Comparing the runtime for the "view" rating for the average price of a home where the homes are greater than or equal to $350,000 (Q4) when we haven't cached the data, cached data, and when data is ppartitioned: 

**Standard runtime for Q4** :  0.59 seconds

**Cached runtime for Q4** :  0.35 seconds

**Parquet runtime for Q4** : 0.76 seconds

## Summary
Comparing the standard runtime, cached runtime, and parquet runtime idicate that running a query on the cached table was the fastest option (0.35 s). Running a query on the partitioned parquet files were the slowest (0.76 s). 
The faster query performance on cached tables is due to direct in-memory data access. Partitioned Parquet files are slower because they require disk access and also this is likely due to the partition being on the "date_built" column, which was not used in the query.