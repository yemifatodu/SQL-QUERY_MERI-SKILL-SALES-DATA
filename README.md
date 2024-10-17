# SQL-QUERY_MERI-SKILL-SALES-DATA

This repository is the SQL section of the Meri Skill Sales Project Analysis. Here, you'll find SQL-based solutions and insights derived from the sales data, along with advanced SQL techniques applied to solve real-world business problems.
You can find the full project report in the main repository here:  
**[Sale_Data_MeriSkill_Internship_Project](https://github.com/yemifatodu/Sale_Data_MeriSkill_internship_project?tab=readme-ov-file#sale_data_meriskill_internship_project)**

---


SQL QUERY IN DATA CLEANING PROCESS

   - **Select All Data**:
     ```sql
     SELECT * FROM [dbo].[Sale_Data];
     ```

   - **Drop a Column** ( `Unnamed: 0`):
     ```sql
     ALTER TABLE [dbo].[Sale_Data]
     DROP COLUMN [Unnamed: 0];
     ```

   - **Rename a Column** (`Unnamed: 7` to `Month`):
     ```sql
     EXEC sp_rename '[dbo].[Sale_Data].[Unnamed: 7]', 'Months', 'COLUMN';
     ```

   - **Split `Order Date` into `Date_Only` and `Time_Only`**:
     SQL Server doesn’t directly support splitting strings into multiple columns in a single statement. However, I did it using `SUBSTRING` and `CHARINDEX`:
     ```sql
     ALTER TABLE [dbo].[Sale_Data]
     ADD [Date_Only] DATE, [Time_Only] TIME;

     UPDATE [dbo].[Sale_Data]
     SET [Date_Only] = CAST([Order Date] AS DATE),
         [Time_Only] = CAST([Order Date] AS TIME);

     -- Drop the original Order Date column
     ALTER TABLE [dbo].[Sale_Data]
     DROP COLUMN [Order Date];
     ```

   - **Example: Grouping Sales by Month**:
     ```sql
     SELECT Month, SUM(Sales) AS Total_Sales
     FROM [dbo].[Sale_Data]
     GROUP BY Month
     ORDER BY Month;
     ```



---

### 1. **Sales Trend Over Time (Top Left Chart)**

```sql
-- SQL Query to get monthly sales trend for 2019
SELECT 
    STRFTIME('%m', [Order Date]) AS Month,
    SUM(Sales) AS Total_Sales
FROM 
    SalesData
WHERE 
    STRFTIME('%Y', [Order Date]) = '2019'
GROUP BY 
    STRFTIME('%m', [Order Date])
ORDER BY 
    STRFTIME('%m', [Order Date]);
```

**Explanation**: This query calculates the total sales for each month in 2019, allowing you to analyze sales trends over time.

---

### 2. **Total Sales & Sales Quantity (Top Right Section)**

```sql
-- SQL Query to get total sales and total quantity ordered in 2019
SELECT 
    SUM(Sales) AS Total_Sales,
    SUM([Quantity Ordered]) AS Total_Quantity
FROM 
    SalesData
WHERE 
    STRFTIME('%Y', [Order Date]) = '2019';
```

**Explanation**: This query aggregates the total sales and total quantity ordered for the entire year of 2019.

---

### 3. **Hourly Sales Distribution (Middle Right Chart)**

```sql
-- SQL Query to analyze sales distribution by hour for 2019
SELECT 
    STRFTIME('%H', [Order Date]) AS Hour,
    SUM(Sales) AS Total_Sales
FROM 
    SalesData
WHERE 
    STRFTIME('%Y', [Order Date]) = '2019'
GROUP BY 
    STRFTIME('%H', [Order Date])
ORDER BY 
    STRFTIME('%H', [Order Date]);
```

**Explanation**: This query calculates total sales for each hour of the day, helping to identify peak sales hours.

---

### 4. **Top 10 Selling Products (Bottom Left Charts)**

```sql
-- SQL Query to get top 10 products by quantity sold
SELECT 
    Product, 
    SUM([Quantity Ordered]) AS Total_Quantity
FROM 
    SalesData
WHERE 
    STRFTIME('%Y', [Order Date]) = '2019'
GROUP BY 
    Product
ORDER BY 
    SUM([Quantity Ordered]) DESC
LIMIT 10;

-- SQL Query to get top 10 products by total sales
SELECT 
    Product, 
    SUM(Sales) AS Total_Sales
FROM 
    SalesData
WHERE 
    STRFTIME('%Y', [Order Date]) = '2019'
GROUP BY 
    Product
ORDER BY 
    SUM(Sales) DESC
LIMIT 10;
```

**Explanation**: The first query finds the top 10 products based on quantity sold, while the second query finds the top 10 products based on total sales.

---

### 5. **Quantity Ordered by City (Middle Chart)**

```sql
-- SQL Query to get the total quantity ordered by city
SELECT 
    City, 
    SUM([Quantity Ordered]) AS Total_Quantity
FROM 
    SalesData
WHERE 
    STRFTIME('%Y', [Order Date]) = '2019'
GROUP BY 
    City
ORDER BY 
    SUM([Quantity Ordered]) DESC;
```

**Explanation**: This query calculates the total quantity of products ordered in each city, allowing you to identify cities with high demand.

---

### 6. **Sales by City (Middle Right Map)**

```sql
-- SQL Query to get the total sales by city
SELECT 
    City, 
    SUM(Sales) AS Total_Sales
FROM 
    SalesData
WHERE 
    STRFTIME('%Y', [Order Date]) = '2019'
GROUP BY 
    City
ORDER BY 
    SUM(Sales) DESC;
```

**Explanation**: This query calculates total sales in each city, useful for geographical analysis of sales performance.

---

### 7. **Sales Correlation Analysis (Bottom Middle Chart)**

```sql
-- SQL Queries for correlation analysis between different products

-- Example for correlating sales between two products
SELECT 
    A.Product AS Product_A, 
    B.Product AS Product_B, 
    COUNT(*) AS Correlation_Count
FROM 
    SalesData A
JOIN 
    SalesData B ON A.[Order ID] = B.[Order ID] AND A.Product != B.Product
WHERE 
    STRFTIME('%Y', A.[Order Date]) = '2019'
GROUP BY 
    A.Product, B.Product
ORDER BY 
    Correlation_Count DESC
LIMIT 10;
```

**Explanation**: This query checks for orders where two different products were purchased together, which helps in understanding product correlations.

---

### 8. **Trend of Year Quantity Distribution (Bottom Right Chart)**

```sql
-- SQL Query to analyze the distribution of quantity ordered across months
SELECT 
    STRFTIME('%m', [Order Date]) AS Month,
    SUM([Quantity Ordered]) AS Total_Quantity
FROM 
    SalesData
WHERE 
    STRFTIME('%Y', [Order Date]) = '2019'
GROUP BY 
    STRFTIME('%m', [Order Date])
ORDER BY 
    STRFTIME('%m', [Order Date]);
```

**Explanation**: This query calculates the total quantity of products ordered each month, showing the distribution of orders throughout the year.

---

Uncovering hidden insights from a dataset involves diving deeper into the data and exploring patterns, anomalies, correlations, and trends that might not be immediately obvious. Here are some potential findings that I explore in the dataset, along with the SQL queries to derive the hidden insights from the dataset

### 1. **Sales Seasonality**
   - **Finding**: Identify if there are certain months or periods of the year where sales spike or drop significantly.
   - **SQL Query**:
   ```sql
   -- Total Sales by Month
   SELECT 
       Month, 
       SUM(Sales) AS Total_Sales
   FROM 
       [dbo].[Sale_Data]
   GROUP BY 
       Month
   ORDER BY 
       Total_Sales DESC;
   ```

### 2. **Sales per Hourly Distribution by City**
   - **Finding**: Determine which hours of the day are most profitable for each city.
   - **SQL Query**:
   ```sql
   -- Sales Distribution by Hour and City
   SELECT 
       City, 
       Hour, 
       SUM(Sales) AS Total_Sales
   FROM 
       [dbo].[Sale_Data]
   GROUP BY 
       City, 
       Hour
   ORDER BY 
       City, 
       Hour;
   ```

### 3. **Average Order Value (AOV)**
   - **Finding**: Calculate the average order value to understand how much customers are spending on average.
   - **SQL Query**:
   ```sql
   -- Average Order Value (AOV)
   SELECT 
       AVG(Sales) AS Average_Order_Value
   FROM 
       [dbo].[Sale_Data];
   ```

### 4. **Top Customers**
   - **Finding**: Identify the top customers based on the total amount spent.
   - **SQL Query**:
   ```sql
   -- Top Customers by Total Sales
   SELECT 
       [Purchase Address], 
       SUM(Sales) AS Total_Spent
   FROM 
       [dbo].[Sale_Data]
   GROUP BY 
       [Purchase Address]
   ORDER BY 
       Total_Spent DESC;
   ```

### 5. **Sales Distribution by Product Category**
   - **Finding**: Analyze which product categories are driving the most sales.
   - **SQL Query**:
   ```sql
   -- Sales Distribution by Product
   SELECT 
       Product, 
       SUM(Sales) AS Total_Sales
   FROM 
       [dbo].[Sale_Data]
   GROUP BY 
       Product
   ORDER BY 
       Total_Sales DESC;
   ```

### 6. **Sales Variance Between Cities**
   - **Finding**: Compare the sales performance between different cities to identify top-performing regions.
   - **SQL Query**:
   ```sql
   -- Sales Comparison by City
   SELECT 
       City, 
       SUM(Sales) AS Total_Sales
   FROM 
       [dbo].[Sale_Data]
   GROUP BY 
       City
   ORDER BY 
       Total_Sales DESC;
   ```


### 7. **Customer Purchase Frequency**
   - **Finding**: Determine how frequently customers are making purchases.
   - **SQL Query**:
   ```sql
   -- Customer Purchase Frequency
   SELECT 
       [Purchase Address], 
       COUNT([Order ID]) AS Purchase_Count
   FROM 
       [dbo].[Sale_Data]
   GROUP BY 
       [Purchase Address]
   ORDER BY 
       Purchase_Count DESC;
   ```


### 8. **Sales Contribution by Product**
   - **Finding**: Determine the contribution of each product to the overall sales.
   - **SQL Query**:
   ```sql
   -- Contribution of Each Product to Total Sales
   SELECT 
       Product, 
       SUM(Sales) AS Total_Sales,
       SUM(Sales) * 100.0 / (SELECT SUM(Sales) FROM [dbo].[Sale_Data]) AS Sales_Contribution_Percentage
   FROM 
       [dbo].[Sale_Data]
   GROUP BY 
       Product
   ORDER BY 
       Total_Sales DESC;
   ```

### 9. **Sales by Weekday**
   - **Finding**: Analyze sales trends by day of the week to see which days are the most profitable.
   - **SQL Query**:
   ```sql
   -- Sales by Day of the Week
   SELECT 
       DATENAME(WEEKDAY, CAST(Date_Only AS DATETIME)) AS Weekday, 
       SUM(Sales) AS Total_Sales
   FROM 
       [dbo].[Sale_Data]
   GROUP BY 
       DATENAME(WEEKDAY, CAST(Date_Only AS DATETIME))
   ORDER BY 
       Total_Sales DESC;
```

---

```sql
-- Top 3 Most Profitable Hours for Each City
WITH RankedHours AS (
    SELECT 
        City,
        Hour,
        SUM(Sales) AS Total_Sales,
        ROW_NUMBER() OVER (PARTITION BY City ORDER BY SUM(Sales) DESC) AS SalesRank
    FROM 
        [dbo].[Sale_Data]
    GROUP BY 
        City, 
        Hour
)
SELECT 
    City, 
    Hour, 
    Total_Sales
FROM 
    RankedHours
WHERE 
    SalesRank <= 3
ORDER BY 
    City, 
    SalesRank;
```
---

```sql
-- Bottom 3 Least Profitable Hours for Each City
WITH RankedHours AS (
    SELECT 
        City,
        Hour,
        SUM(Sales) AS Total_Sales,
        ROW_NUMBER() OVER (PARTITION BY City ORDER BY SUM(Sales) ASC) AS SalesRank
    FROM 
        [dbo].[Sale_Data]
    GROUP BY 
        City, 
        Hour
)
SELECT 
    City, 
    Hour, 
    Total_Sales
FROM 
    RankedHours
WHERE 
    SalesRank <= 3
ORDER BY 
    City, 
    SalesRank;
```
---

```sql
-- Top 10 Customers by Total Sales
SELECT 
    TOP 10 [Purchase Address], 
    SUM(Sales) AS Total_Spent
FROM 
    [dbo].[Sale_Data]
GROUP BY 
    [Purchase Address]
ORDER BY 
    Total_Spent DESC;
```
---

```sql
-- Grouped by Sales with Product, Sales, and Quantity Ordered
SELECT 
    Product, 
    SUM(Sales) AS Total_Sales,
    SUM([Quantity Ordered]) AS Total_Quantity_Ordered
FROM 
    [dbo].[Sale_Data]
GROUP BY 
    Product, 
    Sales
ORDER BY 
    Total_Sales DESC;
```
