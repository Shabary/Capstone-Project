
## Capstone Project

Data Analysis Project for a Classic Car selling Company- Axon
Sales Performance Report Using SQL

About Dataset
This Project provided by Axon company, consists of 8 tables which contains information about it’s Customers, products, Orders, Payments, Employees and Offices.
The Dataset contains transaction reports of three years from 2003 to 2005.
All data for this Project is saved in a MYSQL database schema named classicmodels. 

MySQL Sample Database Schema
The MySQL sample database schema consists of the following 8 tables:
•	Customers: stores customer’s data.
•	Products: stores a list of scale model cars.
•	ProductLines: stores a list of product line categories.
•	Orders: stores sales orders placed by customers.
•	OrderDetails: stores sales order line items for each sales order.
•	Payments: stores payments made by customers based on their accounts.
•	Employees: stores all employee information as well as the organization structure such as who reports to whom.
•	Offices: stores sales office data

What is the task given?
Using this dataset the Management and Sales Team of Axon company wants to know:
1.	Accurate and UpToDate Sales Report
2.	Extract insights to improve it’s sales
3.	Enable management to access the dashboards and Reports in real-time to make decisions.
4.	Reports and Dashboards should be user friendly.
5.	Sales performance over time.
6.	Customer demographics
7.	Product Popularity
8.	Top 5 customers
9.	To improve decision making process.

Let’s solve these problems for the company!

To get the desired results, we will first perform some advanced analytics: 
Using SQL, we will perform advanced analytics on the sales data to extract insights and inform decision-making. This will involve tasks such as creating pivot tables, running queries, and creating views.
To derive the above insights, we will first focus on 4 problem statements-
1. Order Numbers and Total Sales by Years


SELECT YEAR(orderDate) years,
SUM(sales) sales,
COUNT(status) 'number of orders'
FROM classicmodels.orderdetails JOIN classicmodels.orders ON 
orderdetails.orderNumber = orders.orderNumber
WHERE status = 'Shipped'
GROUP BY 1;
orderDate field has a date format, we can get the year part of date format by using YEAR() function. Next, we use the SUM() function to get total sales, then COUNT() function to get the number of order. We have also used WHERE clause to filter status of the order just for Shipped orders that are completed. Finally, to get the value by years, we should put GROUP BY 1 on the query since year field is located in the first column.

Output:
2003	3223095.80	1019
2004	4300602.99	1353
2005	1341395.85	399
Fig1. Number of sales and order

We can see, total sales of Axon company have changed over the years. The highest total sales were in 2004 and it has gone down very low in year 2005 which is not good for a company’s growth. We have to make some immediate changes in Sales strategy for the company to be up and running otherwise it will be a huge loss for the company and also lead to shut down. As we can see a drastic decrease in the number of orders in the year 2005.

2. Total Sales by productCode on 2003 & 2004

SELECT *, ROUND((sales2004-sales2003)*100/sales2004,1) 'growth sales (%)'
FROM(SELECT productCode,
     SUM( IF(YEAR(orderDate) = 2003, sales, 0)) sales2003,
     SUM( IF(YEAR(orderDate) = 2004, sales, 0)) sales2004
     FROM classicmodels.orderdetails JOIN classicmodels.orders ON 
orderdetails.orderNumber = orders.orderNumber
WHERE status = 'Shipped'
GROUP BY productCode
) product_code
ORDER BY 4 DESC;

Here the pivot table was used to compare the total Sales in 2003 with 2004.We can use SUM() function followed by IF() function to do it. SUM() is used to get the total sales and IF() used to filter by year that we want to specify.

Output:
PCode             S2003	S2004 	growth sales(%)
S700_2834	27525.69	56357.8	51.2
S32_1374	20829.13	42155.35	50.6
S700_1138	14375.36	28051.75	48.8
S18_3685	49863.32	44572.37	-11.9
S18_1367	20560.62	17362.43	-18.4
S18_1589	43142.40	36343.82	-18.7

Mostly the growth in sales are shown by a positive value. If we look at the above output we will see that few products such as ATA: B757-300, 1997 BMW F650 ST and The Schooner Bluenose have got increase in Sales in year 2004 compared to 2003. However there are few products which got a decline in sales in year 2004 compared to previous year which are shown by a negative value under growth sales(%) such as 1965 Aston Martin DB5, 1936 Mercedes-Benz 500K Special Roadster and 1948 Porsche Type 356 Roadster.

3. Customers Transactions per Year

SELECT YEAR(orderDate) years,
 COUNT(DISTINCT customerName) 'Number of customer'
 FROM orders JOIN customers 
 ON orders.customerNumber = customers.customerNumber
 WHERE status = 'Shipped'
 GROUP BY years;


The calculation of customers number for each year doesn’t involve the duplicate value. That is why DISTINCT is used in this query for getting the unique value of customers number.

Output:
Years	No. of customers
2003	73
2004	87
2005	36

Fig3. No. of customers by year

The number of customers has changed significantly and we see a huge decline in the year 2005 compared to 2004. The company started good and gained customers in 2004 from 2003, however could not sustain the growth in the following Year.

4. New customers over the years


SELECT YEAR(first_order) years,
   COUNT(customerName) 'new customers'
   FROM(
        SELECT customerName,
        MIN(orderDate) first_order
        FROM orders JOIN customers 
		ON orders.customerNumber = customers.customerNumber
        WHERE status = 'Shipped'
        GROUP BY 1) first
   GROUP BY 1;

To get the number of new customers for each year, we only need the data that shows the first time transaction from each customer. We can get it by applying MIN() function on the first_order field then calculate the number of customers.
Output:
	Years			new customers
	2003			73
2004			25

Fig4. New customers by year

The growth of new customers for each year is decreasing sharply. It gets extreme in 2005 that there are no new customers. If we go back to the previous Fig3. We can see that the customers remained in the year 2004 but gradually left in 2005.This informs us that many previous customers have left in year 2005, to be precise approximately 50% of customers have left and 50% have remained.

5. Total Sales by productCode on 2004 & 2005

SELECT *, ROUND((sales2004-sales2005)*100/sales2004,1) 'sales reduction (%)'
FROM(SELECT productCode,
     SUM( IF(YEAR(orderDate) = 2004, sales, 0)) sales2004,
     SUM( IF(YEAR(orderDate) = 2005, sales, 0)) sales2005
     FROM classicmodels.orderdetails JOIN classicmodels.orders ON 
orderdetails.orderNumber = orders.orderNumber 
WHERE status = 'Shipped'
GROUP BY productCode
) product_code
ORDER BY 4 DESC;

Here the pivot table was used to compare the total Sales in 2005 with 2004.We can use SUM() function followed by IF() function to do it. SUM() is used to get the total sales and IF() used to filter by year that we want to specify.

Output:
productCode	sales2004	sales2005	sales reduction(%)
S18_2248		19313.80	1372.95	92.9
S18_4409		31092.23	2503.36	91.9
S18_4933		18340.95	1607.76	91.2
S24_1578		42111.85	24921.49	40.8
S24_3856		47973.85	31432.14	34.5
S12_4675		35341.40	29567.27	16.3

Fig5. Sales reduction(%) over years

As we observe the above output, we can conclude that there are few product categories that have decline in popularity from 2004 to 2005 such as 1911 Ford Town Car, 1932 Alfa Romeo 8C2300 Spider Sport and 1957 Ford Thunderbird. On the other hand, there are few product categories that are still doing well such as 1969 Dodge Charger , 1956 Porsche 356A Coupe and 1997 BMW R 1100 S 

Summary
According to data analysis that we have done in this section, we can conclude that :
1.	The total sales and order number of Axon company are fluctuating as overall, where the highest total sales happened in 2004. Meanwhile, the number of orders decreased very significantly except in 2004 where we saw an increase.
2.	Total sales based on the category of products got increasing between 2003 and 2004 as overall. But there some of them that got sales decreasing, they are1965 Aston Martin DB5, 1936 Mercedes-Benz 500K Special Roadster and 1948 Porsche Type 356 Roadster.
3.	The number of customers over the years tends to be decreasing, it is around 73-87 and then in 2005 with a sharp decrease to 36 only, which is around 50% of the customers.
4.	The number of new customers decreasing over the years, with the lowest number of new customers is 0 in 2005, which is quite working for Axon Company as they need to make some changes immediately in the marketing strategy to stabilize their lost position in the market.
5.	In terms of Product Popularity -These products like 1969 Dodge Charger , 1956 Porsche 356A Coupe and 1997 BMW R 1100 S Still doing good business. So the company should focus more on these items to get them in stock instead of 1911 Ford Town Car, 1932 Alfa Romeo 8C2300 Spider Sport and 1957 Ford Thunderbird as they are no longer very popular with customers.

To successfully complete this project the next tool we have used is Microsoft PowerBI , and followed a structured approach. Below are the detailed steps, along with explanations of the formulas and graphs used in Power BI. 
This technology- PowerBI, can be used to extract, clean, and analyze sales data and build interactive dashboards and reports. They are widely used in the field of Business Intelligence (BI) and can be leveraged to solve a variety of data-related problems and derive valuable insights from the data.
Step 1: Data Import and Integration
•	We have imported the data from MYSQL database after connecting power BI to MYSQL database with the help of connectors, entering the server details, database name, and credentials.
o	.
•	Data Cleaning and Transformation: 

•	We have removed duplicates, handled missing values, and performed data type conversions.
•	Merged relevant tables using relationships (e.g., connecting Orders to Customers and Products).
•	Created calculated columns for additional insights if needed (e.g., revenue per order).
Step 2: Building Interactive Dashboards
Dashboard 1: Sales Performance Over Time
1.	Line Chart:
•	X-axis: Time (e.g., Order Date).
•	Y-axis: Total Sales.
•	This chart visualizes sales trends over time.
2.	Card Visuals:
•	We have displayed total sales, average sales, and other key metrics.
3.	Slicers:
•	Data was filtered by time period (e.g., month, quarter, year).


 Dashboard 2: Customer Demographics
1.	Map Visual:
•	We have displayed customer locations.
•	Used color saturation and size to represent sales volume.
2.	Pie Chart:
•	Sales data was further divided by customer segments (e.g., region, industry).
3.	Table:
•	Top customers were listed and key metrics (e.g., revenue, number of orders).
Dashboard 3: Product Popularity
1.	Bar Chart:
•	Displayed top-selling products.
•	Used color and pattern to differentiate product lines.
2.	Donut Chart:
•	Illustrated the distribution of sales among product lines.
Dashboard 4: Top 5 Customers
1.	Table:
•	Top 5 customers were listed based on sales.
•	Relevant details like customer name, total sales, and contact information were also included.
Step 3: SQL for Advanced Analytics
1.	Writing SQL Queries:
•	Used SQL to perform advanced analytics.
•	Example queries: Identifying underperforming products, analyzing customer buying patterns, etc.
2.	Imported SQL Results into Power BI:
•	Connected Power BI to the database and imported the results of SQL queries.
Step 4: User-Friendly Design
1.	Formatting:
•	Ensured a consistent color scheme, fonts, and layout.
•	Used tooltips for additional information.
2.	Interactivity:
•	Implemented slicers, drill-through options, and tooltips for a user-friendly experience.
Step 5: Real-Time Access and Decision-Making

To get the desired results for real Time access for management of Axon company and the Sales Team the below steps can be followed:

1.	Publish to Power BI Service:
•	Publish the Power BI report to the Power BI Service.
2.	Share and Collaborate:
•	Share the report with relevant stakeholders.
•	Schedule data refresh for real-time access.



To calculate total sales and average sales to display on a card in Power BI, we have used Data Analysis Expressions (DAX) in Power BI.
Calculating Total Sales:
We have used the following DAX formula to calculate Total Sales:

TotalSales= SUM(‘order’[Quantity] * ‘order’[unitPrice])'[Sales]) 

This formula uses the SUM function to add up the "Sales" column for each row in the table.
Calculating Average Sales:
We have entered the following DAX formula to calculate Average Sales:
Sales]) 
This formula uses the AVERAGE function to calculate the average of the "Sales" column for each row in the table.
Displaying Measures on a Card

Calculating Product Popularity:

We have used the following DAX formula to calculate Product Popularity by creating a measure for the same.
DAX
ProductPopularity= CALCULATE(SUM(‘order’[TotalSales]), ‘Products’)

By following the above steps, we have created a comprehensive Power BI project that addresses the needs of Axon Company and enables effective sales data management and analysis.

			
			
			
			
			
			
			
			
			
			
			
			
			
			
			
			
			
			
			
			
			
			
			
			
			
			
			
			
			
			
			
			
			
			
			
			
			
			
			
			
			
			
			
			
			
			
			
			
			
			
			
			
			
			
			
			
			
			
			
			
			
			
			
			
			
			
			
			
			
			
			
			
			
			
			
			
			
			
			
			
			
			
			
			
			
			
			
			
			
			
			
			
			
			
			
			
			
			
			
			
			
			
			
			
			
			
			
			
			
			
				


				
				
				
				
				
				
				
				
				
				
				
				
				
				
				
				
				
				
				
				
				
				
				
				
				
				
				
				
				
				
				
				
				
				
				
				
				
				
				
				
				
				
				
				
				
				
				
				
				
				
				
				
				
				
				
				
				
				
				
				
				
				
				
				
				
				
				
				
				
				
				
				
				
				
				
				
				
				
				
				
				
				
				
				
				
				
				
				
				
				
				
				
				
				
				
				
				
				
				
				
				
				
				
				
				
				
				
				
				

