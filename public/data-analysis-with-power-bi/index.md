# RFM Analysis with SQL & Power BI (DAX)


## What is RFM?
**RFM** is a technique that can perform Customer Segmentation to determine customer's buying behavior. 
Company uses the RFM metric as a customer behavior segmentation indicator to improve marketing strategies for revenue increases by reactivating customers to more royal.

* R(Recency) : The last time the customer made a purchase. The smaller the number, the better. 

* F(Frequency) : Number of transactions. The bigger the number, the better 

* M(Monetary) : The spending power of a customer. The bigger the number, the better. 


--- 

## Data points used in RFM Analysis 
* Recency - last order date
* Frequency - count of total orders
* Monetary value - total spend

## Understand Data (SQL)

* Check the whole data
```sql
SELECT * 
FROM sales_data
```

* Inspecting Data

```sql
SELECT DISTINCT status FROM sales_data -- NICE TO PLOT
SELECT DISTINCT year_id FROM sales_data
SELECT DISTINCT PRODUCTLINE FROM sales_data -- NICE TO PLOT 
SELECT DISTINCT COUNTRY FROM sales_data -- NICE TO PLOT
SELECT DISTINCT DEALSIZE FROM sales_data -- NICE TO PLOT
SELECT DISTINCT TERRITORY FROM sales_data -- NICE TO PLOT 

SELECT DISTINCT MONTH_ID FROM sales_data
WHERE year_id = 2003
```

## Analysis 

### 1. Salse
* **By Product**
```sql
SELECT PRODUCTLINE, SUM(sales) AS revenue
FROM sales_data
GROUP BY PRODUCTLINE
ORDER BY 2 DESC; 
```
* **By year**
```sql
SELECT YEAR_ID, SUM(sales) AS revenue
FROM sales_data
GROUP BY YEAR_ID
ORDER BY 2 DESC; 
```

* **By Dealsize**
```sql
SELECT DEALSIZE, SUM(sales) AS revenue
FROM sales_data
GROUP BY DEALSIZE
ORDER BY 2 DESC; 
```
### 2. Best Month for sales per month 
```sql 
SELECT MONTH_ID, 
      SUM(sales) AS Revenue, 
      COUNT(ORDERNUMBER) AS Frequency
FROM sales_data
WHERE YEAR_ID = 2003 -- CAN CHANGE YEAR TO 2004
GROUP BY MONTH_ID 
ORDER BY Revenue DESC;

```


```sql 
SELECT MONTH_ID, 
      SUM(sales) AS Revenue, 
      COUNT(ORDERNUMBER) AS Frequency
FROM sales_data
WHERE YEAR_ID = 2003 AND MONTH_ID = 11 -- CAN CHANGE YEAR TO 2004
GROUP BY MONTH_ID, PRODUCTLINE 
ORDER BY Revenue DESC;

```

### 3. WHO IS OUR BEST CUSTOMER (this could be answered with RFM)

```sql
SELECT 
  CUSTOMERNAME, 
  SUM(sales) AS MonetaryValue, 
  AVG(sales) AS AvgMonetaryValue, 
  COUNT(ORDERNUMBER) AS Frequency, 
  MAX(ORDERDATE) AS last_order_date, 
  DATEDIFF(DD, MAX(ORDERDATE), (SELECT MAX(ORDERDATE) FROM sales_data AS max_order_date)) AS Recency
FROM sales_data
GROUP BY CUSTOMERNAME
```

### Categorize the RFM 
```sql
WITH rfm AS (
  SELECT 
    CUSTOMERNAME, 
    SUM(sales) AS MonetaryValue, 
    AVG(sales) AS AvgMonetaryValue, 
    COUNT(ORDERNUMBER) AS Frequency, 
    MAX(ORDERDATE) AS last_order_date, 
    DATEDIFF(DD, MAX(ORDERDATE), (SELECT MAX(ORDERDATE) FROM sales_data AS max_order_date)) AS Recency
  FROM sales_data
  GROUP BY CUSTOMERNAME
)

rfm_calc AS (
  SELECT R.*, 
        NTILE(4) OVER (ORDER BY Recency) rfm_recency, 
        NTILE(4) OVER (ORDER BY Frequency) rfm_frequency,
        NTILE(4) OVER (ORDER BY AvgMonetaryValue) rfm_monetary,  
  FROM rfm R
  ORDER BY 4 DESC
)

SELECT 
  C.*, 
  CAST(rfm_recency AS VARCHAR) + CAST(rfm_frequency AS VARCHAR) + CAST(rfm_monetary AS VARCHAR) AS rfm_cell_string
FROM rfm_calc C

;

```

### Create TEMP Table

```sql
DROP TABLE IF EXISTS #rfm --for global table ##rfm

WITH rfm AS (
  SELECT 
    CUSTOMERNAME, 
    SUM(sales) AS MonetaryValue, 
    AVG(sales) AS AvgMonetaryValue, 
    COUNT(ORDERNUMBER) AS Frequency, 
    MAX(ORDERDATE) AS last_order_date, 
    DATEDIFF(DD, MAX(ORDERDATE), (SELECT MAX(ORDERDATE) FROM sales_data AS max_order_date)) AS Recency
  FROM sales_data
  GROUP BY CUSTOMERNAME
),

  rfm_calc AS (
    SELECT R.*, 
          NTILE(4) OVER (ORDER BY Recency) rfm_recency, 
          NTILE(4) OVER (ORDER BY Frequency) rfm_frequency,
          NTILE(4) OVER (ORDER BY MonetaryValue) rfm_monetary,  
    FROM rfm R
    ORDER BY 4 DESC
  )

SELECT 
  C.*, 
  CAST(rfm_recency AS VARCHAR) + CAST(rfm_frequency AS VARCHAR) + CAST(rfm_monetary AS VARCHAR) AS rfm_cell_string
into #rfm
FROM rfm_calc C

;
```

```sql
SELECT CUSTOMERNAME, rfm_recency, rfm_freqeuncy, rfm_monetary, 
  CASE
   WHEN rfm_cell_string IN (111, 112, 121, 122, 123, 132, 211, 212, 114, 141) THEN 'lost_customers'
   WHEN rfm_cell_string IN (133, 134, 143, 244, 334, 343, 344) THEN 'slipping away, cannot lose'
   WHEN rfm_cell_string IN (311, 411, 331) THEN 'new_customers' 
   WHEN rfm_cell_string IN (222, 223, 233, 322) THEN 'potential churners'
   WHEN rfm_cell_string IN (323, 333, 321, 422, 332, 432) THEN 'active'
   WHEN rfm_cell_string IN (433, 434, 443, 444) THEN 'loyal'
FROM #rfm
```

### What products codes are sold together?
```SQL
SELECT DISTINCT ORDERNUMBER, STUFF(

  (SELECT ',' + PRODUCTCODE
  FROM sales_data P
  WHERE ORDERNUMBER IN 
    (
      SELECT ORDERNUMBER
      FROM (
        SELECT ORDERNUMBER, COUNT(*) AS rn, 
        FROM sales_data
        WHERE STATUS='Shipped'
        GROUP BY ORDERNUMBER
      ) m 
      where rn = 2 -- CAN CHANGE THE NUMBER
    )
    AND p.ORDERNUMBER = s.ORDERNUMBER
    for xml path ('')), 
    1, 1, '')
FROM sales_data S
ORDER BY 2 DESC
```
### Power BI DAX
``` 
Measures Table = 
```
```
Recency = DATEDIFF(MAX(sales_data[ORDERDATE]), [Last_Purchase], DAY)


AvgMonetaryValue = AVERAGE(sales_data[sales])
```


