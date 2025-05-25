# Blinkit-Analysis
To conduct a comprehensive analysis of blinkit’s sales performance, customer satisfaction, and inventory distribution to identify key insights and opportunities for Optimization using various KPI’s and visualizations in Power BI.

### Data Cleaning
````
--- CLEANING ITEM_FAT_CONTENT---
UPDATE blinkit_data
    SET item_Fat_Content = 
	CASE
	WHEN Item_Fat_Content IN ('LF', 'low fat') THEN 'Low Fat'
	WHEN Item_Fat_Content = 'reg' THEN 'Regular'
	ELSE Item_Fat_Content
END;
````
### Kpi's Requirement:
##### Total Sales: The overall revenue generated from all items sold.
##### Average Sales: The average revenue per sale.
##### Number of items: The total count of different items sold.
##### Average Rating: The average customer rating for items sold.

````---TO CHECK THE CLEAN DATA
SELECT DISTINCT Item_Fat_Content FROM blinkit_data;


--- TOTAL SALES
SELECT 
	CAST(SUM(Total_sales)/1000000 AS DECIMAL(10,2)) AS Total_Sales_Millions
FROM blinkit_data;

--- AVERAGE SALES
SELECT 
	CAST(AVG(Total_Sales) AS DECIMAL(10,1)) AS Avg_Sales
FROM blinkit_data;

---TOTAL NUMBER OF ITEMS
SELECT
	COUNT(*) AS No_of_items
FROM blinkit_data;


--- AVERAGE RATING
SELECT 
	CAST(AVG(Rating) AS DECIMAL(10,2)) AS Avg_rating
FROM blinkit_data;
````
### Granular Requirement:
#### Total Sales by Fat Content
###### - Objective: Analyze the impact of fat content on total sales
###### - Additional KPI Metrics: Assess how other KPI’s (Average Sales, Number of items, Average Rating) vary with fat content

#### Total Sales by item Type
###### - Objective: Identify the performance of different item types in terms of total sales
###### - Additional KPI Metrics: Assess how other KPI’s (Average Sales, Number of items, Average Rating) vary with fat content
#### Fat Content by Outlet for Total Sales
###### - Objective: Compare total sales across different outlets segmented by fat content.
###### - Additional KPI Metrics: Assess how other KPI’s (Average Sales, Number of items, Average Rating) vary with fat content                      
#### Total Sales by Outlet Establishment
###### - Objective: Compare total sales across different outlets segmented by fat content.

````
---TOTAL SALES BY FAT CONTENT
SELECT 
	Item_fat_content, 
	CAST(SUM(Total_sales)/1000 AS DECIMAL(10,2))AS Total_sales_Thousands,
	CAST(AVG(Total_Sales) AS DECIMAL(10,1)) AS Avg_Sales,
	COUNT(*) AS No_of_item,
	CAST(AVG(Rating) AS DECIMAL(10,2)) AS Avg_rating
FROM blinkit_data
GROUP BY Item_Fat_Content
ORDER BY Total_sales_Thousands DESC;

---Item type
SELECT TOP 5
	Item_type, 
	CAST(SUM(Total_sales)/1000 AS DECIMAL(10,2))AS Total_sales_Thousands,
	CAST(AVG(Total_Sales) AS DECIMAL(10,1)) AS Avg_Sales,
	COUNT(*) AS No_of_item,
	CAST(AVG(Rating) AS DECIMAL(10,2)) AS Avg_rating
FROM blinkit_data
GROUP BY Item_type
ORDER BY Total_sales_Thousands DESC;

---OUTLET LOCATION TYPE
SELECT Outlet_Location_Type, Item_Fat_Content
	Item_type, 
	CAST(SUM(Total_sales)/1000 AS DECIMAL(10,2))AS Total_sales_Thousands,
	CAST(AVG(Total_Sales) AS DECIMAL(10,1)) AS Avg_Sales,
	COUNT(*) AS No_of_item,
	CAST(AVG(Rating) AS DECIMAL(10,2)) AS Avg_rating
FROM blinkit_data
GROUP BY Outlet_Location_Type, Item_Fat_Content
ORDER BY Total_sales_Thousands DESC;

---Fat Content by Outlet for Total Sales
SELECT Outlet_Location_Type, 
       ISNULL([Low Fat], 0) AS Low_Fat, 
       ISNULL([Regular], 0) AS Regular
FROM 
(
    SELECT Outlet_Location_Type, Item_Fat_Content, 
           CAST(SUM(Total_Sales) AS DECIMAL(10,2)) AS Total_Sales
    FROM blinkit_data
    GROUP BY Outlet_Location_Type, Item_Fat_Content
) AS SourceTable
PIVOT 
(
    SUM(Total_Sales) 
    FOR Item_Fat_Content IN ([Low Fat], [Regular])
) AS PivotTable
ORDER BY Outlet_Location_Type;

---Total Sales by Outlet Establishment
SELECT 
	Outlet_Establishment_Year, 
	CAST(SUM(Total_Sales) AS DECIMAL(10,2)) AS Total_Sales
FROM blinkit_data
GROUP BY Outlet_Establishment_Year
ORDER BY Outlet_Establishment_Year;
````
### Chart's Requirements
#### Percentage of Sales by Outlet Size
###### - Objective: Analyze the correlation between outlet size and total sales
#### Sales by Outlet Location
###### - Objective: Assess the geographic distribution of sales across different location.
#### All Metrics by Outlet Type
###### - Objective: Provide a comprehensive view of all key metrics (Total Sales, Average Sales, Number of items, Average Rating) broken down by different outlet types.

```` 
--Percentage of Sales by Outlet Size
SELECT 
    Outlet_Size, 
    CAST(SUM(Total_Sales) AS DECIMAL(10,2)) AS Total_Sales,
    CAST((SUM(Total_Sales) * 100.0 / SUM(SUM(Total_Sales)) OVER()) AS DECIMAL(10,2)) AS Sales_Percentage
FROM blinkit_data
GROUP BY Outlet_Size
ORDER BY Total_Sales DESC;

---Sales by Outlet Location
SELECT 
	Outlet_Location_Type, 
	CAST(SUM(Total_Sales) AS DECIMAL(10,2)) AS Total_Sales
FROM blinkit_data
GROUP BY Outlet_Location_Type
ORDER BY Total_Sales DESC;

---All Metrics by Outlet Type:
SELECT Outlet_Type, 
CAST(SUM(Total_Sales) AS DECIMAL(10,2)) AS Total_Sales,
		CAST(AVG(Total_Sales) AS DECIMAL(10,0)) AS Avg_Sales,
		COUNT(*) AS No_Of_Items,
		CAST(AVG(Rating) AS DECIMAL(10,2)) AS Avg_Rating,
		CAST(AVG(Item_Visibility) AS DECIMAL(10,2)) AS Item_Visibility
FROM blinkit_data
GROUP BY Outlet_Type
ORDER BY Total_Sales DESC;


````

                              
                  










