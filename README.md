# Description
E-Commerce Database Management System (EC-DBMS) is a system that is designed to store, process, retrieve and analyze information concerned with the administrative and management of sales activity done by a customer online sitting at home. This project aims at maintaining all the information pertaining to the customers, vendors, products, and their categories, orders, and couriers. It enables vendors to set up online shops, customers to browse through the shops, and a system administrator to approve and reject requests for new shops and maintain lists of shop categories. The system manages the items in the shop and also helps customers purchase them online without having to visit the shop physically. The online shopping system will use the internet as the sole method for selling goods, products, and services to its consumers. The website will show all products in a categorized manner. Customers can browse any product for its price, other details and can order the product by using their registered account. The customer has to pay the order amount at the time of delivery.


# 3. Implementation
## 3.1 Creating Tables

Complete script is available in Database & Table Creation folder.

CREATE DATABASE OnlineShopping;

Use OnlineShopping;

CREATE TABLE Customer
(
	CustomerID int IDENTITY(1, 1) PRIMARY KEY,
	FirstName varchar(255) NOT NULL,
	LastName varchar(255) NOT NULL,
	DOB date NOT NULL,
	Email varchar(255) NOT NULL,
	Password varchar(255) NOT NULL,
	Contact varchar(255) NOT NULL
);

CREATE TABLE Country
(
	CountryID int IDENTITY(1, 1) PRIMARY KEY,
	CountryName varchar(255) NOT NULL
);

CREATE TABLE Province
(
	ProvinceID int IDENTITY(1, 1) PRIMARY KEY,
	ProvinceName varchar(255) NOT NULL
);

CREATE TABLE City
(
	CityID int IDENTITY(1, 1) PRIMARY KEY,
	CityName varchar(255) NOT NULL
);

CREATE TABLE ZipCode
(
	ZipCodeID int IDENTITY(1, 1) PRIMARY KEY,
	CityID int FOREIGN KEY REFERENCES City(CityID) NOT NULL,
	ProvinceID int FOREIGN KEY REFERENCES Province(ProvinceID) NOT NULL,
	CountryID int FOREIGN KEY REFERENCES Country(CountryID) NOT NULL
);

CREATE TABLE Address
(
	AddressID int IDENTITY(1, 1) PRIMARY KEY,
	HouseNo varchar(255) NOT NULL,
	Street int NOT NULL,
	CustomerID int FOREIGN KEY REFERENCES Customer(CustomerID) NOT NULL,
	ZipCodeID int FOREIGN KEY REFERENCES ZipCode(ZipCodeID) NOT NULL,
	Area varchar(255) NOT NULL
);
## 3.2 Inserting Data

Complete scripts are available in Data Insertion folder.

## Note: All information is fake and generated for the demonstration of the project.

use OnlineShopping;

Insert Into Category Values ('Android Smart TV Box/Air Mouse');

Insert Into City Values ('Karachi');

Insert Into Orders Values ('1','2011-01-27','1','318','77425889862');

Insert Into Product Values ('Air Mouse C120 for Android and Smart TV','1');

Insert Into Province Values ('Punjab');

Insert Into Review Values ('4','Great Product Slow delivery. Such slow much wow','1','1');
# 4. Interview questions 
## 4.1 What is the difference between WHERE and HAVING?
-- WHERE filters rows before grouping
SELECT VendorID, COUNT(*) AS TotalProducts
FROM VendorProduct
WHERE Price > 1000
GROUP BY VendorID;

-- HAVING filters after grouping
SELECT VendorID, COUNT(*) AS TotalProducts
FROM VendorProduct
GROUP BY VendorID
HAVING COUNT(*) > 5;
## 4.2 What are the different types of joins?
-- INNER JOIN (Only matching rows)
SELECT c.FirstName, o.OrderID
FROM Customer c
INNER JOIN Orders o ON c.CustomerID = o.CustomerID;

-- LEFT JOIN (All customers and their orders, if any)
SELECT c.FirstName, o.OrderID
FROM Customer c
LEFT JOIN Orders o ON c.CustomerID = o.CustomerID;

-- RIGHT JOIN (All orders and the customer info, if any)
SELECT c.FirstName, o.OrderID
FROM Customer c
RIGHT JOIN Orders o ON c.CustomerID = o.CustomerID;

-- FULL OUTER JOIN is not supported in MySQL directly,
-- But can be simulated using UNION of LEFT and RIGHT JOINs.
## 4.3 How do you calculate Average Revenue Per User (ARPU)?

SELECT 
  ROUND(SUM(vp.Price * op.Quantity) / COUNT(DISTINCT o.CustomerID), 2) AS ARPU
FROM Orders o
JOIN OrderedProduct op ON o.OrderID = op.OrderID
JOIN VendorProduct vp ON op.VendorProductID = vp.VendorProductID;

## 4.4 What are subqueries?

SELECT FirstName, LastName
FROM Customer
WHERE CustomerID IN (
  SELECT o.CustomerID
  FROM Orders o
  JOIN OrderedProduct op ON o.OrderID = op.OrderID
  GROUP BY o.CustomerID
  HAVING SUM(op.Quantity) > 5
);

## 4.5  How do you optimize a SQL query?
Based on your schema:

Add indexes on columns often used in JOIN, WHERE, ORDER BY (e.g., CustomerID, OrderID, VendorID).

Use LIMIT for pagination.

Avoid SELECT *; select only needed columns.

Use EXISTS instead of IN for large subqueries.

## 4.6 What is a view in SQL?

CREATE VIEW ProductCatalog AS
SELECT p.ProductName, vp.Price, v.Name AS VendorName
FROM Product p
JOIN VendorProduct vp ON p.ProductID = vp.ProductID
JOIN Vendor v ON vp.VendorID = v.VendorID;

## 4.7 How would you handle NULL values in SQL?

SELECT 
  r.Rating,
  COALESCE(r.Comment, 'No comment provided') AS Comment
FROM Review r;
