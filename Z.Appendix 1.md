# Appendix A. Sample Table Scripts

Writing SQL statements requires a good understanding of the **underlying database design**. Without knowing what information is stored in what table, how tables are related to each other, and the actual breakup of data within a row, it is impossible to write effective SQL.

You are strongly advised to actually try every example in every lesson in this book. All the lessons use a common set of data files. To assist you in better understanding the examples, and to enable you to follow along with the lessons, this appendix describes the tables used, their relationships, and how to build (or obtain) them.

## Understanding the Sample Tables

The tables used throughout this book are part of **an order entry system** used by an imaginary **distributo**r of toys. The tables are used to perform several tasks:

- Manage vendors

- Manage product catalogs

- Manage customer lists

- Enter customer orders

  > 这个tables, 回忆起往事一幕幕.

Making this all work requires five tables (that are closely interconnected as part of a relational database design). A description of each of the tables appears in the following sections.

> **Note: Simplified Examples**
>
> The tables used here are by no means complete. A real-world order entry system would have to keep track of lots of other data that has not been included here (for example, payment and accounting information, shipment tracking, and more). However, these tables do demonstrate the kinds of data organization and relationships that you will encounter in most real installations. You can apply these techniques and technologies to your own databases.



### Table Descriptions

What follows is a description of each of the five tables, along with the name of the columns within each table and their descriptions.

#### The Vendors Table

The Vendors table stores the vendors whose products are sold. Every vendor has a record in this table, and that vendor ID (the vend_id) column is used to match products with vendors.

![Screen Shot 2018-08-11 at 10.40.01 AM](http://heropublic.oss-cn-beijing.aliyuncs.com/030348.jpg)

All tables should have primary keys defined. This table should use vend_id as its primary key.



### The Product Table

The Products table contains the product catalog, one product per row. Each product has a unique ID (the prod_id column) and is related to its vendor by vend_id (the vendor’s unique ID).

![Screen Shot 2018-08-11 at 10.41.44 AM](http://heropublic.oss-cn-beijing.aliyuncs.com/030341.jpg)

- All tables should have primary keys defined. This table should use prod_id as its primary key.
- To **enforce referential integrity**, a foreign key should be defined on vend_id relating it to vend_id in VENDORS.

### The Customers Table

The Customers table stores all customer information. Each customer has a unique ID (the cust_id column).

![Screen Shot 2018-08-11 at 10.46.11 AM](http://heropublic.oss-cn-beijing.aliyuncs.com/030347.jpg)

- All tables should have primary keys defined. This table should use cust_id as its primary key.



### The Orders Table

The Orders table stores customer orders (but not order details). Each order is uniquely numbered (the order_num column). Orders are associated with the appropriate customers by the cust_id column (which relates to the customer’s unique ID in the Customers table).



![Screen Shot 2018-08-11 at 10.48.05 AM](http://heropublic.oss-cn-beijing.aliyuncs.com/030350.jpg)

- All tables should have primary keys defined. This table should use order_num as its primary key.
- To enforce referential integrity, a foreign key should be defined on cust_id relating it to cust_id in CUSTOMERS.

###  The OrderItems Table

The OrderItems table stores the actual items in each order, one row per item per order. For every row in Orders there are one or more rows in OrderItems. Each order item is uniquely identified by the order number plus the order item (first item in order, second item in order, and so on). Order items are associated with their appropriate order by the order_num column (which relates to the order’s unique ID in Orders). In addition, each order item contains the product ID of the item orders (which relates the item back to the Products table).

![Screen Shot 2018-08-11 at 10.54.57 AM](http://heropublic.oss-cn-beijing.aliyuncs.com/030345.jpg)

- All tables should have primary keys defined. This table should use `order_num` and `order_item` as its primary keys.
- To enforce referential integrity, foreign keys should be defined on `order_num` relating it to `order_num` in Orders and` prod_id `relating it to prod_id in Products.

Database administrators often use relationship diagrams to help demonstrate how database tables are connected. Remember, it is foreign keys that define those relationships as noted in the table descriptions above. Figure A.1 is the relationship diagram for the five tables described in this appendix.

![Screen Shot 2018-08-11 at 11.04.19 AM](http://heropublic.oss-cn-beijing.aliyuncs.com/031633.jpg)





## Obtaining the Sample Tables

In order to follow along with the examples, you need a set of populated tables. Everything you need to get up and running can be found on this book’s Web page at http://forta.com/books/0672336073/.



### Download a Ready-to-Use Data File

Go to the above URL to download fully populated data files in the following formats:

- Apache Open Office Base 
- Microsoft Access (2000 and 2007)
- SQLite 

If you use these files you will not need to run any of the SQL creation and population scripts.



### Download DBMS SQL Scripts

Most DBMSs store data in formats that do not lend themselves to **complete** file distribution (as Access, Open Office Base, and SQLite do). For these DBMSs you may download SQL scripts from the above URL. There are two files for each DBMS:

- create.txt contains the SQL statements to create the five database tables (including defining all primary keys and foreign key constraints). 
- populate.txt contains the SQL INSERT statements used to populate these tables. 

The SQL statements in these file are very DBMS specific, so be sure to execute the one for your own DBMS. These scripts are provided as a convenience to readers, and no liability is assumed for problems that may arise from their use.

At the time that this book went to press scripts were available for:

- IBM DB2 -
- Microsoft SQL Server (including Microsoft SQL Server Express) 
- MariaDB 
- MySQL 
- Oracle (include Oracle Express) 
- PostgreSQL

Other DBMSs may be added as needed or requested.

Appendix B, “Working in Popular Applications,” provides instructions on running the scripts in several popular environments.

> **Note: Create, Then Populate**
>
> You must run the table creation scripts before the table population scripts. Be sure to check for any error messages returned by these scripts, if the creation scripts fail you will need to remedy whatever problem may exist before continuing with table population.
>
> **Note: Specific DBMS Setup Instructions**
>
> The specific steps used to setup your DBMS varies greatly based on the DBMS used. When you download the scripts or databases from the book webpage, you’ll find a README file that provides specific setup and installation steps for specific DBMS.