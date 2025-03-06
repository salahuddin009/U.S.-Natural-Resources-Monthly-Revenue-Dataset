# U.S.-Natural-Resources-Monthly-Revenue-Dataset
This repository contains SQL scripts to analyze the "Monthly Revenue of U.S. Natural Resources" dataset. It includes data import, aggregation, ranking, and time-based breakdowns, along with advanced queries to explore revenue by state, product, commodity, and more. Ideal for data analysis and exploration.
#Data Source: The dataset is sourced from Kaggle: Monthly Revenue of U.S. Natural Resources: Kaggle Dataset

# Table Schema: natural_resources

# Create Table
 
CREATE TABLE natural_resources (
    Date DATE,
    Land_Class VARCHAR(50),
    Land_Category VARCHAR(50),
    State_Name VARCHAR(50),
    Country VARCHAR(50),
    FIPS_Code INT,
    Revenue_Type VARCHAR(50),
    Mineral_Lease_Type VARCHAR(50),
    Commodity VARCHAR(50),
    Product VARCHAR(150),
    Revenue NUMERIC(20,4)
);

# Load Data from CSV
 
COPY natural_resources (Date, Land_Class, Land_Category, State_Name, Country, FIPS_Code, Revenue_Type, Mineral_Lease_Type, Commodity, Product, Revenue)
FROM 'G:/Dataset/Monthly_Revenue_of_U.S_Natural_Resources.csv'
DELIMITER ',' CSV HEADER;
Data Exploration

# Inspect Table Schema
 
SELECT column_name, data_type, is_nullable
FROM information_schema.columns
WHERE table_name = 'natural_resources';

# View First 10 Rows
 
SELECT * FROM natural_resources
LIMIT 10;

# Check for Missing Values
 
SELECT 
    COUNT(*) AS total_rows,
    SUM(CASE WHEN Date IS NULL THEN 1 ELSE 0 END) AS missing_date,
    SUM(CASE WHEN Land_Class IS NULL THEN 1 ELSE 0 END) AS missing_Land_Class,
    SUM(CASE WHEN Land_Category IS NULL THEN 1 ELSE 0 END) AS missing_Land_Category,
    SUM(CASE WHEN State_Name IS NULL THEN 1 ELSE 0 END) AS missing_State_Name,
    SUM(CASE WHEN Country IS NULL THEN 1 ELSE 0 END) AS missing_Country,
    SUM(CASE WHEN FIPS_Code IS NULL THEN 1 ELSE 0 END) AS missing_FIPS_Code,
    SUM(CASE WHEN Revenue_Type IS NULL THEN 1 ELSE 0 END) AS missing_Revenue_Type,
    SUM(CASE WHEN Mineral_Lease_Type IS NULL THEN 1 ELSE 0 END) AS missing_Mineral_Lease_Type,
    SUM(CASE WHEN Commodity IS NULL THEN 1 ELSE 0 END) AS missing_Commodity,
    SUM(CASE WHEN Product IS NULL THEN 1 ELSE 0 END) AS missing_Product,
    SUM(CASE WHEN Revenue IS NULL THEN 1 ELSE 0 END) AS missing_Revenue
FROM natural_resources;

# Find Distinct Values
# Get Distinct Values for Key Columns
 
SELECT DISTINCT 
    Land_Class,
    Land_Category,
    State_Name,
    Country,
    Revenue_Type,
    Mineral_Lease_Type,
    Commodity,
    Product
FROM natural_resources;
# Descriptive Statistics
 
 
SELECT 
    MAX(Revenue) AS max_revenue,
    MIN(revenue) AS min_revenue,
    AVG(Revenue) AS avg_revenue,
    STDDEV_POP(Revenue) AS std_revenue
FROM natural_resources;

# Revenue Breakdown
# Breakdown by Land Class
 
SELECT land_class, SUM(revenue) AS total_revenue 
FROM natural_resources
GROUP BY land_class;

# Breakdown by Land Category
 
SELECT land_category, SUM(revenue) AS total_revenue
FROM natural_resources
GROUP BY land_category
ORDER BY total_revenue;

# Breakdown by State Name
 
SELECT state_name, SUM(revenue) AS total_revenue
FROM natural_resources
GROUP BY state_name
ORDER BY total_revenue DESC;

# Breakdown by Country
 
SELECT country, SUM(revenue) AS total_revenue
FROM natural_resources
GROUP BY country 
ORDER BY total_revenue;

# Breakdown by Revenue Type
 
SELECT revenue_type, SUM(revenue) AS total_revenue
FROM natural_resources
GROUP BY revenue_type
ORDER BY total_revenue;
#Time-Based Revenue Breakdown

# Breakdown by Year
 
SELECT EXTRACT(YEAR FROM date) AS year_data, SUM(revenue) AS total_revenue
FROM natural_resources
GROUP BY EXTRACT(YEAR FROM date)
ORDER BY year_data;

# Breakdown by Month
 
SELECT EXTRACT(MONTH FROM date) AS monthly_data, SUM(revenue) AS total_revenue
FROM natural_resources
GROUP BY EXTRACT(MONTH FROM date)
ORDER BY monthly_data;

# Breakdown by Year and Month
 
SELECT 
    EXTRACT(YEAR FROM date) AS year_data, 
    EXTRACT(MONTH FROM date) AS monthly_data, 
    SUM(revenue) AS total_revenue
FROM natural_resources
GROUP BY EXTRACT(YEAR FROM date), EXTRACT(MONTH FROM date)
ORDER BY year_data, monthly_data;

# Breakdown by Quarter
 
SELECT 
    EXTRACT(YEAR FROM date) AS year_data, 
    EXTRACT(QUARTER FROM date) AS quarterly_data,
    SUM(revenue) AS total_revenue
FROM natural_resources
GROUP BY EXTRACT(YEAR FROM date), EXTRACT(QUARTER FROM date)
ORDER BY year_data, quarterly_data;

# Views and Indexes
# Create Materialized View: Above Average Revenue by State

CREATE MATERIALIZED VIEW above_avg_rev AS
WITH avg_rev AS(
    SELECT AVG(revenue) AS avg_rev
    FROM natural_resources)
SELECT date, state_name, revenue
FROM natural_resources, avg_rev
WHERE revenue > avg_rev.avg_rev;

# Create Index: By Date, State, Country, Revenue

CREATE INDEX indx_date_state_country_rev
ON natural_resources(date, state_name, country, revenue);

# Advanced Queries
# Find the States with Negative Revenue

SELECT date, state_name, revenue
FROM natural_resources
WHERE revenue < 0;

# Find Top Ten States by Revenue

SELECT state_name, SUM(revenue) AS total_revenue,
       DENSE_RANK() OVER (ORDER BY SUM(revenue) DESC) AS rank
FROM natural_resources
GROUP BY state_name
ORDER BY rank
LIMIT 10;

# Find Top Ten Products by Revenue

SELECT product, SUM(revenue) AS total_revenue,
       DENSE_RANK() OVER (ORDER BY SUM(revenue) DESC) AS rank
FROM natural_resources
GROUP BY product
ORDER BY rank
LIMIT 10;

# Find Top Revenue Types by Revenue

SELECT revenue_type, SUM(revenue) AS total_revenue,
       DENSE_RANK() OVER (ORDER BY SUM(revenue) DESC) AS rank
FROM natural_resources
GROUP BY revenue_type
ORDER BY rank
LIMIT 10;

# Conclusion
This repository provides a comprehensive SQL-based analysis of the Monthly Revenue of U.S. Natural Resources dataset. It includes various aggregation techniques, time-based breakdowns, and advanced queries to help you explore and understand the revenue patterns in U.S. natural resources.
