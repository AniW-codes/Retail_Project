# Retail Sales Analysis SQL Project

## Project Overview

**Project Title**: Retail Sales Analysis  
**Level**: Beginner  
**Database**: `Portfolio_Project`

This project is designed to demonstrate SQL skills and techniques typically used by data analysts to explore, clean, and analyze retail sales data. The project involves setting up a retail sales database, performing exploratory data analysis (EDA), and answering specific business questions through SQL queries. This project is ideal for those who are starting their journey in data analysis and want to build a solid foundation in SQL.

## Objectives

1. **Set up a retail sales database**: Create and populate a retail sales database with the provided sales data.
2. **Data Cleaning**: Identify and remove any records with missing or null values.
3. **Exploratory Data Analysis (EDA)**: Perform basic exploratory data analysis to understand the dataset.
4. **Business Analysis**: Use SQL to answer specific business questions and derive insights from the sales data.

## Project Structure

### 1. Database Setup

- **Database Creation**: The project starts by creating a database named `p1_retail_db`.
- **Table Creation**: A table named `Portfolio_Project.dbo.Retail_Sales_Analysis` is created to store the sales data. The table structure includes columns for transaction ID, sale date, sale time, customer ID, gender, age, product category, quantity sold, price per unit, cost of goods sold (COGS), and total sale amount.

```sql
CREATE DATABASE p1_retail_db;

CREATE TABLE Portfolio_Project.dbo.Retail_Sales_Analysis_Analysis
(
    transactions_id INT PRIMARY KEY,
    sale_date DATE,	
    sale_time TIME,
    customer_id INT,	
    gender VARCHAR(10),
    age INT,
    category VARCHAR(35),
    quantity INT,
    price_per_unit FLOAT,	
    cogs FLOAT,
    total_sale FLOAT
);
```

### 2. Data Exploration & Cleaning

- **Record Count**: Determine the total number of records in the dataset.
- **Customer Count**: Find out how many unique customers are in the dataset.
- **Category Count**: Identify all unique product categories in the dataset.
- **Null Value Check**: Check for any null values in the dataset and delete records with missing data.

```sql
SELECT COUNT(*) FROM Portfolio_Project.dbo.Retail_Sales_Analysis;
SELECT COUNT(DISTINCT customer_id) FROM Portfolio_Project.dbo.Retail_Sales_Analysis;
SELECT DISTINCT category FROM Portfolio_Project.dbo.Retail_Sales_Analysis;

SELECT * FROM Portfolio_Project.dbo.Retail_Sales_Analysis
WHERE 
    sale_date IS NULL OR sale_time IS NULL OR customer_id IS NULL OR 
    gender IS NULL OR age IS NULL OR category IS NULL OR 
    quantity IS NULL OR price_per_unit IS NULL OR cogs IS NULL;

DELETE FROM Portfolio_Project.dbo.Retail_Sales_Analysis
WHERE 
    sale_date IS NULL OR sale_time IS NULL OR customer_id IS NULL OR 
    gender IS NULL OR age IS NULL OR category IS NULL OR 
    quantity IS NULL OR price_per_unit IS NULL OR cogs IS NULL;


```

### 3. Data Analysis & Findings

The following SQL queries were developed to answer specific business questions:

1. **Write a SQL query to retrieve all columns for sales made on '2022-11-05**:
```sql
SELECT *
FROM Portfolio_Project.dbo.Retail_Sales_Analysis
WHERE sale_date = '2022-11-05';
```

2. **Write a SQL query to retrieve all transactions where the category is 'Clothing' and the quantity sold is more than 4 in the month of Nov-2022**:

```sql
---Adding Month to the table
Select sale_date, DATENAME(MONTH, sale_date)
from Portfolio_Project.dbo.Retail_Sales_Analysis

Alter Table Portfolio_Project.dbo.Retail_Sales_Analysis
Add Month_Sale varchar(20)

Update Portfolio_Project.dbo.Retail_Sales_Analysis
Set Month_Sale = DATENAME(MONTH, sale_date)


---Adding YEAR to the table
Select sale_date, DATENAME(YEAR, sale_date)
from Portfolio_Project.dbo.Retail_Sales_Analysis

Alter Table Portfolio_Project.dbo.Retail_Sales_Analysis
Add Year_Sale int

Update Portfolio_Project.dbo.Retail_Sales_Analysis
Set Year_Sale = DATENAME(YEAR, sale_date)

Select *
from Portfolio_Project.dbo.Retail_Sales_Analysis


---Adding WEEKDAY to the table
Select sale_date, DATENAME(WEEKDAY, sale_date)
from Portfolio_Project.dbo.Retail_Sales_Analysis

Alter Table Portfolio_Project.dbo.Retail_Sales_Analysis
Add Weekday_Sale varchar(20)

Update Portfolio_Project.dbo.Retail_Sales_Analysis
Set Weekday_Sale = DATENAME(WEEKDAY, sale_date)

```

```sql
Select *
from Portfolio_Project.dbo.Retail_Sales_Analysis
WHERE category = 'Clothing'
	and quantiy >= 4
	and Month_Sale = 'November'
	and Year_Sale = 2022
```

3. **Write a SQL query to calculate the total sales (total_sale) for each category.**:
```sql
Select category, SUM(total_sale), Count(*) as Total_Orders
from Portfolio_Project.dbo.Retail_Sales_Analysis
group by category
```

4. **Write a SQL query to find the average age of customers who purchased items from the 'Beauty' category.**:
```sql
Select ROUND(Avg(Age), +2)
from Portfolio_Project.dbo.Retail_Sales_Analysis
where category = 'Beauty'
```

5. **Write a SQL query to find all transactions where the total_sale is greater than 1000.**:
```sql
Select * 
from Portfolio_Project.dbo.Retail_Sales_Analysis
where total_sale > 1000
```

6. **Write a SQL query to find the total number of transactions (transaction_id) made by each gender in each category.**:
```sql
Select Gender, Category, COUNT(*) as Total_Transactions
from Portfolio_Project.dbo.Retail_Sales_Analysis
Group By Gender, category
Order By 1
```

7. **Write a SQL query to calculate the average sale for each month. Find out best selling month in each year**:
```sql
With CTE_Monthly_Sale as (
Select 
	Year_Sale, 
	Month_Sale, 
	Round(Avg(total_sale), 3) as Average_Sale_per_Month,
	RANK() OVER(Partition By Year_Sale Order By Avg(total_sale) Desc) as MonthRank
from Portfolio_Project.dbo.Retail_Sales_Analysis
GROUP BY Year_Sale, Month_Sale
							)
Select * from 
CTE_Monthly_Sale where MonthRank = 1
```

8. **Write a SQL query to find the top 5 customers based on the highest total sales **:
```sql
Select TOP(5) customer_id, 
		sum(total_sale) as Sum
		from Portfolio_Project.dbo.Retail_Sales_Analysis
GROUP BY customer_id
Order By Sum desc
```

9. **Write a SQL query to find the number of unique customers who purchased items from each category.**:
```sql
Select 
	category,
	Count(Distinct(Customer_id)) as Unique_Customers
from Portfolio_Project.dbo.Retail_Sales_Analysis
group By category
```

10. **Write a SQL query to create each shift and number of orders (Example Morning <12, Afternoon Between 12 & 17, Evening >17)**:
```sql
Select 
	* ,
	CASE When DATEPART(HOUR, sale_time) < 12 then 'Morning'
		When DATEPART(HOUR, sale_time) between 12 and 17 then 'Afternoon'
		Else 'Evening'
	END as Shifts
from Portfolio_Project.dbo.Retail_Sales_Analysis

--Since group by cannot be used on column created in select statement, we need to use a CTE to make it happen.
--Query to create number of orders in different time of the day	

Select *
from Portfolio_Project.dbo.Retail_Sales_Analysis

With CTE_Orders as (
				Select 	* ,
	CASE When DATEPART(HOUR, sale_time) < 12 then 'Morning'
		When DATEPART(HOUR, sale_time) between 12 and 17 then 'Afternoon'
		Else 'Evening'
	END as Shifts
from Portfolio_Project.dbo.Retail_Sales_Analysis
				)
Select Shifts, Count(*) as Orders_in_those_hours
from CTE_Orders
group By Shifts
```

## Findings

- **Customer Demographics**: The dataset includes customers from various age groups, with sales distributed across different categories such as Clothing and Beauty.
- **High-Value Transactions**: Several transactions had a total sale amount greater than 1000, indicating premium purchases.
- **Sales Trends**: Monthly analysis shows variations in sales, helping identify peak seasons.
- **Customer Insights**: The analysis identifies the top-spending customers and the most popular product categories.

## Reports

- **Sales Summary**: A detailed report summarizing total sales, customer demographics, and category performance.
- **Trend Analysis**: Insights into sales trends across different months and shifts.
- **Customer Insights**: Reports on top customers and unique customer counts per category.

## Conclusion

This project serves as a comprehensive introduction to SQL for data analysts, covering database setup, data cleaning, exploratory data analysis, and business-driven SQL queries. The findings from this project can help drive business decisions by understanding sales patterns, customer behavior, and product performance.


## Author - Aniruddha Warang

This project is part of my portfolio, showcasing the SQL skills essential for data analyst roles. If you have any questions, feedback, or would like to collaborate, feel free to get in touch!

**LinkedIn**: [Connect with me professionally](https://www.linkedin.com/in/aniruddhawarang/)

Thank you for your support, and I look forward to connecting with you!
