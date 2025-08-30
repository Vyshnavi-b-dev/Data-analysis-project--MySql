# Data-analysis-project--MySql

## Project Overview

**Project Title**: Sales Analyzer  
**Database**: `sales_analyzer_db`

A mini data analytics project analyzing retail sales transactions using the `retail_sales.csv` dataset.

## Objectives

The main objectives of this project are:

1. **Create a retail sales database**: Use the supplied sales data to create and fill a retail sales database.
2. **Data Cleaning**: Find and eliminate any records that have null or missing values.
3. **Exploratory Data Analysis (EDA)**: To comprehend the dataset, conduct rudimentary exploratory data analysis.
4. **Business Analysis**: Utilise SQL to extract insights from the sales data and provide answers to particular business issues.

### 1. Database Setup

1. **Database Creation**: The project starts by creating a database named `sales_analyzer_db`.
2. **Table Creation**: A table named `retail_sales` is created to store the sales data. The table structure includes columns for transaction ID, sale date, sale time, customer ID, gender, age, product category, quantity sold, price per unit, cost of goods sold (COGS), and total sale amount.

```sql
CREATE DATABASE sales_analyzer_db;

CREATE TABLE retail_sales
            (
                transaction_id INT PRIMARY KEY,	
                sale_date DATE,	 
                sale_time TIME,	
                customer_id	INT,
                gender	VARCHAR(15),
                age	INT,
                category VARCHAR(15),	
                quantity	INT,
                price_per_unit FLOAT,	
                cogs	FLOAT,
                total_sale FLOAT
            );
```
### 2. Data Exploration & Cleaning
 1. Display the first 10 rows from the retail_sales table
 2. Get the total number of sales in the retail_sales table
 3. Check column names and data types
 4. Total Number of Unique Customers
 5. List of Unique Product Categories in Retail Sales

```sql

SELECT * FROM retail_sales limit 10;

SELECT COUNT(total_sale) AS Number_of_sales
FROM retail_sales;

DESCRIBE retail_sales;

SELECT DISTINCT COUNT(customer_id) AS Unique_Customers
FROM retail_sales;

SELECT DISTINCT category FROM retail_sales
```

### 3. Data Analysis & Findings

This section provides an overview of how the `retail_sales.csv` dataset can be analyzed:

1. Remove duplicate rows
2. Handle NULL or Missing Values
3. Trim Extra Spaces

```sql
SELECT transaction_id, COUNT(*) 
FROM retail_sales
GROUP BY transaction_id 
HAVING COUNT(*) >1

SELECT * FROM retail_sales
WHERE 
transaction_id IS NULL
OR
sale_date IS NULL
OR
sale_time IS NULL
OR
gender IS NULL
OR
category IS NULL
OR
quantity IS NULL
OR
cogs IS NULL
OR
total_sale IS NULL

SELECT * FROM retail_sales
WHERE sale_date IS NULL

UPDATE retail_sales
SET gender = TRIM(gender)

UPDATE retail_sales
SET category = TRIM(category)
```
This section summarizes the key insights from the `retail_sales.csv` dataset after performing data analysis:
The following SQL queries were created to address specific business enquiries:

1. **Write a SQL query to retrieve all columns for sales made on 2022-06-24**
   
```sql
SELECT * FROM retail_sales
WHERE sale_date = '2022-06-24'
```
2. **Write a SQL query to retrieve all transactions where the category is 'Clothing' and the quantity sold is more than 4 in the month of Nov-2022**
   
  ```sql
SELECT * FROM retail_sales
WHERE category = 'Clothing'
and
DATE_FORMAT(sale_date, '%Y-%m') = '2022-11'
and
quantity >= 4;
```

3.**Write a SQL query to calculate the total sales (total_sale) for each category.**

```sql
SELECT 
 category,sum(total_sale) As Total_sale
 FROM retail_sales
 GROUP BY category
```

4.**Write a SQL query to find the average age of customers who purchased items from the 'Beauty' category.**
```sql
SELECT 
ROUND(AVG(age)) AS Average_age_of_customers 
FROM retail_sales
WHERE category = 'Beauty'
 ```
5.**Write a SQL query to find all transactions where the total_sale is greater than 1000.**

```sql
SELECT * FROM retail_sales
WHERE total_sale > 1000
LIMIT 10
```

6.**Write a SQL query to find the total number of transactions (transaction_id) made by each gender in each category.**

```sql
SELECT
    category,
    gender,
    COUNT(*) AS Total_no_of_Transactions
FROM retail_sales
GROUP BY gender,category
ORDER BY 1
```

7.**Write a SQL query to calculate the average sale for each month. Find out best selling month in each year**

```sql
SELECT 
       Year,
       Month,
       Average_sales
FROM 
(
    SELECT 
        YEAR(sale_date) AS Year,
        MONTH(sale_date) AS Month,
        AVG(total_sale) AS Average_sales,
        RANK()OVER(PARTITION BY YEAR(sale_date) ORDER BY AVG(total_sale) DESC )AS Rank_Month
    FROM retail_sales
    GROUP BY YEAR(sale_date),MONTH(sale_date)
)
AS Monthly_rank
WHERE Rank_Month = 1
```

8.**Write a SQL query to find the top 5 customers based on the highest total sales**

```sql
SELECT 
    customer_id,
    SUM(total_sale) AS Total_sales
FROM 
    retail_sales
GROUP BY 1
ORDER BY 2 DESC
LIMIT 5;
```

9.**Write a SQL query to find the number of unique customers who purchased items from each category.**

```sql
SELECT 
    category,
    COUNT(DISTINCT customer_id)	AS Count_of_unique_customers
FROM    
    retail_sales
GROUP BY 
    category
```

10.**Write a SQL query to create each shift and sum of total sales (Example Morning <12, Afternoon Between 12 & 17, Evening >17)**
```sql
WITH shift_sale
AS
(
    SELECT 
        CASE
            WHEN HOUR(sale_time) < 12 THEN 'Morning'
            WHEN HOUR(sale_time) BETWEEN 12 AND 17 THEN 'Afternoon'
            ELSE 'Evening'
         END AS shift,
        total_sale
    FROM 
        retail_sales
)
SELECT 
    shift,
    SUM(total_sale) AS Total_sale
FROM 
    shift_sale
GROUP BY 
    shift
```

11.**How many customers made repeat purchases?**

```sql
SELECT 
    customer_id,
    COUNT(*) AS purchase_count
FROM 
    retail_sales
GROUP BY 
    customer_id
HAVING
purchase_count > 1
LIMIT 10
```

### Findings
1. **Sales by Date & Category**  
   - On **2022-06-24**, sales were recorded for multiple transactions.
   - The **Clothing category in Nov-2022** had several transactions where the quantity sold was greater than 4.
2. **Customer Insights**
     - The average age of customers purchasing Beauty products is approximately 40 years.
3. **Monthly & Shift Trends**
     - Best selling month in each year was identified using average sales per month.
     - Sales were also categorized into Morning, Afternoon, and Evening shifts, showing peak sales times.
4. **Repeat Customers**
    -Customers who made repeat purchases were identified, which helps in loyalty analysis.

### Reports

1. The retail_sales dataset is successfully stored in the sales_analyzer_db database.
2. Data cleaning dealt with NULL/missing values and eliminated duplicates.
3. Customer demographics, category performance, and sales dispersion were all better understood with the aid of exploratory data analysis, or EDA.
4. Actionable information such as top-performing categories, top clients, high-value transactions, and peak sales times were obtained using SQL queries.

### Conclusion

1. The project shows that it is possible to get valuable insights from raw sales data.
2. Best-selling months and product categories with the highest performance were determined.
3. Patterns of consumer behaviour, including age distribution for categories and repeat purchases, were examined.
4. Sales forecasting, inventory control, and marketing strategy are just a few of the business decisions that may be made using this approach.
