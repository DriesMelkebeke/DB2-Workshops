# Solutions - Revisit the basics
## Dataset - Xtreme
![img](/workshops/shared/images/diagrams/diagram-xtreme.png)

1. Give the unique names of all products containing the word 'helmet' or with a name of 6 characters.
    ```sql
    SELECT DISTINCT productName ​
    FROM product​
    WHERE productName LIKE '%helmet%' ​
        OR productName LIKE '______';
    ```
2. Show the name and the reorderlevel of all products with a level between 50 and 500 (boundaries included)
    ```sql
    SELECT 
     productName
    ,reorderLevel​
    FROM product​
    WHERE reorderLevel BETWEEN 50 AND 500;
    ```
3. Count the amount of products (columnname‘amount of products’), AND the amount of products in stock (= unitsinstocknot empty) (columnname‘Units in stock’)
    ```sql
    SELECT 
     COUNT(productid)    AS 'Amount of Products'
    ,COUNT(unitsinstock) AS 'Units in Stock' ​
    FROM product;
    ```
4. How many employees have a function of supervisor?
    ```sql
    SELECT COUNT(DISTINCT supervisorID) + 
    (SELECT COUNT(*) FROM  employee WHERE supervisorid IS NULL) AS "No. of Supervisors" 
    FROM employee;
    ```
    > 1. Count all the unique people who are supervising others;
    > 2. Count the ones who are not supervised, which means they are supervisors.
5. Give the date of birth of the youngest employee and the oldest.
    ```sql
    SELECT 
     MIN(birthDate) as 'Eldest'
    ,MAX(birthDate) as 'Youngest' 
    FROM employee;​
    ```
6. What’s the number of employees who will retire (at 65) within the first 30 years?
    ```sql
    SELECT 
      employeeid
     ,birthdate
     ,DATEDIFF(YEAR,birthdate,GETDATE()) AS 'Current Age'
     ,DATEDIFF(YEAR,birthdate,DATEADD(year,30,GETDATE())) 'Current Age +30' -- Optiional
    FROM employee​
    WHERE DATEDIFF(YEAR,birthdate,DATEADD(YEAR,30,GETDATE())) >= 65;
    ```
    > See Deep Dive for more explanation.
7. Show a list of different countries where 2 of more suppliers live in, make sure to order alphabeticaly. 
    ```sql
    SELECT country 
    FROM supplier ​
    GROUP BY country ​
    HAVING COUNT(country) >= 2
    ORDER BY country;
    ```
8. Which suppliers offer at least 10 products with a price less then 100 dollar? Show supplierId and the number of different products. The supplier with the highest number of products comes first.
    ```sql
    SELECT 
     supplierID
    ,COUNT(*) as "No. of Products" ​
    FROM product​
    WHERE price < 100​
    GROUP BY supplierID​
    HAVING COUNT(supplierId) >= 10​
    ORDER BY count(*) DESC;
    ```
9. Count the number of workers (salary below 40.000), clerks (salary between 40.000 and 50.000 EUR) and managers (salary > 50000). Show 2 columns the name of the role and the amount of people in that role. 
    ```sql
    SELECT 
    CASE
        WHEN salary < 40000 THEN 'Worker'  ​
        WHEN salary <= 50000 THEN 'Clerck'  ​
        ELSE 'Manager'
    END AS 'Role'
    , COUNT(salary) AS "Amount"​
    FROM employee​
    GROUP BY ​
    CASE
        WHEN salary < 40000 THEN 'Worker'  ​
        WHEN salary <= 50000 THEN 'Clerck'  ​
        ELSE 'Manager'
    END
    ```
    > See Deep Dive for more explanation.
10. Which suppliers (Id and name) deliver products from the class "Bicycle"?
    ```sql
    SELECT DISTINCT
      product.supplierID
     ,supplierName
     FROM product
        JOIN supplier ON product.supplierID = supplier.supplierID     ​
        JOIN productClass ON product.productClassID = productClass.productClassID​
    WHERE productClassName = 'Bicycle';
    ```
    > An opposite `JOIN` is also possible, you can start with the `Supplier` table and `JOIN` the `Product, ProductClass` tables.
11. Give for each supplier the number of orders that contain products of that supplier. Show supplierID, supplier name and the number of orders. Order the results alphabetically by the name of the supplier.
    ```sql
    SELECT 
     s.supplierID
    ,supplierName
    ,COUNT(DISTINCT o.orderID) AS 'No. of Orders'
    FROM supplier s 
        LEFT JOIN product p ON s.supplierID = p.supplierID     ​
        LEFT JOIN ordersDetail o ON p.productID = o.productID​
    GROUP BY s.supplierID, supplierName
    ORDER BY supplierName;
    ```
12. What’s for each type the lowest productpriceof products for Youth? Show producttypename and lowest. 
    > Hint: products for youth contain `Y` in the `M_F` field
    ```sql
    SELECT 
     prodT.productTypeName
    ,MIN(prod.price) AS 'Lowest Price'​
    FROM product prod
        JOIN productType prodT ​ON prod.productTypeID = prodT.productTypeID​
    WHERE prod.m_f = 'Y'​
    GROUP BY prodT.productTypeName;
    ```
13. Give for each purchased productId: productname, the least and the most ordered. Order by productname.
    ```sql
    SELECT 
     pu.productid
    ,p.productname
    ,MIN(unitsonorder) AS 'Least'
    ,MAX(unitsonorder)​ AS 'Most'
    FROM purchases pu 
        JOIN product p ON pu.productid=p.productid​
    GROUP BY pu.productid,p.productname;
    ```
14. Give a summary for each employee with orderID, employeeID and the fullname of the employee. 
    > Make sure that the list also contains employees who don’t have orders yet. 
    ```sql
    SELECT 
     ord.orderID
    ,emp.employeeID
    ,CONCAT(emp.lastName,' ',emp.firstName) AS 'Employee'​
    FROM employee emp
        LEFT JOIN orders ord ON emp.employeeID = ord.employeeID​
    ORDER BY ord.orderID;
    ```

## Deep Dive
1. Is the `LIKE` operator case-sensitive or not?
    - An answer to this question can be found in [this StackOverflow post.](https://stackoverflow.com/questions/14962419/is-the-like-operator-case-sensitive-with-mssql-server) 
2. Why is it not possible to use certain columns from the `SELECT` statement in the `WHERE`, `ORDER BY` or `GROUP BY` clauses?
    - The projection (`SELECT`) happens after the filter (`WHERE`), same goes for the `GROUP BY` claus but not the `ORDER BY`.
 
## Exercises
Click [here](../basic-xtreme.md) to go back to the exercises.
