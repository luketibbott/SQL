1. Which customers are from the UK?
Around the Horn
B's Beverages
Consolidated Holdings
Eastern Connection
Island Trading
North/South
Seven Seas Imports

postgresql```
SELECT *
FROM Customers c
WHERE c.Country = 'UK';
```

2. What is the name of the customer who has the most orders?
Reggiani Caseifici

postgresql```
SELECT c.CustomerName, COUNT(*)
FROM Customers c
JOIN Orders o
ON c.CustomerID = o.CustomerID
ORDER BY 2 DESC
LIMIT 1;
```

3. Which supplier has the highest average product price?
Aux joyeux ecclésiastiques

postgresql```
SELECT s.SupplierName, AVG(p.price)
FROM Suppliers s
JOIN Products P
ON s.SupplierID = p.SupplierID
GROUP BY 1
ORDER BY 2 DESC
LIMIT 1;
```

4. How many different countries are all the customers from? (Hint: consider DISTINCT.)
21

postgresql```
SELECT COUNT(DISTINCT c.Country)
FROM Customers c;
```

5. What category appears in the most orders?
Dairy Products

postgresql```
SELECT c.CategoryName, COUNT(*)
FROM Orders o
JOIN OrderDetails od
ON o.OrderID = od.OrderID
JOIN PRODUCTS p
ON od.ProductID = p.ProductID
JOIN Categories c
ON p.CategoryID = c.CategoryID
GROUP BY 1
ORDER BY 2 DESC;
```

6. What was the total cost for each order?
The below query returns the cost of each order.

postgresql```
WITH t1 AS (SELECT od.OrderID, od.Quantity*p.Price AS prod
  FROM OrderDetails od
  JOIN Products p
  ON od.ProductID = p.ProductID)
  
SELECT SUM(t1.prod)
FROM t1
GROUP BY t1.OrderID;
```

7. Which employee made the most sales (by total price)?
Margaret Peacock

postgresql```
WITH t1 AS (SELECT e.FirstName, e.LastName, od.OrderID, od.Quantity*p.Price AS prod
  FROM Employees e
  JOIN Orders o
  ON o.EmployeeID = e.EmployeeID
  JOIN OrderDetails od
  ON od.OrderID = od.OrderID
  JOIN Products p
  ON od.ProductID = p.ProductID)
  
SELECT t1.FirstName, t1.LastName, ROUND(SUM(t1.prod), 2) AS total_sales
FROM t1
GROUP BY t1.FirstName, t1.LastName
ORDER BY 3 DESC
LIMIT 1;
```


8. Which employees have BS degrees? (Hint: look at the LIKE operator.)
Janet Leverling, Steve Buchanan

postgresql```
SELECT e.FirstName, e.LastName
FROM Employees e
WHERE e.Notes LIKE '%BS%';
```

9. Which supplier of three or more products has the highest average product price? (Hint: look at the HAVING operator.)
Tokyo Traders has an average product price of 46 and supplies 3 products.

WITH t1 AS (
  SELECT s.SupplierName, COUNT(*) num_products
  FROM Suppliers s
  JOIN Products p
  ON p.SupplierID = s.SupplierID
  GROUP BY s.SupplierName
  HAVING COUNT(*) > 2
  ORDER BY 2 DESC),
  
  t2 AS (
  SELECT s.SupplierName, AVG(p.Price) avg_price
  FROM Suppliers s
  JOIN Products p
  ON s.SupplierID = p.SupplierID
  GROUP BY 1)
  
  SELECT * 
  FROM t2
  JOIN t1 
  ON t2.SupplierName = t1.SupplierName
  ORDER BY t2.avg_price DESC
  LIMIT 1;