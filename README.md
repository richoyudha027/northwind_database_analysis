# Northwind Database Analysis  
Source: [en.wikiversity.org/wiki/Database_Examples/Northwind/MySQL]([url](https://en.wikiversity.org/wiki/Database_Examples/Northwind/MySQL))   
  
This repository contains a collection of SQL queries performed on the classic Northwind database to analyze sales performance, customer behavior, employee productivity, and product trends.  The project demonstrates practical SQL skills including data retrieval, aggregation, joins, subqueries, and analytical thinking for business insights.

## 1. Product Count per Category
Understanding product distribution across categories helps inventory planning and identifies which categories need expansion.

```
SELECT 
    c.CategoryID,
    c.CategoryName,
    COUNT(p.ProductID) AS NumberOfProducts
FROM Categories c
LEFT JOIN Products p ON c.CategoryID = p.CategoryID
GROUP BY c.CategoryID, c.CategoryName
ORDER BY NumberOfProducts DESC;
```
  
**Result:**

| CategoryID | CategoryName     | NumberOfProducts |
|------------|------------------|------------------|
| 3          | Confections      | 13               |
| 8          | Seafood          | 12               |
| 2          | Condiments       | 12               |
| 1          | Beverages        | 12               |
| 4          | Dairy Products   | 10               |
| 5          | Grains/Cereals   | 7                |
| 6          | Meat/Poultry     | 6                |
| 7          | Produce          | 5                |

**Insight:**
- Confections has the highest number of products, indicating a broad product variety.
- Produce and Meat/Poultry have the lowest product counts, suggesting potential opportunities for category expansion.
    
## 2. Average Price per Category
Comparing average prices across categories reveals pricing strategy and helps identify premium vs budget categories.

```
SELECT 
    c.CategoryID,
    c.CategoryName,
    COUNT(p.ProductID) AS TotalProducts,
    ROUND(AVG(p.Price), 2) AS AvgPrice,
    ROUND(MIN(p.Price), 2) AS MinPrice,
    ROUND(MAX(p.Price), 2) AS MaxPrice
FROM Categories c
LEFT JOIN Products p ON c.CategoryID = p.CategoryID
GROUP BY c.CategoryID, c.CategoryName
ORDER BY AvgPrice DESC;
```
  
**Result:**
| CategoryID | CategoryName     | TotalProducts | AvgPrice | MinPrice | MaxPrice |
|------------|------------------|---------------|----------|----------|----------|
| 6          | Meat/Poultry     | 6             | 54.00    | 7.00     | 124.00   |
| 1          | Beverages        | 12            | 38.08    | 5.00     | 264.00   |
| 7          | Produce          | 5             | 32.40    | 10.00    | 53.00    |
| 4          | Dairy Products   | 10            | 28.90    | 3.00     | 55.00    |
| 3          | Confections      | 13            | 25.15    | 9.00     | 81.00    |
| 2          | Condiments       | 12            | 23.08    | 10.00    | 44.00    |
| 8          | Seafood          | 12            | 20.75    | 6.00     | 63.00    |
| 5          | Grains/Cereals   | 7             | 20.29    | 7.00     | 38.00    |
  
**Insight:**
- Meat/Poultry has the highest average price, indicating a premium pricing segment despite having a relatively small number of products.
- Beverages show the widest price range, suggesting a diverse portfolio spanning low-cost to premium items.
  
## 3. Shipping Performance Analysis
Evaluating which shipper handles most orders and revenue helps negotiate better rates and optimize logistics partnerships.

```
SELECT 
    s.ShipperID,
    s.ShipperName,
    COUNT(o.OrderID) AS TotalOrders,
    COUNT(DISTINCT o.CustomerID) AS UniqueCustomers,
    SUM(od.Quantity) AS TotalUnitShipped,
    ROUND(SUM(od.Quantity * p.Price), 2) AS TotalRevenueHandled,
    ROUND(SUM(od.Quantity * p.Price) / COUNT(o.OrderID), 2) AS AvgOrderValue
FROM Shippers s
LEFT JOIN Orders o ON s.ShipperID = o.ShipperID
LEFT JOIN OrderDetails od ON o.OrderID = od.OrderID
LEFT JOIN Products p ON od.ProductID = p.ProductID
GROUP BY s.ShipperID, s.ShipperName
ORDER BY TotalRevenueHandled DESC;
```
**Result:**
| ShipperID | ShipperName      | TotalOrders | UniqueCustomers | TotalUnitShipped | TotalRevenueHandled | AvgOrderValue |
|-----------|------------------|-------------|------------------|------------------|---------------------|---------------|
| 2         | United Package   | 188         | 45               | 4802             | 155,347.00          | 826.31        |
| 3         | Federal Shipping | 181         | 42               | 4366             | 135,876.00          | 750.70        |
| 1         | Speedy Express   | 149         | 40               | 3575             | 95,843.00           | 643.24        |

**Insight:**
- United Package handles the highest order volume and revenue, making it the most impactful logistics partner overall.
- Despite similar order volumes, Federal Shipping generates lower average order value compared to United Package, indicating differences in customer or shipment profiles.

## 4. Customer Segmentation by Order Frequency
Segmenting customers helps prioritize marketing efforts and identify high-value customers for retention programs.

```
SELECT 
    c.CustomerID,
    c.CustomerName,
    c.Country,
    COUNT(o.OrderID) AS TotalOrders,
    CASE 
        WHEN COUNT(o.OrderID) >= 10 THEN 'High Value'
        WHEN COUNT(o.OrderID) >= 5 THEN 'Medium Value'
        WHEN COUNT(o.OrderID) >= 1 THEN 'Low Value'
        ELSE 'Inactive'
    END AS CustomerSegment
FROM Customers c
LEFT JOIN Orders o ON c.CustomerID = o.CustomerID
GROUP BY c.CustomerID, c.CustomerName, c.Country
ORDER BY TotalOrders DESC;
```

**Result:**
| CustomerID | CustomerName                    | Country    | TotalOrders | CustomerSegment |
|------------|----------------------------------|------------|-------------|-----------------|
| 20         | Ernst Handel                     | Austria    | 10          | High Value      |
| 63         | QUICK-Stop                       | Germany    | 7           | Medium Value    |
| 65         | Rattlesnake Canyon Grocery       | USA        | 7           | Medium Value    |
| 87         | Wartian Herkku                   | Finland    | 7           | Medium Value    |
| 37         | Hungry Owl All-Night Grocers     | Ireland    | 6           | Medium Value    |
| 75         | Split Rail Beer & Ale            | USA        | 6           | Medium Value    |
| 51         | Mère Paillarde                   | Canada     | 5           | Medium Value    |
| 46         | LILA-Supermercado                | Venezuela  | 5           | Medium Value    |
| 41         | La maison d'Asie                 | France     | 5           | Medium Value    |
| 61         | Que Delícia                      | Brazil     | 4           | Low Value       |
| 24         | Folk och fä HB                   | Sweden     | 4           | Low Value       |
| 25         | Frankenversand                   | Germany    | 4           | Low Value       |
| 10         | Bottom-Dollar Marketse           | Canada     | 4           | Low Value       |
| 80         | Tortuga Restaurante              | Mexico     | 4           | Low Value       |
| 86         | Die Wandernde Kuh                | Germany    | 4           | Low Value       |
| 55         | Old World Delicatessen           | USA        | 4           | Low Value       |
| 71         | Save-a-lot Markets               | USA        | 4           | Low Value       |
| 7          | Blondel père et fils             | France     | 4           | Low Value       |
| 66         | Reggiani Caseifici               | Italy      | 3           | Low Value       |
| 72         | Seven Seas Imports               | UK         | 3           | Low Value       |
| 9          | Bon app'                         | France     | 3           | Low Value       |
| 36         | Hungry Coyote Import Store       | USA        | 3           | Low Value       |
| 21         | Familia Arquibaldo               | Brazil     | 3           | Low Value       |
| 69         | Romero y tomillo                 | Spain     | 3           | Low Value       |
| 5          | Berglunds snabbköp               | Sweden    | 3           | Low Value       |
  
**Insight:**
- The customer base is heavily skewed toward Low and Medium Value segments, indicating that revenue depends on retaining and upgrading frequent mid-tier customers.
- Only one High Value customer exists (Ernst Handel), highlighting a potential concentration risk and an opportunity to develop more high-frequency buyers.
  
## 5. Top 10 Products by Revenue
Identifying top revenue-generating products helps focus inventory and marketing resources on high-impact items.

```
SELECT 
    p.ProductID,
    p.ProductName,
    c.CategoryName,
    SUM(od.Quantity) AS TotalUnitsSold,
    ROUND(p.Price, 2) AS UnitPrice,
    ROUND(SUM(od.Quantity * p.Price), 2) AS TotalRevenue
FROM Products p
JOIN OrderDetails od ON p.ProductID = od.ProductID
JOIN Categories c ON p.CategoryID = c.CategoryID
GROUP BY p.ProductID, p.ProductName, c.CategoryName, p.Price
ORDER BY TotalRevenue DESC
LIMIT 10;
```

**Result:**
| ProductID | ProductName               | CategoryName    | TotalUnitsSold | UnitPrice | TotalRevenue |
|----------:|---------------------------|-----------------|----------------|-----------|--------------|
| 38        | Côte de Blaye             | Beverages       | 239            | 264.00    | 63096.00     |
| 29        | Thüringer Rostbratwurst   | Meat/Poultry    | 168            | 124.00    | 20832.00     |
| 59        | Raclette Courdavault      | Dairy Products  | 346            | 55.00     | 19030.00     |
| 62        | Tarte au sucre            | Confections     | 325            | 49.00     | 15925.00     |
| 60        | Camembert Pierrot         | Dairy Products  | 430            | 34.00     | 14620.00     |
| 17        | Alice Mutton              | Meat/Poultry    | 331            | 39.00     | 12909.00     |
| 56        | Gnocchi di nonna Alice    | Grains/Cereals  | 269            | 38.00     | 10222.00     |
| 72        | Mozzarella di Giovanni    | Dairy Products  | 270            | 35.00     | 9450.00      |
| 63        | Vegie-spread              | Condiments      | 209            | 44.00     | 9196.00      |
| 28        | Rössle Sauerkraut         | Produce         | 189            | 46.00     | 8694.00      |
  
**Insight:**
- Revenue is primarily driven by high-priced premium products rather than sales volume, as *Côte de Blaye* generates the highest total revenue despite not having the highest units sold.
- Several products achieve high sales volume with relatively low unit prices, indicating potential opportunities for revenue growth through pricing optimization or product bundling strategies.
  
## 6. Revenue Analysis by Country
Geographic revenue analysis reveals market opportunities and helps prioritize international expansion strategies.

```
SELECT 
    c.Country,
    COUNT(DISTINCT c.CustomerID) AS TotalCustomers,
    COUNT(DISTINCT o.OrderID) AS TotalOrders,
    ROUND(SUM(od.Quantity * p.Price), 2) AS TotalRevenue,
    ROUND(SUM(od.Quantity * p.Price) / COUNT(DISTINCT c.CustomerID), 2) AS RevenuePerCustomer,
    ROUND(SUM(od.Quantity * p.Price) / COUNT(DISTINCT o.OrderID), 2) AS AvgOrderValue
FROM Customers c
JOIN Orders o ON c.CustomerID = o.CustomerID
JOIN OrderDetails od ON o.OrderID = od.OrderID
JOIN Products p ON od.ProductID = p.ProductID
GROUP BY c.Country
ORDER BY TotalRevenue DESC;
```

**Result:**
| Country     | TotalCustomers | TotalOrders | TotalRevenue | RevenuePerCustomer | AvgOrderValue |
|-------------|----------------|-------------|--------------|--------------------|---------------|
| USA         | 8              | 29          | 69,722.00    | 8,715.25           | 2,404.21      |
| Austria     | 2              | 13          | 51,687.00    | 25,843.50          | 3,975.92      |
| Germany     | 9              | 25          | 47,316.00    | 5,257.33           | 1,892.64      |
| Brazil      | 9              | 19          | 40,272.00    | 4,474.67           | 2,119.58      |
| Canada      | 2              | 9           | 31,402.00    | 15,701.00          | 3,489.11      |
| France      | 7              | 18          | 29,559.00    | 4,222.71           | 1,642.17      |
| Denmark     | 2              | 4           | 17,954.00    | 8,977.00           | 4,488.50      |
| UK          | 6              | 12          | 16,764.00    | 2,794.00           | 1,397.00      |
| Ireland     | 1              | 6           | 15,405.00    | 15,405.00          | 2,567.50      |
| Venezuela   | 4              | 9           | 13,559.00    | 3,389.75           | 1,506.56      |
| Sweden      | 2              | 7           | 9,736.00     | 4,868.00           | 1,390.86      |
| Switzerland | 2              | 4           | 8,109.00     | 4,054.50           | 2,027.25      |
| Belgium     | 1              | 2           | 8,075.00     | 8,075.00           | 4,037.50      |
| Finland     | 2              | 8           | 6,478.00     | 3,239.00           | 809.75        |
| Mexico      | 5              | 9           | 5,882.00     | 1,176.40           | 653.56        |
| Italy       | 3              | 7           | 4,339.00     | 1,446.33           | 619.86        |
| Spain       | 4              | 7           | 4,316.00     | 1,079.00           | 616.57        |
| Portugal    | 2              | 5           | 4,169.00     | 2,084.50           | 833.80        |
| Norway      | 1              | 1           | 1,341.00     | 1,341.00           | 1,341.00      |
| Poland      | 1              | 1           | 585.00       | 585.00             | 585.00        |
| Argentina   | 1              | 1           | 396.00       | 396.00             | 396.00        |
  
**Insight:**
- Revenue contribution is not solely driven by customer count, as countries like Austria and Canada generate significantly higher revenue per customer despite having fewer customers.
- The USA leads in total revenue due to higher order volume, while several smaller markets show strong average order values, indicating potential for targeted market expansion.
  
## 7. Employee Sales Performance
Employee performance metrics help identify top performers, training needs, and fair commission/bonus calculations.

```
SELECT 
    e.EmployeeID,
    CONCAT(e.FirstName, ' ', e.LastName) AS EmployeeName,
    e.Title,
    COUNT(DISTINCT o.OrderID) AS TotalOrders,
    COUNT(DISTINCT o.CustomerID) AS UniqueCustomers,
    SUM(od.Quantity) AS TotalUnitsSold,
    ROUND(SUM(od.Quantity * p.Price), 2) AS TotalRevenue,
    ROUND(SUM(od.Quantity * p.Price) / COUNT(DISTINCT o.OrderID), 2) AS AvgOrderValue
FROM Employees e
LEFT JOIN Orders o ON e.EmployeeID = o.EmployeeID
LEFT JOIN OrderDetails od ON o.OrderID = od.OrderID
LEFT JOIN Products p ON od.ProductID = p.ProductID
GROUP BY e.EmployeeID, e.FirstName, e.LastName, e.Title
ORDER BY TotalRevenue DESC;
```

**Result:**
| EmployeeID | EmployeeName       | TotalOrders | UniqueCustomers | TotalUnitsSold | TotalRevenue | AvgOrderValue |
|------------|--------------------|-------------|------------------|----------------|--------------|---------------|
| 4          | Margaret Peacock   | 40          | 31               | 3232           | 105,926.00   | 2,648.15      |
| 1          | Nancy Davolio      | 29          | 23               | 1924           | 57,765.00    | 1,991.90      |
| 3          | Janet Leverling    | 31          | 26               | 1725           | 42,823.00    | 1,381.39      |
| 7          | Robert King        | 14          | 14               | 733            | 39,843.00    | 2,845.93      |
| 8          | Laura Callahan     | 27          | 25               | 1293           | 39,341.00    | 1,457.07      |
| 2          | Andrew Fuller      | 20          | 18               | 1315           | 32,559.00    | 1,627.95      |
| 5          | Steven Buchanan    | 11          | 10               | 778            | 27,606.00    | 2,509.64      |
| 6          | Michael Suyama     | 18          | 17               | 1094           | 25,501.00    | 1,416.72      |
| 9          | Anne Dodsworth     | 6           | 6                | 649            | 15,702.00    | 2,617.00      |
| 10         | Adam West          | 0           | 0                | NULL           | NULL         | NULL          |
  
**Insight:**
- Margaret Peacock is the top-performing employee, leading in total orders, customers, and revenue, making her the most impactful contributor overall.
- Employees with fewer orders, such as Robert King and Anne Dodsworth, show high average order values, indicating strong performance in handling high-value transactions.
  
## 8. Monthly Sales Trend Analysis (Year-over-Year)
Time-series analysis reveals seasonality patterns and growth trends for demand forecasting and resource planning.

```
SELECT 
    YEAR(o.OrderDate) AS OrderYear,
    MONTH(o.OrderDate) AS OrderMonth,
    COUNT(DISTINCT o.OrderID) AS TotalOrders,
    COUNT(DISTINCT o.CustomerID) AS UniqueCustomers,
    SUM(od.Quantity) AS TotalUnitsSold,
    ROUND(SUM(od.Quantity * p.Price), 2) AS MonthlyRevenue,
    ROUND(AVG(od.Quantity * p.Price), 2) AS AvgOrderItemValue
FROM Orders o
JOIN OrderDetails od ON o.OrderID = od.OrderID
JOIN Products p ON od.ProductID = p.ProductID
GROUP BY YEAR(o.OrderDate), MONTH(o.OrderDate)
ORDER BY OrderYear, OrderMonth;
```

**Result:**
| OrderYear | OrderMonth | TotalOrders | UniqueCustomers | TotalUnitsSold | MonthlyRevenue | AvgOrderItemValue |
|-----------|------------|-------------|------------------|----------------|----------------|-------------------|
| 1996      | 7          | 22          | 20               | 1462           | 37,826.00      | 641.12            |
| 1996      | 8          | 25          | 18               | 1322           | 33,304.00      | 482.67            |
| 1996      | 9          | 23          | 19               | 1124           | 34,545.00      | 606.05            |
| 1996      | 10         | 26          | 20               | 1738           | 51,672.00      | 707.84            |
| 1996      | 11         | 25          | 21               | 1735           | 62,202.00      | 942.45            |
| 1996      | 12         | 31          | 25               | 2200           | 63,971.00      | 789.77            |
| 1997      | 1          | 33          | 27               | 2401           | 83,566.00      | 983.13            |
| 1997      | 2          | 11          | 11               | 761            | 19,980.00      | 713.57            |
  
**Insight:**
- There is a strong upward sales trend from October 1996 to January 1997, indicating seasonal demand growth toward year-end and early-year periods.
- January 1997 records the highest revenue and average order item value, suggesting not only higher sales volume but also increased spending per transaction.
  
## 9. Top Customer Analysis
Deep customer analysis with multiple metrics helps create targeted retention strategies and identify growth opportunities.

```
WITH CustomerMetrics AS (
    SELECT 
        c.CustomerID,
        c.CustomerName,
        c.Country,
        c.City,
        COUNT(DISTINCT o.OrderID) AS TotalOrders,
        SUM(od.Quantity) AS TotalUnitsPurchased,
        ROUND(SUM(od.Quantity * p.Price), 2) AS TotalRevenue,
        ROUND(AVG(od.Quantity * p.Price), 2) AS AvgOrderItemValue,
        MIN(o.OrderDate) AS FirstOrderDate,
        MAX(o.OrderDate) AS LastOrderDate
    FROM Customers c
    JOIN Orders o ON c.CustomerID = o.CustomerID
    JOIN OrderDetails od ON o.OrderID = od.OrderID
    JOIN Products p ON od.ProductID = p.ProductID
    GROUP BY c.CustomerID, c.CustomerName, c.Country, c.City
)
SELECT 
    CustomerID,
    CustomerName,
    Country,
    City,
    TotalOrders,
    TotalUnitsPurchased,
    TotalRevenue,
    AvgOrderItemValue,
    FirstOrderDate,
    LastOrderDate,
    DATEDIFF(LastOrderDate, FirstOrderDate) AS CustomerLifespanDays
FROM CustomerMetrics
ORDER BY TotalRevenue DESC
LIMIT 10;
```

**Result:**
| CustomerID | CustomerName                   | Country  | City         | TotalOrders | TotalUnitsPurchased | TotalRevenue | AvgOrderItemValue | FirstOrderDate | LastOrderDate | CustomerLifespanDays |
|------------|--------------------------------|----------|--------------|-------------|----------------------|--------------|-------------------|----------------|---------------|----------------------|
| 20         | Ernst Handel                   | Austria  | Graz         | 10          | 1418                 | 35630.00     | 1018.00           | 1996-07-17     | 1997-02-11    | 209                  |
| 51         | Mère Paillarde                 | Canada   | Montréal     | 5           | 422                  | 23434.00     | 1673.86           | 1996-10-17     | 1997-02-07    | 113                  |
| 71         | Save-a-lot Markets             | USA      | Boise        | 4           | 775                  | 22516.00     | 1407.25           | 1996-10-08     | 1997-02-10    | 125                  |
| 65         | Rattlesnake Canyon Grocery     | USA      | Albuquerque  | 7           | 573                  | 18442.00     | 838.27            | 1996-07-22     | 1997-01-01    | 163                  |
| 63         | QUICK-Stop                     | Germany  | Cunewalde    | 7           | 839                  | 18171.00     | 908.55            | 1996-08-05     | 1997-01-17    | 165                  |
| 62         | Queen Cozinha                  | Brazil   | São Paulo    | 2           | 253                  | 17925.00     | 1991.67           | 1996-12-04     | 1997-01-07    | 34                   |
| 59         | Piccolo und mehr               | Austria  | Salzburg     | 3           | 147                  | 16057.00     | 4014.25           | 1996-11-13     | 1997-01-27    | 75                   |
| 37         | Hungry Owl All-Night Grocers   | Ireland  | Cork         | 6           | 565                  | 15405.00     | 733.57            | 1996-09-05     | 1997-01-29    | 146                  |
| 7          | Blondel père et fils           | France   | Strasbourg   | 4           | 367                  | 15268.00     | 1174.46           | 1996-07-25     | 1997-02-05    | 195                  |
| 73         | Simons bistro                  | Denmark  | København    | 2           | 140                  | 14666.00     | 2444.33           | 1996-10-29     | 1997-01-16    | 79                   |
  
**Insight:**
- Long-term customers with higher order frequency, such as *Ernst Handel*, generate the highest total revenue, highlighting the importance of customer retention over time.
- Some customers achieve high revenue with relatively few orders but very high average order values, indicating strong potential for targeted premium-focused sales strategies.
  
## 10. Product Ranking within Categories
Ranking products within categories identifies category leaders, underperformers, and opportunities for product optimization.

```
WITH ProductSales AS (
    SELECT 
        p.ProductID,
        p.ProductName,
        c.CategoryID,
        c.CategoryName,
        p.Price AS UnitPrice,
        SUM(od.Quantity) AS TotalUnitsSold,
        ROUND(SUM(od.Quantity * p.Price), 2) AS TotalRevenue
    FROM Products p
    JOIN Categories c ON p.CategoryID = c.CategoryID
    JOIN OrderDetails od ON p.ProductID = od.ProductID
    GROUP BY p.ProductID, p.ProductName, c.CategoryID, c.CategoryName, p.Price
)
SELECT 
    ProductID,
    ProductName,
    CategoryName,
    UnitPrice,
    TotalUnitsSold,
    TotalRevenue,
    RANK() OVER (PARTITION BY CategoryID ORDER BY TotalRevenue DESC) AS RevenueRankInCategory,
    DENSE_RANK() OVER (ORDER BY TotalRevenue DESC) AS OverallRevenueRank,
    ROUND(TotalRevenue * 100.0 / SUM(TotalRevenue) OVER (PARTITION BY CategoryID), 2) AS PctOfCategoryRevenue
FROM ProductSales
ORDER BY CategoryName, RevenueRankInCategory;
```

**Result:**
| ProductID | ProductName                          | CategoryName | UnitPrice | TotalUnitsSold | TotalRevenue | RevenueRankInCategory | OverallRevenueRank | PctOfCategoryRevenue |
|-----------|--------------------------------------|----------------|-----------|----------------|--------------|-----------------------|--------------------|----------------------|
| 38        | Côte de Blaye                        | Beverages      | 264       | 239            | 63096.00     | 1                     | 1                  | 63.29                |
| 35        | Steeleye Stout                       | Beverages      | 18        | 369            | 6642.00      | 2                     | 17                 | 6.66                 |
| 2         | Chang                                | Beverages      | 19        | 341            | 6479.00      | 3                     | 19                 | 6.50                 |
| 43        | Ipoh Coffee                          | Beverages      | 46        | 136            | 6256.00      | 4                     | 20                 | 6.27                 |
| 39        | Chartreuse verte                     | Beverages      | 18        | 266            | 4788.00      | 5                     | 26                 | 4.80                 |
| 76        | Lakkalikööri                         | Beverages      | 18        | 198            | 3564.00      | 6                     | 34                 | 3.57                 |
| 1         | Chais                                | Beverages      | 18        | 159            | 2862.00      | 7                     | 39                 | 2.87                 |
| 70        | Outback Lager                        | Beverages      | 15        | 164            | 2460.00      | 8                     | 43                 | 2.47                 |
| 34        | Sasquatch Ale                        | Beverages      | 14        | 110            | 1540.00      | 9                     | 52                 | 1.54                 |
| 75        | Rhönbräu Klosterbier                 | Beverages      | 8         | 144            | 1152.00      | 10                    | 58                 | 1.16                 |
| 24        | Guaraná Fantástica                   | Beverages      | 5         | 158            | 790.00       | 11                    | 69                 | 0.79                 |
| 67        | Laughing Lumberjack Lager            | Beverages      | 14        | 5              | 70.00        | 12                    | 77                 | 0.07                 |
| 63        | Vegie-spread                         | Condiments     | 44        | 209            | 9196.00      | 1                     | 9                  | 26.26                |
| 8         | Northwoods Cranberry Sauce           | Condiments     | 40        | 140            | 5600.00      | 2                     | 24                 | 15.99                |
| 65        | Louisiana Fiery Hot Pepper Sauce     | Condiments     | 21        | 175            | 3675.00      | 3                     | 32                 | 10.49                |
| 44        | Gula Malacca                         | Condiments     | 19        | 178            | 3382.00      | 4                     | 36                 | 9.66                 |
| 61        | Sirop d'érable                       | Condiments     | 29        | 106            | 3074.00      | 5                     | 38                 | 8.78                 |
| 5         | Chef Anton's Gumbo Mix               | Condiments     | 21        | 129            | 2709.00      | 6                     | 40                 | 7.73                 |
| 4         | Chef Anton's Cajun Seasoning         | Condiments     | 22        | 107            | 2354.00      | 7                     | 44                 | 6.72                 |
| 66        | Louisiana Hot Spiced Okra            | Condiments     | 17        | 90             | 1530.00      | 8                     | 53                 | 4.37                 |
| 77        | Original Frankfurter grüne Soße      | Condiments     | 13        | 108            | 1404.00      | 9                     | 56                 | 4.01                 |
| 6         | Grandma's Boysenberry Spread         | Condiments     | 25        | 36             | 900.00       | 10                    | 67                 | 2.57                 |
| 3         | Aniseed Syrup                        | Condiments     | 10        | 80             | 800.00       | 11                    | 68                 | 2.28                 |
| 15        | Genen Shouyu                         | Condiments     | 16        | 25             | 400.00       | 12                    | 73                 | 1.14                 |
| 62        | Tarte au sucre                       | Confections    | 49        | 325            | 15925.00     | 1                     | 4                  | 29.10                |
| 20        | Sir Rodney's Marmalade               | Confections    | 81        | 106            | 8586.00      | 2                     | 12                 | 15.69                |
| 26        | Gumbär Gummibärchen                  | Confections    | 31        | 232            | 7192.00      | 3                     | 15                 | 13.14                |
| 16        | Pavlova                              | Confections    | 17        | 338            | 5746.00      | 4                     | 22                 | 10.50                |
| 27        | Schoggi Schokolade                   | Confections    | 44        | 90             | 3960.00      | 5                     | 29                 | 7.24                 |
| 49        | Maxilaku                             | Confections    | 20        | 180            | 3600.00      | 6                     | 33                 | 6.58                 |
| 68        | Scottish Longbreads                  | Confections    | 13        | 199            | 2587.00      | 7                     | 42                 | 4.73                 |
| 19        | Teatime Chocolate Biscuits           | Confections    | 9         | 181            | 1629.00      | 8                     | 51                 | 2.98                 |
| 21        | Sir Rodney's Scones                  | Confections    | 10        | 147            | 1470.00      | 9                     | 55                 | 2.69                 |
| 50        | Valkoinen suklaa                     | Confections    | 16        | 70             | 1120.00      | 10                    | 59                 | 2.05                 |
| 47        | Zaanse koeken                        | Confections    | 10        | 101            | 1010.00      | 11                    | 63                 | 1.85                 |
| 25        | NuNuCa Nuß-Nougat-Creme              | Confections    | 14        | 71             | 994.00       | 12                    | 64                 | 1.82                 |
| 48        | Chocolate                            | Confections    | 13        | 70             | 910.00       | 13                    | 66                 | 1.66                 |
| 59        | Raclette Courdavault                 | Dairy Products | 55        | 346            | 19030.00     | 1                     | 3                  | 26.98                |
| 60        | Camembert Pierrot                    | Dairy Products | 34        | 430            | 14620.00     | 2                     | 5                  | 20.73                |
| 72        | Mozzarella di Giovanni               | Dairy Products | 35        | 270            | 9450.00      | 3                     | 8                  | 13.40                |
| 71        | Fløtemysost                          | Dairy Products | 22        | 336            | 7392.00      | 4                     | 14                 | 10.48                |
| 69        | Gudbrandsdalsost                     | Dairy Products | 36        | 184            | 6624.00      | 5                     | 18                 | 9.39                 |
| 31        | Gorgonzola Telino                    | Dairy Products | 13        | 458            | 5954.00      | 6                     | 21                 | 8.44                 |
| 11        | Queso Cabrales                       | Dairy Products | 21        | 182            | 3822.00      | 7                     | 30                 | 5.42                 |
| 32        | Mascarpone Fabioli                   | Dairy Products | 32        | 52             | 1664.00      | 8                     | 50                 | 2.36                 |
| 12        | Queso Manchego La Pastora            | Dairy Products | 38        | 27             | 1026.00      | 9                     | 61                 | 1.45                 |
| 33        | Geitost                              | Dairy Products | 3         | 316            | 948.00       | 10                    | 65                 | 1.34                 |
| 56        | Gnocchi di nonna Alice               | Grains/Cereals | 38        | 269            | 10222.00     | 1                     | 7                  | 45.70                |
| 64        | Wimmers gute Semmelknödel            | Grains/Cereals | 33        | 167            | 5511.00      | 2                     | 25                 | 24.64                |
| 57        | Ravioli Angelo                       | Grains/Cereals | 20        | 168            | 3360.00      | 3                     | 37                 | 15.02                |
| 23        | Tunnbröd                             | Grains/Cereals | 9         | 165            | 1485.00      | 4                     | 54                 | 6.64                 |
| 42        | Singaporean Hokkien Fried Mee        | Grains/Cereals | 14        | 77             | 1078.00      | 5                     | 60                 | 4.82                 |
| 22        | Gustaf's Knäckebröd                  | Grains/Cereals | 21        | 18             | 378.00       | 6                     | 74                 | 1.69                 |
| 52        | Filo Mix                             | Grains/Cereals | 7         | 48             | 336.00       | 7                     | 75                 | 1.50                 |
| 29        | Thüringer Rostbratwurst              | Meat/Poultry   | 124       | 168            | 20832.00     | 1                     | 2                  | 40.34                |
| 17        | Alice Mutton                         | Meat/Poultry   | 39        | 331            | 12909.00     | 2                     | 6                  | 25.00                |
| 53        | Perth Pasties                        | Meat/Poultry   | 33        | 251            | 8283.00      | 3                     | 13                 | 16.04                |
| 55        | Pâté chinois                         | Meat/Poultry   | 24        | 238            | 5712.00      | 4                     | 23                 | 11.06                |
| 54        | Tourtière                            | Meat/Poultry   | 7         | 280            | 1960.00      | 5                     | 46                 | 3.80                 |
| 9         | Mishi Kobe Niku                      | Meat/Poultry   | 97        | 20             | 1940.00      | 6                     | 47                 | 3.76                 |
| 28        | Rössle Sauerkraut                    | Produce        | 46        | 189            | 8694.00      | 1                     | 10                 | 37.09                |
| 51        | Manjimup Dried Apples                | Produce        | 53        | 163            | 8639.00      | 2                     | 11                 | 36.86                |
| 14        | Tofu                                 | Produce        | 23        | 152            | 3496.00      | 3                     | 35                 | 14.92                |
| 74        | Longlife Tofu                        | Produce        | 10        | 186            | 1860.00      | 4                     | 48                 | 7.94                 |
| 7         | Uncle Bob's Organic Dried Pears      | Produce        | 30        | 25             | 750.00       | 5                     | 70                 | 3.20                 |
| 18        | Carnarvon Tigers                     | Seafood        | 63        | 106            | 6678.00      | 1                     | 16                 | 22.53                |
| 40        | Boston Crab Meat                     | Seafood        | 18        | 256            | 4608.00      | 2                     | 27                 | 15.55                |
| 30        | Nord-Ost Matjeshering                | Seafood        | 26        | 170            | 4420.00      | 3                     | 28                 | 14.91                |
| 36        | Inlagd Sill                          | Seafood        | 19        | 198            | 3762.00      | 4                     | 31                 | 12.69                |
| 10        | Ikura                                | Seafood        | 31        | 85             | 2635.00      | 5                     | 41                 | 8.89                 |
| 58        | Escargots de Bourgogne               | Seafood        | 13        | 155            | 2015.00      | 6                     | 45                 | 6.80                 |
| 46        | Spegesild                            | Seafood        | 12        | 145            | 1740.00      | 7                     | 49                 | 5.87                 |
| 41        | Jack's New England Clam Chowder      | Seafood        | 10        | 139            | 1390.00      | 8                     | 57                 | 4.69                 |
| 37        | Gravad lax                           | Seafood        | 26        | 39             | 1014.00      | 9                     | 62                 | 3.42                 |
| 73        | Röd Kaviar                           | Seafood        | 15        | 45             | 675.00       | 10                    | 71                 | 2.28                 |
| 13        | Konbu                                | Seafood        | 6         | 92             | 552.00       | 11                    | 72                 | 1.86                 |
| 45        | Røgede sild                          | Seafood        | 10        | 15             | 150.00       | 12                    | 76                 | 0.51                 |
  
**Insight:**
- Revenue within each category is heavily concentrated in the top-ranked product, highlighting clear category leaders and potential dependency risks.
- High-priced products tend to outperform high-volume products in revenue contribution, emphasizing the importance of pricing strategy over unit sales alone.
- Lower-ranked products contribute marginal revenue, indicating opportunities for product optimization, consolidation, or repositioning.



