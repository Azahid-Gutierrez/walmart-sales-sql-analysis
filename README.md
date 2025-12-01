#  Walmart Sales SQL Analysis  
**Author:** Azaahid Gutierrez  
**Tools Used:** SQLite, DB Browser for SQLite, SQL  
**Dataset:** Walmart Retail Sales (2010–2012)

---

##  Dataset Overview
This analysis uses Walmart’s weekly sales dataset, which includes data from multiple stores across the U.S., including holiday weeks, economic indicators, and weather variables.

### **Key Columns**
- `Store`
- `Date`
- `Weekly_Sales`
- `Holiday_Flag`
- `Temperature`
- `Fuel_Price`
- `CPI`
- `Unemployment`

Total rows: **421,570+**

---

#  Project Objective
The goal of this SQL project is to perform a full exploratory data analysis (EDA) on Walmart’s retail performance.  
This project demonstrates:

 SQL cleaning & preparation  
 Aggregation & grouping  
 Trend analysis  
 Holiday impact evaluation  
 Economic indicator analysis  
 SQL views for BI tools  
 Documentation of insights  

---

#  SQL Analysis Breakdown

## ** Sanity Checks**
Validate data shape and structure.

```sql
SELECT COUNT(*) AS Row_Count FROM Walmart;

SELECT MIN(Date) AS Start_Date, MAX(Date) AS End_Date FROM Walmart;

SELECT COUNT(DISTINCT Store) AS Store_Count FROM Walmart;

SELECT * FROM Walmart ORDER BY Date, Store LIMIT 10;
```

---

## ** Overall Sales KPIs**

```sql
SELECT ROUND(SUM(Weekly_Sales), 2) AS Total_Sales FROM Walmart;

SELECT 
    ROUND(AVG(Weekly_Sales), 2) AS Avg_Weekly_Sales_Per_Row,
    ROUND(SUM(Weekly_Sales) / COUNT(DISTINCT Store), 2) AS Avg_Weekly_Sales_Per_Store
FROM Walmart;

SELECT COUNT(DISTINCT Date) AS Distinct_Weeks FROM Walmart;
```

---

## ** Best & Worst Performing Stores**

### Top 10 Stores
```sql
SELECT Store, ROUND(SUM(Weekly_Sales), 2) AS Total_Sales
FROM Walmart
GROUP BY Store
ORDER BY Total_Sales DESC
LIMIT 10;
```

### Bottom 10 Stores
```sql
SELECT Store, ROUND(SUM(Weekly_Sales), 2) AS Total_Sales
FROM Walmart
GROUP BY Store
ORDER BY Total_Sales ASC
LIMIT 10;
```

---

## ** Best & Worst Weeks Overall**

```sql
SELECT Date, ROUND(SUM(Weekly_Sales), 2) AS Total_Sales
FROM Walmart
GROUP BY Date
ORDER BY Total_Sales DESC
LIMIT 10;

SELECT Date, ROUND(SUM(Weekly_Sales), 2) AS Total_Sales
FROM Walmart
GROUP BY Date
ORDER BY Total_Sales ASC
LIMIT 10;
```

---

## ** Holiday vs Non-Holiday Sales**

```sql
SELECT
    CASE WHEN Holiday_Flag = 1 THEN 'Holiday Week' ELSE 'Non-Holiday Week' END AS Week_Type,
    ROUND(SUM(Weekly_Sales), 2) AS Total_Sales,
    ROUND(AVG(Weekly_Sales), 2) AS Avg_Weekly_Sales,
    COUNT(*) AS Row_Count
FROM Walmart
GROUP BY Holiday_Flag;
```

### Top Holiday Weeks
```sql
SELECT Date, ROUND(SUM(Weekly_Sales), 2) AS Total_Sales
FROM Walmart
WHERE Holiday_Flag = 1
GROUP BY Date
ORDER BY Total_Sales DESC
LIMIT 10;
```

---

## ** Yearly Sales Trends**

```sql
SELECT 
    strftime('%Y', Date) AS Year,
    ROUND(SUM(Weekly_Sales), 2) AS Total_Sales
FROM Walmart
GROUP BY Year
ORDER BY Year;
```

### Yearly sales per store
```sql
CREATE VIEW IF NOT EXISTS vw_store_year_sales AS
SELECT
    Store,
    strftime('%Y', Date) AS Year,
    ROUND(SUM(Weekly_Sales), 2) AS Total_Sales
FROM Walmart
GROUP BY Store, Year;
```

---

## ** Impact of Temperature on Sales**

```sql
WITH temp_buckets AS (
    SELECT
        CASE
            WHEN Temperature < 40 THEN 'Cold (<40F)'
            WHEN Temperature BETWEEN 40 AND 60 THEN 'Mild (40–60F)'
            WHEN Temperature BETWEEN 61 AND 80 THEN 'Warm (61–80F)'
            ELSE 'Hot (>80F)'
        END AS Temp_Bucket,
        Weekly_Sales
    FROM Walmart
)
SELECT
    Temp_Bucket,
    ROUND(SUM(Weekly_Sales), 2) AS Total_Sales,
    ROUND(AVG(Weekly_Sales), 2) AS Avg_Weekly_Sales,
    COUNT(*) AS Row_Count
FROM temp_buckets
GROUP BY Temp_Bucket
ORDER BY Total_Sales DESC;
```

---

## ** Impact of Unemployment on Sales**

```sql
WITH unemployment_buckets AS (
    SELECT
        CASE
            WHEN Unemployment < 6 THEN 'Low (<6%)'
            WHEN Unemployment BETWEEN 6 AND 8 THEN 'Medium (6–8%)'
            ELSE 'High (>8%)'
        END AS Unemp_Bucket,
        Weekly_Sales
    FROM Walmart
)
SELECT
    Unemp_Bucket,
    ROUND(SUM(Weekly_Sales), 2) AS Total_Sales,
    ROUND(AVG(Weekly_Sales), 2) AS Avg_Weekly_Sales,
    COUNT(*) AS Row_Count
FROM unemployment_buckets
GROUP BY Unemp_Bucket
ORDER BY Total_Sales DESC;
```

---

#  Key Insights

###  1. 2011 was Walmart’s strongest year  
Sales peaked across most stores.

###  2. Holiday weeks boost sales significantly  
Holiday_Flag = 1 weeks had much higher averages.

###  3. Store 20 ranked as the top-performing location  
Consistently highest total weekly revenue.

###  4. Economic conditions affect sales  
High unemployment areas surprisingly generated the most total sales.

###  5. Weather impacts store trends  
Warm (61–80°F) weeks generated the highest overall sales.

---

#  Files in This Project

| File | Description |
|------|-------------|
| **walmart_full.db** | SQLite database file |
| **walmart_analysis.sql** | Full SQL case study script |
| **README.md** | Project documentation |

---

#  What This Project Demonstrates

 SQL Data Cleaning  
 Aggregations & KPIs  
 Trend & Time Series Analysis  
 Use of Views  
 Business Insights  
 Portfolio-Ready Documentation  
 Ability to work with real-world retail data  



