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
![image](https://github.com/user-attachments/assets/aaf34866-b6aa-4e69-904c-ae58c46f4a60)

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
````
![image](https://github.com/user-attachments/assets/0de84fe3-f8b6-4010-ba5a-352f5240c10f)

````
--- AVERAGE SALES
SELECT 
	CAST(AVG(Total_Sales) AS DECIMAL(10,1)) AS Avg_Sales
FROM blinkit_data;
````
![image](https://github.com/user-attachments/assets/1edb5f4a-e4e5-4224-8a11-977ffb6f1b1a)

````
---TOTAL NUMBER OF ITEMS
SELECT
	COUNT(*) AS No_of_items
FROM blinkit_data;
````
![image](https://github.com/user-attachments/assets/8ee23462-4a63-46d5-a516-db9e3dd23317)

````
--- AVERAGE RATING
SELECT 
	CAST(AVG(Rating) AS DECIMAL(10,2)) AS Avg_rating
FROM blinkit_data;
````
 ![image](https://github.com/user-attachments/assets/54c6c051-ca61-43c2-9d14-a5fcf5adc7e4)

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
````
![image](https://github.com/user-attachments/assets/d6d7f8ef-2a2c-42ce-9df8-3421c498f75c)

````
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
````
![image](https://github.com/user-attachments/assets/de99e905-3bc0-4385-b06f-d559697bda76)

````
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
````
![image](https://github.com/user-attachments/assets/1b6706fe-fe53-4c82-9040-28ef58f0b32a)

````
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
````
![image](https://github.com/user-attachments/assets/ffb80551-f507-4026-a083-0aeb7e9d51b1)

````
---Total Sales by Outlet Establishment
SELECT 
	Outlet_Establishment_Year, 
	CAST(SUM(Total_Sales) AS DECIMAL(10,2)) AS Total_Sales
FROM blinkit_data
GROUP BY Outlet_Establishment_Year
ORDER BY Outlet_Establishment_Year;
````
![image](https://github.com/user-attachments/assets/500db4f5-6762-4b3c-b85e-bbfee4dd7d31)

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
````
![image](https://github.com/user-attachments/assets/b37709c6-3701-45ae-86b5-cad73cc5ecfe)

````
---Sales by Outlet Location
SELECT 
	Outlet_Location_Type, 
	CAST(SUM(Total_Sales) AS DECIMAL(10,2)) AS Total_Sales
FROM blinkit_data
GROUP BY Outlet_Location_Type
ORDER BY Total_Sales DESC;
````
![image](https://github.com/user-attachments/assets/5f915698-5d20-40a3-b367-9f8ce8d06043)

````
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
![image](https://github.com/user-attachments/assets/64776275-3ace-4c83-b0e5-585633807596)

                              
                  










