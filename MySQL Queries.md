**1. Which products have the lowest stock levels and need restocking?**

SELECT ProductName, CategoryName,
ProductStandardCost
 FROM product
WHERE ProductStandardCost < 100 # Assuming stock by represent cost 
ORDER BY 3 ASC

| Product Name                         | Category | Standard Cost ($) |
|--------------------------------------|----------|-------------------|
| Western Digital WD2500AVVS          | Storage  | 12.63             |
| Western Digital WD2500AAJS          | Storage  | 15.23             |
| Seagate ST31000340NS - FFP          | Storage  | 34.40             |
| Western Digital WD10EZEX            | Storage  | 35.83             |
| ADATA ASU800SS-128GT-C              | Storage  | 37.78             |
| SA400S37/120G                       | Storage  | 40.63             |
| Seagate ST1000DM010                 | Storage  | 42.18             |


**2. What are the top 10 best-selling products?**


SELECT 
    od.ProductID,
    p.ProductName,
    p.CategoryName,
    ROUND(SUM(od.OrderItemQuantity * od.PerUnitPrice),
            2) Revenue
FROM
    orderdetails od
        JOIN
    product p ON od.ProductID = p.ProductID
GROUP BY 1 , 2 , 3
ORDER BY 4 DESC
LIMIT 10
;
| Product ID | Product Name                                          | Category      | Revenue ($)  |
|------------|------------------------------------------------------|--------------|--------------|
| P359       | PNY SSD9SC240GMDA-RB                                 | Storage      | 600,155.00   |
| P246       | Samsung MZ-V6E500                                    | Storage      | 550,487.00   |
| P304       | Kingston Fury (HX316C10FB/8) 8 GB                    | RAM          | 389,855.70   |
| P273       | Samsung MZ-75E120B/AM                                | Storage      | 344,872.35   |
| P267       | Western Digital WD5000AACS                          | Storage      | 328,038.42   |
| P101       | EVGA 11G-P4-6598-KR                                  | Video Card   | 323,400.00   |
| P307       | G.Skill RipjawsX (F3-1600C9Q-32GXM) DDR3 32 GB       | RAM          | 307,683.51   |
| P156       | G.Skill Trident Z                                    | Storage      | 302,398.88   |
| P213       | Asus TUF X299 MARK 1                                 | Mother Board | 300,139.18   |
| P110       | MSI GeForce GTX 1080 Ti GAMING X 11G                 | Video Card   | 292,382.07   |

**3. Which products are generating the highest profit?**

SELECT 
    ProductName, CategoryName, Profit
FROM
    product
ORDER BY profit DESC
LIMIT 1;

| Product Name                                         | Category | Profit ($) |
|------------------------------------------------------|----------|------------|
| G.Skill RipjawsX (F3-1600C9Q-32GXM) DDR3 32 GB      | RAM      | 2,770      |

**4. What is the monthly revenue trend?**

SELECT 
    MONTHNAME(o.orderdate) Month,
    ROUND(SUM(od.OrderItemQuantity * od.perunitprice)) Revenue
FROM
    orders o
        JOIN
    orderdetails od ON o.Orderid = od.OrderID
GROUP BY 1
ORDER BY 2 DESC
; 

| Month      | Revenue ($) |
|------------|------------|
| February   | 5,975,381  |
| October    | 3,756,405  |
| June       | 3,143,407  |
| September  | 2,824,782  |
| August     | 2,697,259  |
| March      | 2,611,744  |
| December   | 2,524,208  |
| May        | 2,258,027  |
| November   | 2,025,941  |
| April      | 1,316,502  |
| January    | 1,242,340  |
| July       | 144,094    |

**5. Who are the top 10 customers by total purchases?**

SELECT 
    c.CustomerName,
    ROUND(SUM(od.orderitemquantity * od.perunitprice),
            2) Total_Purchases
FROM
    customer c
        JOIN
    orders o ON c.Customerid = o.CustomerID
        JOIN
    orderdetails od ON od.OrderID = o.orderid
GROUP BY 1
ORDER BY 2 DESC
LIMIT 10;

| Customer Name        | Total Purchases ($) |
|----------------------|--------------------|
| Shields Seffi       | 600,155            |
| Bill Stein         | 550,487            |
| Daina Combs       | 389,855.70         |
| Mac Mckay         | 344,872.35         |
| Alessandra Estrada | 328,038.42         |
| Samara Barnett    | 323,400            |
| Darron Robertson  | 307,683.51         |
| Glinda Lambert    | 302,398.88         |
| Herman Stokes     | 300,139.18         |
| Reva Fuller       | 292,382.07         |


**6. Which category generates the highest sales?**

SELECT 
    p.CategoryName,
    ROUND(SUM(od.OrderItemQuantity * od.PerUnitPrice),
            2) Total_Sales
FROM
    product p
        JOIN
    orderdetails od ON p.ProductID = od.ProductID
GROUP BY 1
ORDER BY 2 DESC
LIMIT 1; 

| Category Name | Total Sales ($)   |
|--------------|------------------|
| Storage      | 10,712,932.17    |

**7. Which warehouses fulfill the most orders?**

SELECT 
    w.WarehouseName, COUNT(o.orderid) Total_Orders
FROM
    warehouse w
        JOIN
    employee e ON w.WarehouseID = e.WarehouseID
        JOIN
    orders o ON e.EmployeeID = o.EmployeeID
GROUP BY 1
ORDER BY 2 DESC; 

| Warehouse Name       | Total Orders |
|----------------------|--------------|
| New Jersey          | 45           |
| Seattle Washington  | 45           |
| Sydney             | 45           |
| Mexico City        | 45           |
| Beijing            | 45           |
| Southlake Texas    | 44           |
| San Francisco      | 44           |
| Toronto            | 44           |
| Bombay             | 43           |

**8. Which regions contribute the highest sales?**

SELECT 
    r.RegionName,
    ROUND(SUM(od.OrderItemQuantity * od.perunitprice),
            2) AS Sales
FROM
    region r
        JOIN
    warehouse w ON r.regionid = w.regionid
        JOIN
    employee e ON w.warehouseid = e.warehouseid
        JOIN
    orders o ON e.EmployeeID = o.EmployeeID
        JOIN
    orderdetails od ON o.orderid = od.orderid
GROUP BY r.RegionName
order by 2 desc;

| Region Name     | Sales ($)       |
|----------------|----------------|
| North America  | 9,747,952.78    |
| Asia           | 6,412,605.26    |
| North America  | 4,850,774.35    |
| Australia      | 3,704,306.45    |
| South America  | 2,707,261.23    |

**9 What is the average customer credit limit by region?**

SELECT 
    r.RegionName,
    c.CustomerName,
    ROUND(AVG(c.Customercreditlimit), 2) AvgCustomer_creditlimit
FROM
    customer c
        JOIN
    orders o ON c.Customerid = o.CustomerID
        JOIN
    employee e ON o.EmployeeID = e.EmployeeID
        JOIN
    warehouse w ON e.WarehouseID = w.WarehouseID
        JOIN
    region r ON w.RegionID = r.RegionID
GROUP BY 1 , 2
order by 3 desc;

| Region Name     | Customer Name         | Avg Customer Credit Limit |
|----------------|----------------------|---------------------------|
| South America | Flor Stone           | 5000.00                   |
| South America | Lavera Emerson       | 5000.00                   |
| South America | Fern Head            | 1200.00                   |
| South America | Shyla Ortiz          | 2400.00                   |
| South America | Jeni Levy            | 1200.00                   |
| South America | Matthias Hannah      | 500.00                    |
| South America | Matthias Cruise      | 500.00                    |
| South America | Meenakshi Mason      | 3600.00                   |
| South America | Christian Cage       | 2400.00                   |
| South America | Charlie Sutherland   | 3600.00                   |
| South America | Charlie Pacino       | 500.00                    |
| South America | Guillaume Jackson    | 700.00                    |
| South America | Daniel Costner       | 3700.00                   |
| South America | Dianne Derek         | 5000.00                   |
| South America | Geraldine Schneider  | 500.00                    |
| South America | Geraldine Martin     | 500.00                    |
| South America | Guillaume Edwards    | 500.00                    |
| South America | Maurice Mahoney      | 600.00                    |
| South America | Maurice Hasan        | 700.00                    |
| South America | Diane Higgins        | 700.00                    |
| South America | Dianne Sen           | 700.00                    |
| South America | Maurice Daltrey      | 900.00                    |
| South America | Tess Roth            | 1200.00                   |
| South America | Ka Kaufman           | 1200.00                   |

**10 Which employees process the most orders?**

SELECT 
    EmployeeName, COUNT(o.OrderID) TotalOrders
FROM
    employee e
        JOIN
    orders o ON e.employeeid = o.employeeid
  group by 1 
    order by 2 desc;
		

| Employee Name          | Total Orders |
|------------------------|-------------|
| Summer Payne          | 1           |
| Rose Stephens        | 1           |
| Annabelle Dunn       | 1           |
| Tommy Bailey         | 1           |
| Blake Cooper         | 1           |
| Jude Rivera         | 1           |
| Tyler Ramirez        | 1           |
| Ryan Gray           | 1           |
| Elliot Brooks        | 1           |
| Elliott James        | 1           |
| Albert Watson       | 1           |
| Mohammad Peterson    | 1           |

**11 What is the average order processing time per employee?**

SELECT 
    e.EmployeeName,
    round(AVG(DATEDIFF(o.OrderDate, CURDATE())),2) AS AvgProcessingDays
FROM
    Employee e
        JOIN
    Orders o ON e.EmployeeID = o.employeeid
GROUP BY e.EmployeeName
ORDER BY 2 DESC; 


| Employee Name            | Avg Processing Days |
|-------------------------|---------------------|
| Layla Mason             | 2248.00             |
| Forrest Alex            | 2248.00             |
| Lindsay Leonara         | 2248.00             |
| Austin Flores           | 2068.00             |
| Brown Mia               | 2068.00             |
| Close Phil              | 2068.00             |
| Owad Clinton            | 2068.00             |
| Roup Simon              | 2068.00             |
| Spirea Kelley           | 2068.00             |
| Georgia Mills           | 1916.00             |
| Gonzalez Maria          | 1916.00             |
| Miller Ned              | 1916.00             |
| Maisie Nichols         | 1915.00             |
| Hannah Knight         | 1915.00             |
| Good Susan             | 1915.00             |
| Gosciminski Phylicia | 1915.00             |
| Monkfish Erasmus   | 1915.00             |


**12 What is the total profit generated by each product?**

SELECT 
    ProductName, round(SUM(Profit) ,2)TotalProfit
FROM
    product
GROUP BY 1
order by 2 desc;

| Product Name | Total Profit |
|-------------|-------------|
| G.Skill RipjawsX (F3-1600C9Q-32GXM) DDR3 32 GB | 2770 |
| A-DATA (AX4U300038G16-SRS) 8GB DDR4 | 1990 |
| G.Skill TridentX (F3-2400C10D-8GTX) DDR3 8GB | 1962 |
| G.Skill Ripjaws V Series | 1833.04 |
| Corsair Vengeance LPX (CMK16GX4M2B3000C15B) 16GB | 1749 |


**13 Which products have the highest return rate?**

SELECT 
    p.ProductName, COUNT(o.OrderID) Return_Count
FROM
    product p
        JOIN
    orderdetails od ON p.ProductID = od.ProductID
        JOIN
    orders o ON od.OrderID = o.OrderID
WHERE
    od.OrderStatus = 'shipped'
GROUP BY 1
ORDER BY 2 DESC;

| Product Name                           | Return Count |
|----------------------------------------|--------------|
| G.Skill Ripjaws V Series              | 6            |
| Corsair Dominator Platinum             | 5            |
| G.Skill Trident Z                      | 5            |
| Corsair Vengeance LPX                  | 3            |
| Intel Xeon E5-2690 (OEM/Tray)          | 2            |
| Intel Xeon E5-2687W V4                 | 2            |





