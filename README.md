# May-June
Welcome to My Project Repository — May-June Edition

This GitHub repository showcases a curated collection of industry-level personal projects that I successfully completed during May–June. Each project reflects my ability to solve real-world business problems, apply technical skills across tools like SQL, Python, Power BI, and Excel, and adapt quickly in a fast-paced learning environment.

🧠 Whether it's designing an ETL pipeline, building interactive dashboards, performing deep-dive analysis, or generating actionable insights—every project here is a step toward impactful data-driven decision-making.

🔖 Naming Convention:

P1, P2, etc. → Personal Projects

I → Internship Projects

Explore these works to see my journey as an aspiring Data Analyst, where I combine problem-solving, storytelling, and technology to create real value.


-----------------------------------------------------------SQL Query---------------------------------------------------------------------------

--****************************************** TABLE VIEW **************************************************************
SELECT *
FROM detail
LIMIT 33;

SELECT *
FROM review
LIMIT 11;

--&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&& METRICS & AGGREGATES &&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&
SELECT COUNT(*)
FROM detail; --9994

SELECT COUNT(*) --793
FROM review;

SELECT COUNT(DISTINCT(customerid))
FROM detail;    --793 **Both of the schemas are in standardized format.

SELECT DISTINCT region FROM detail;
--South,West,East,Central

SELECT DISTINCT subcategory FROM detail;
--17

SELECT ordermonth, COUNT(ordermonth)
FROM detail
GROUP BY ordermonth;
-- Nov, Dec, Sept are most frequent for the orders.

SELECT SUM(sales) FROM detail;
--2296118.60

SELECT SUM(quantity) FROM detail;
--37870

SELECT MAX(discount) FROM detail;
-- 0.80

SELECT MIN(profit) FROM detail;
--0.00

SELECT DISTINCT customerrating FROM review;

SELECT DISTINCT servicerating FROM review;

--############################### EXPLORATORY DATA ANALYSIS(EDA) ###################################

SELECT segment, COUNT(*) AS segment_count
FROM detail
WHERE profit = 0.00
GROUP BY segment;
-- Consumer segment has the lowest profit

SELECT category, COUNT(*) AS category_count
FROM detail
WHERE profit = 0.00
GROUP BY category;
-- Office supplies has the least profit.

-- CUSTOMER RATINGS

SELECT customerrating, COUNT(*) AS rating_count
FROM review
GROUP BY customerrating
ORDER BY rating_count;
--The customers above 7 rating will have to provide special offers to increase the sales.

SELECT customerid, customerrating, COUNT(*) AS rating_count
FROM review
WHERE customerrating < 5
GROUP BY customerrating, customerid
ORDER BY rating_count;
--The orders from these customerid's should be confirmed twice to reduce the cancellation probability.

SELECT servicerating, COUNT(*) AS rating_count
FROM review
WHERE servicerating < 5
GROUP BY servicerating
ORDER BY rating_count;
--Service rating of 3 is the most frequent one

--SHIPPING DELAY BY STATE

SELECT state, delay, COUNT(*) AS delay_count
FROM detail
WHERE delay > 5
GROUP BY state, delay
ORDER BY delay_count DESC;
-- California, New York, Texas

-- Customer Distribution

SELECT COUNT(DISTINCT(customerid)) AS customercount, state
FROM detail
GROUP BY state
ORDER BY COUNT(DISTINCT(customerid)) DESC
LIMIT 10;
-- California, New York, Texas

-- Subcategory Popularity

SELECT subcategory, COUNT(*) AS item_count
FROM detail
GROUP BY subcategory
ORDER BY item_count DESC;
--Binders 1523, Paper 1370, Furnishings 957


-- Region Distribution
SELECT region, COUNT(*) AS region_count
FROM detail
GROUP BY region
ORDER BY region_count;
--West, East


-- Order Count by ShipMode
SELECT shipmode, COUNT(*) AS order_count
FROM detail
GROUP BY shipmode
ORDER BY order_count DESC;
--5968-Standard Class, 1945-Second Class

-- Customer Service Ratings by State

SELECT d.city, d.state, COUNT(*) AS review_count, MIN(r.servicerating) AS min_rating
FROM detail d
JOIN review r ON d.customerid = r.customerid
GROUP BY d.city, d.state
HAVING MIN(r.servicerating) < 5
ORDER BY review_count DESC;
-- The Service Rating of "New York", "California", "Pennsylvania", "Washington" is least.

--Repeted Customers

SELECT customerid, customername, state, COUNT(*) AS orders
FROM detail
GROUP BY customerid, customername, state
ORDER BY orders DESC;
-- Repetitive customer ids "WB-21850"..........

-- High quantity and low rated customers

SELECT d.customerid, MAX(d.quantity) AS order_quantity, MIN(r.customerrating) AS least_rated
FROM detail d
JOIN review r ON d.customerid = r.customerid
GROUP BY d.customerid
HAVING MIN(r.customerrating) < 4
ORDER BY order_quantity DESC;
--Helps reduce the logistic cost for the e-commerce platform.

--Highest Sales of category, sub-category according to month

SELECT category, subcategory, ordermonth,
       SUM(quantity) AS total_quantity, SUM(sales) AS total_sales
FROM detail
GROUP BY category, subcategory, ordermonth
ORDER BY total_sales DESC;
-- "Furniture"	"Chairs"	"Dec"	430	57429.80
-- "Technology"	"Phones"	"Nov"	520	56112.70
 

--(AOV) BY STATES
SELECT state, ROUND(SUM(sales) / COUNT(DISTINCT orderid), 1) AS AVG_Ord_val
FROM detail
GROUP BY state
ORDER BY AVG_Ord_val DESC
LIMIT 10;
--Identify high-spending customer states to focus retention and upsell strategies.

--YOY SALES GROWTH
SELECT EXTRACT(YEAR FROM orderdate) AS sales_year, SUM(sales) AS total_sales
FROM detail
GROUP BY sales_year
ORDER BY total_sales DESC;
--Understand sales performance trajectory across years.

--PROFIT MARGIN BY CATEGORY

SELECT category,
       ROUND(SUM(profit) / NULLIF(SUM(sales), 0) * 100, 2) AS profit_margin
FROM detail
GROUP BY category
ORDER BY profit_margin DESC;
-- NULLIF added to prevent division by zero.
-- Office Supplies has the highest profit margin.

--CUSTOMER LIFETIME VALUE (LTV) APPROXIMATION
SELECT customerid, SUM(sales) AS lifetime_value
FROM detail
GROUP BY customerid
ORDER BY lifetime_value DESC
LIMIT 10;
--Identify your most valuable customers for loyalty or VIP programs.
-- SM-20320	25043.30
-- TC-20980	19052.30
-- RB-19360	15117.30


--TOP SUBCATEGORIES PER SEGMENT
WITH ranked_subcategories AS (
  SELECT segment, subcategory, COUNT(*) AS purchases,
         RANK() OVER (PARTITION BY segment ORDER BY COUNT(*) DESC) AS rnk
  FROM detail
  GROUP BY segment, subcategory
)
SELECT * FROM ranked_subcategories
WHERE rnk <= 3;
--Discover which subcategories are popular within segments

--HIGH SHIPPING DELAY BASED ON REGION
SELECT region, delay, COUNT(*) AS delay_count
FROM detail
WHERE delay > 3
GROUP BY region, delay
ORDER BY delay_count DESC;
-- West	4 832

--MONTHWISE DEMAND OF PRODUCTS
SELECT ordermonth, subcategory, COUNT(*) AS demand
FROM detail
GROUP BY ordermonth, subcategory
ORDER BY demand DESC;

--MONTHLY CUSTOMER Count
SELECT ordermonth, COUNT(DISTINCT customerid) AS customer_count
FROM detail
GROUP BY ordermonth
ORDER BY customer_count desc;

--HIGH SHIPPING DELAY BASED ON SHIPPING MODE
SELECT shipmode, delay AS delay_in_days, COUNT(*) AS delay_count
FROM detail
WHERE delay > 3
GROUP BY shipmode, delay
ORDER BY delay_count ASC;





