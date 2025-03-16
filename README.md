**DS 2002 Midterm**

By: *Gabriel Jackson (tbp8gx)*

***

**General Info**

For this project, I used the source database *adventureworks* to extract, transform, and load the data into a 
*midterm_dw* database. I chose a business process of retail sale orders,
with an emphasis on tracking the relationships between customers, products, and territories. To meet the requirements of the project,
I created a data mart with the following
(OLAP, star) schema:

**Schema**

1. dim_customer for Customers (via SQL)
2. dim_product for Products (via JSON & MongoDB)
3. dim_date for Dates (via SQL)
4. dim_territory for Territories (via local CSV file)
5. fact_sale_orders for Sales (via SQL)

Two of my dimensional tables (dim_customer and dim_date) and my fact tabe (fact_sales_orders) are extracted by SQL. One dimensional table (dim_product) is extracted by
using MongoDB and the last dimensional table (dim_territory) is extracted by a local CSV file. 

**Making the JSON and CSV files**

To make the JSON file, I used MySQL on the *adventureworks* database with the following select query and exported the query response to JSON:
```
    WITH ProductCatsAndSubCatsCTE AS 
        (
            SELECT pc.ProductCategoryID, pc.Name AS ProductCategory, psc.ProductSubcategoryID, psc.Name AS ProductSubcategory
            FROM productcategory AS pc
            INNER JOIN productsubcategory AS psc
            ON pc.ProductCategoryID = psc.ProductCategoryID
        )
    SELECT p.ProductID, p.Name, p.ProductNumber, p.MakeFlag, p.FinishedGoodsFlag,
            p.Color, p.SafetyStockLevel, p.ReorderPoint, p.StandardCost, p.ListPrice,
            p.Size, p.SizeUnitMeasureCode, p.WeightUnitMeasureCode, p.Weight, p.DaysToManufacture,
            p.ProductLine, p.Class, p.Style, psc.ProductCategory, psc.ProductSubcategory,
            pm.NAME AS ProductModel, p.SellStartDate, p.SellEndDate, p.DiscontinuedDate
    FROM product AS p
    LEFT OUTER JOIN ProductCatsAndSubCatsCTE AS psc
    ON p.ProductSubcategoryID = psc.ProductSubcategoryID
    LEFT OUTER JOIN productmodel AS pm
    ON p.ProductModelID = pm.ProductModelID;
```

To make the CSV file, I used MySQL on the *adventureworks* database with the following select query and exported the query response to CSV:
```
    SELECT 
        TerritoryID,
        Name AS TerritoryName,
        CountryRegionCode,
        `Group`
    FROM salesterritory;
```

**Steps to Recreate the Midterm Project**

To recreate my project, you should follow the steps below:
- A new database, *midterm_dw*, should be created in MySQL. The *adventureworks* database should
also be created and populated with its data. (The script to populate the data can be found [here](https://github.com/JTupitza-UVA/DS-2002/blob/main/Projects/Scripts/AdventureWorks_MySQL.sql).)
- The *Lab_02c_Create_Populate_Dim_Date.sql* file should be used to create the *dim_date* table in *midterm_dw*. (The script can be found [here](https://github.com/JTupitza-UVA/DS-2002/blob/main/01-SQL/Labs/Lab_02c_Create_Populate_Dim_Date.sql).)
- The *Midterm.ipynb* file should be placed in the parent folder of the */data/* folder. 
- The above SQL scripts can be used to generate the products.json and territory.csv files from MySQL. These files should be placed within the */data/* folder.
- A Jupyter server can then run *Midterm.ipynb*. You will likely need to tweak the *src_db_args* and *dest_db_args* variables in *Midterm.ipynb* to connect to the MySQL databases. You may also need to change *mongodb_args* to connect to a MongoDB Atlas cluster.
- Every section of *Midterm.ipynb* should describe what it is doing in the comments. 
- There is also an outline provided in *Midterm.ipynb* to describe the process in steps:
  1. Import libraries, connect to MongoDB and MySQL, and create helper functions to communicate with MongoDB and MySQL (like in the previous projects)
  2. Extract the dimensional dataframes for the dimensional (dim) tables 
  3. Transform the dimensional dataframes
  4. Load the dimensional dataframes into the new database
  5. Extract the fact dataframe via SQL 
  6. Transform the fact dataframe via SQL 
  7. Load the fact dataframe into the new database 
  8. Demonstrate proper functionality in new database


