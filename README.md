@@ -1 +1,203 @@
#### What is a Stored Procedure (SP)?
A Stored Procedure is a set of SQL statements that are saved in the database and can be reused whenever needed.
It is like a function in programming: you call it, and it performs a task (e.g., inserting data, retrieving records, updating tables).

### Why Use SPs?
Reduces repetition of code.
Improves performance by reducing query parsing.
Adds security by hiding SQL logic.

### Types of Stored Procedures
SQL Server categorizes the stored procedures mainly in two types:-
-User-defined Stored Procedures
-System Stored Procedures

### example--Basic Syntax of Stored Procedure

CREATE PROCEDURE ProcedureName
AS
BEGIN
    -- SQL statements go here
END
--------------------------------------------------
1. User-Defined Stored Procedures
These are procedures created by users to perform specific tasks. They are written using SQL and stored in the database for reuse.
Key Features:
Created by users for customized tasks (e.g., insert, update, retrieve data).
Stored in the database schema and can be executed multiple times.
Can accept input and output parameters.
----------------------------------------------
#### 1. Basic: Retrieve All Records
Task: Get all customer records from the customer table.
----CREATE PROCEDURE GetAllcustomers
   AS
   BEGIN
    SELECT * FROM customer;
  END;
----------------------------------------------
###
Execute:
—--------------------
EXEC GetAllcustomers;

### output:-
CustomerID	CustomerName	ContactName	Address	City	PostalCode	Country
1	Alfreds Futterkiste	Maria Anders	Obere Str. 57	Berlin	12209	Germany
------------------------------------------
### exp2:-
2. Parameterized: Retrieve Records Based on Condition
Task: Get employee details by department using an input parameter.
-----------------------------------------------
create procedure getcusdetails_id
@customerid int
AS
BEGIN
 select *from customers where CustomerID=@customerid;
 END;
-----------------------------------------
###execute--
 getcusdetails_id @customerid=18
output
CustomerID	CustomerName	ContactName	Address	City	PostalCode	Country
18	Du monde entier	Janine Labrune	67, rue des Cinquante Otages	Nantes	44000	France
18	Aux joyeux ecclésiastiques	Guylène Nodier	203, Rue des Francs-Bourgeois	Paris	75004	France
------------------
###exp3
create procedure getcusdetails_id1
@customerid int
AS
BEGIN
 SELECT 
    Customers.CustomerID,
    Customers.CustomerName,
    Orders.OrderID,
    Orders.OrderDate,
    Products.ProductName,
    order_details.Quantity,
    Products.Price,
    (order_details.Quantity * Products.Price) AS TotalPrice
FROM 
    Customers
JOIN 
    Orders ON Customers.CustomerID = Orders.CustomerID
JOIN 
    order_details ON Orders.OrderID = order_details.OrderID
JOIN 
    Products ON order_details.ProductID = Products.ProductID
WHERE 
    Customers.CustomerID = @customerid;
 END;
----------------------------------------------
###execute--
 getcusdetails_id1 @customerid=18
output
CustomerID	CustomerName	OrderID	OrderDate	ProductName	Quantity	Price	TotalPrice
18	Du monde entier	10311	1996-09-20	Singaporean Hokkien Fried Mee	6	14.00	84.00
18	Aux joyeux ecclésiastiques	10311	1996-09-20	Singaporean Hokkien Fried Mee	6	14.00	84.00
18	Du monde entier	10311	1996-09-20	Gudbrandsdalsost	7	36.00	252.00
18	Aux joyeux ecclésiastiques	10311	1996-09-20	Gudbrandsdalsost	7	36.00	252.00
--------------------------------------------
###3. Insert Data into a Table
create procedure addcustomerdetails
@customerid int,
@customername varchar(50),
@contactName varchar(50),
@address varchar(50),
@City varchar(50),
@Postalcode varchar(50),
@Country varchar(50)
AS
Begin
insert into customers(customerid ,customername ,contactName ,
address ,City ,Postalcode ,Country )
values(@customerid ,@customername ,@contactName ,
@address ,@City ,@Postalcode ,@Country );
END;
-----------------------------------------------------------------------------
----execute
addcustomerdetails @customerid=2001 ,@customername='vduyew' ,@contactName='desa' ,
@address='vadodara' ,@City='padra' ,@Postalcode='390012' ,@Country='india'
###output
2001	vduyew	desa	vadodara	padra	390012	india
---------------------------------------------------------------------
###4. Update Existing Records
Task: Update the salary of an employee based on their ID.
select *from customers 


create procedure updatecustomerdetails1
@customerid int,@country varchar(50)
as
begin
update customers set Country=@country where CustomerID=@customerid ;
END;
----execute
updatecustomerdetails1 @customerid=1,@country='india'
###output
CustomerID	CustomerName	ContactName	Address	City	PostalCode	Country
1	Alfreds Futterkiste	Maria Anders	Obere Str. 57	Berlin	12209	india
1	Exotic Liquid	Charlotte Cooper	49 Gilbert St.	Londona	EC1 4SD	india
------------------------------------------------------------------
5. Delete Records
Task: Delete an employee record by their ID.
------------------------------------------
###6.Condition logics(if-else)
create proc checkcustomer_exist
@customerid int
as 
begin
if exists (select 1 from customers where CustomerID=@customerid)
print 'employee exist';
else 
print 'employee does not exist';
end;
-------------------------------------------------------
--exe
checkcustomer_exist @customerid=10000
###output
employee does not exist
OR
employee exist
------------------------------------------------------------------------
###7.Create Stored Procedure to Calculate Amount Payable
Now, let's create a stored procedure that calculates the total payable amount including the MRP, GST, and TAX.

MRP: The base price of the product.
GST: Goods and Services Tax, calculated based on the MRP and the GST percentage for the product.
TAX: Other taxes (for simplicity, we’ll calculate as a fixed percentage of the base price).
Total Amount Payable: The final amount customer has to pay after adding GST and other taxes.
--------------------------------------------------------
create table
CREATE TABLE Customer (
    CustomerID INT PRIMARY KEY,
    Name NVARCHAR(100),
    Address NVARCHAR(200)
);
select *from Customer
CREATE TABLE Product (
    ProductID INT PRIMARY KEY,
    ProductName NVARCHAR(100),
    MRP DECIMAL(10, 2),  -- Maximum Retail Price
    GSTPercentage DECIMAL(5, 2)  -- GST Percentage
);
CREATE TABLE OrderDetails (
    OrderID INT PRIMARY KEY,
    CustomerID INT,
    ProductID INT,
    Quantity INT,
    TotalAmount DECIMAL(10, 2),  -- Final total after applying GST and TAX
    FOREIGN KEY (CustomerID) REFERENCES Customer(CustomerID),
    FOREIGN KEY (ProductID) REFERENCES Product(ProductID)
);
INSERT INTO Customer (CustomerID, Name, Address) 
VALUES 
(1, 'John Doe', '123 Main St, City, Country'),
(2, 'Jane Smith', '456 Oak St, City, Country');
INSERT INTO Product (ProductID, ProductName, MRP, GSTPercentage)
VALUES
(1, 'Laptop', 50000.00, 18),  -- 18% GST
(2, 'Smartphone', 20000.00, 12); -- 12% GST
--------------------------------------------------------------------


