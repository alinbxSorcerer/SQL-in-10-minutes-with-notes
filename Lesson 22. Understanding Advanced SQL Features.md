# Lesson 22. Understanding Advanced SQL Features

In this lesson, you’ll look at several of the advanced data-manipulation features that have evolved with SQL: constraints, indexes, and triggers.

## Understanding Constraints

SQL has evolved through many versions to become a very complete and powerful language. Many of the more powerful features are sophisticated tools that provide you with data-manipulation techniques such as constraints.

Relational tables and referential integrity have both been discussed several times in prior lessons. As I explained in those lessons, relational databases store data broken into multiple tables, each of which stores related data. Keys are used to create references from one table to another (thus the term referential integrity).

For relational database designs to work properly, you need a way to ensure that only valid data is inserted into tables. For example, if the Orders table stores order information and OrderItems stores order details, you want to ensure that any order IDs referenced in OrderItems exist in Orders. Similarly, any customers referred to in Orders must be in the Customers table.

Although you can perform checks before inserting new rows (do a SELECT on another table to make sure the values are valid and present), it is best to avoid this practice for the following reasons:

- If database integrity rules are enforced at the client level, every client is obliged to enforce those rules, and inevitably some clients won’t.
- You must also enforce the rules on UPDATE and DELETE operations.
- Performing client-side checks is a time-consuming process. Having the DBMS do the checks for you is far more efficient.

> **Constraints**
>
> Rules govern how database data is inserted or manipulated.

DBMSs enforce referential integrity by imposing constraints on database tables. Most constraints are defined in table definitions (using the CREATE TABLE or ALTER TABLE as discussed in Lesson 17, “Creating and Manipulating Tables”).

> **Caution: Constraints Are DBMS Specific** 
>
> There are several different types of constraints, and each DBMS provides its own level of support for them. Therefore, the examples shown here might not work as you see them. Refer to your DBMS documentation before proceeding.



### Primary Keys

Lesson 1, “Understanding SQL,” briefly discusses primary keys. A primary key is a special constraint used to ensure that values in a column (or set of columns) are unique and never change, in other words, a column (or columns) in a table whose values uniquely identify each row in the table. This facilitates the direct manipulation of and interaction with individual rows. Without primary keys, it would be difficult to safely use UPDATE or DELETE specific rows without affecting any others.

Any column in a table can be established as the primary key, as long as it meets the following conditions:

- No two rows may have the same primary key value.
- Every row must have a primary key value. (Columns must not enable NULL values.)
- The column containing primary key values can never be modified or updated. (Most DBMSs won’t enable this, but if yours does enable doing so, well, don’t!)
- Primary key values can never be reused. If a row is deleted from the table, its primary key must not be assigned to any new rows.

One way to define primary keys is to create them, as follows:

![Screen Shot 2018-08-13 at 10.28.19 PM](http://heropublic.oss-cn-beijing.aliyuncs.com/142840.png)

In the above example, the keyword PRIMARY KEY is added to the table definition so that vend_id becomes the primary key.

```python
ALTER TABLE Vendors 
ADD CONSTRAINT PRIMARY KEY (vend_id);
```

Here the same column is defined as the primary key, but the CONSTRAINT syntax is used instead. This syntax can be used in CREATE TABLE and ALTER TABLE statements.

>  **Note: Keys In SQLite SQLite** 
>
> does not allow keys to be defined using ALTER TABLE and requires that they be defined as part of the initial CREATE TABLE.



### Foreign Keys

A foreign key is a column in a table whose values must be listed in a primary key in another table. Foreign keys are an extremely important part of ensuring referential integrity. To understand foreign keys, let’s look at an example.

The Orders table contains a single row for each order entered into the system. Customer information is stored in the Customers table. Orders in Orders are tied to specific rows in the Customers table by the customer ID. The customer ID is the primary key in the Customers table; each customer has a unique ID. The order number is the primary key in the Orders table; each order has a unique number.

The values in the customer ID column in the Orders table are not necessarily unique. If a customer has multiple orders, there will be multiple rows with the same customer ID (although each will have a different order number). At the same time, the only values that are valid within the customer ID column in Orders are the IDs of customers in the Customers table.

That’s what a foreign key does. In our example, a foreign key is defined on the customer ID column in Orders so that the column can accept only values that are in the Customers table’s primary key.

Here’s one way to define this foreign key:

```python
MySQL [distributor]> create table oorders ( order_num integer not null primary key, order_date datetime not null, cust_id char(10) not null references customers(cust_id) );
Query OK, 0 rows affected (0.157 sec)
```

Here the table definition uses the REFERENCES keyword to state that any values in cust_id must be in cust_id in the Customers table.

The same thing could have been accomplished using CONSTRAINT syntax in an ALTER TABLE statement:

```python
ALTER TABLE Orders 
ADD CONSTRAINT 
FOREIGN KEY (cust_id) REFERENCES Customers (cust_id)
```

>  **Tip: Foreign Keys Can Help Prevent Accidental Deletion** 
>
> As noted in Lesson 16, in addition to helping enforce referential integrity, foreign keys serve another invaluable purpose. After a foreign key is defined, your DBMS does not allow the deletion of rows that have related rows in other tables. For example, you are not allowed to delete a customer who has associated orders. The only way to delete that customer is to first delete the related orders (which in turn means deleting the related order items). Because they require such methodical deletion, foreign keys can help prevent the accidental deletion of data.
>
> However, some DBMSs support a feature called cascading delete. If enabled, this feature deletes all related data when a row is deleted from a table. For example, if cascading delete is enabled and a customer is deleted from the Customers table, any related order rows are deleted automatically.



### Unique Constraints

Unique constraints are used to ensure that all data in a column (or set of columns) is unique. They are similar to primary keys, but there are some important distinctions:

- A table can contain multiple unique constraints, but only one primary key is allowed per table.
- Unique constraint columns can contain NULL values.
- Unique constraint columns can be modified or updated.
- Unique constraint column values can be reused.
- Unlike primary keys, unique constraints cannot be used to define foreign keys.

An example of the use of constraints is an employees table. Every employee has a unique Social Security number, but you would not want to use it for the primary key because it is too long (in addition to the fact that you might not want that information easily available). Therefore, every employee also has a unique employee ID (a primary key) in addition to his Social Security number.

Because the employee ID is a primary key, you can be sure that it is unique. You also might want the DBMS to ensure that each Social Security number is unique, too (to make sure that a typo does not result in the use of someone else’s number). You can do this by defining a UNIQUE constraint on the Social Security number column.

The syntax for unique constraints is similar to that for other constraints. Either the UNIQUE keyword is defined in the table definition or a separate CONSTRAINT is used.



Check Constraints

Check constraints are used to ensure that data in a column (or set of columns) meets a set of criteria that you specify. Common uses of this are

- Checking minimum or maximum values—For example, preventing an order of 0 (zero) items (even though 0 is a valid number)
- Specifying ranges—For example, making sure that a ship date is greater than or equal to today’s date and not greater than a year from now
- Allowing only specific values—For example, allowing only M or F in a gender field

In other words, datatypes (discussed in Lesson 1) restrict the type of data that can be stored in a column. Check constraints place further restrictions within that datatype, and these can be invaluable in ensuring that the data that gets inserted into your database is exactly what you want. Rather than relying on client applications or users to get it right, the DBMS itself will reject anything that is invalid.

The following example applies a check constraint to the OrderItems table to ensure that all items have a quantity greater than 0:

```python
CREATE TABLE OrderItems 
( 
    order_num INTEGER 
    order_item INTEGER 
    prod_id CHAR(10) quantity INTEGER 0),
   item_price MONEY );
```

With this constraint in place, any row inserted (or updated) will be checked to ensure that quantity is greater than 0.

To check that a column named gender contains only M or F, you can do the following in an ALTER TABLE statement:

```python
ADD CONSTRAINT CHECK (gender LIKE '[MF]')
```



> **Tip: User-Defined Datatypes**
>
> Some DBMSs enable you to define your own datatypes. These are essentially simple datatypes with check constraints (or other constraints) defined. For example, you can define your own datatype called gender that is a single-character text datatype with a check constraint that restricts its values to M or F (and perhaps NULL for Unknown). You could then use this datatype in table definitions. The advantage of custom datatypes is that the constraints need to be applied only once (in the datatype definition), and they are automatically applied each time the datatype is used. Check your DBMS documentation to determine if user-defined datatypes are supported.



## Understanding Indexes

Indexes are used to sort data logically to improve the speed of searching and sorting operations. The best way to understand indexes is to envision the index at the back of a book (this book, for example).

Suppose you want to find all occurrences of the word datatype in this book. The simple way to do this would be to turn to page 1 and scan every line of every page looking for matches. Although that works, it is obviously not a workable solution. Scanning a few pages of text might be feasible, but scanning an entire book in that manner is not. As the amount of text to be searched increases, so does the time it takes to pinpoint the desired data.

That is why books have indexes. An index is an alphabetical list of words with references to their locations in the book. To search for datatype, you find that word in the index to determine what pages it appears on. Then, you turn to those specific pages to find your matches.

What makes an index work? Simply, it is the fact that it is sorted correctly. The difficulty in finding words in a book is not the amount of content that must be searched; rather, it is the fact that the content is not sorted by word. If the content is sorted like a dictionary, an index is not needed (which is why dictionaries don’t have indexes).

Database indexes work in much the same way. Primary key data is always sorted; that’s just something the DBMS does for you. Retrieving specific rows by primary key, therefore, is always a fast and efficient operation.

Searching for values in other columns is usually not as efficient, however. For example, what if you want to retrieve all customers who live in a specific state? Because the table is not sorted by state, the DBMS must read every row in the table (starting at the very first row) looking for matches, just as you would have to do if you were trying to find words in a book without using an index.

The solution is to use an index. You may define an index on one or more columns so that the DBMS keeps a sorted list of the contents for its own use. After an index is defined, the DBMS uses it in much the same way as you would use a book index. It searches the sorted index to find the location of any matches and then retrieves those specific rows.

But before you rush off to create **dozens of indexes**, bear in mind the following:

- Indexes improve the performance of retrieval operations, but they degrade the performance of data insertion, modification, and deletion. When these operations are executed, the DBMS has to update the index dynamically.
- Index data can take up lots of storage space.
- Not all data is suitable for indexing. Data that is not sufficiently unique (State, for example) will not benefit as much from indexing as data that has more possible values (First Name or Last Name, for example).
- Indexes are used for data filtering and for data sorting. If you frequently sort data in a specific order, that data might be a candidate for indexing.
- Multiple columns can be defined in an index (for example, State plus City). Such an index will be of use only when data is sorted in State plus City order. (If you want to sort by City, this index would not be of any use.)

There is no hard-and-fast rule as to what should be indexed and when. Most DBMSs provide utilities you can use to determine the effectiveness of indexes, and you should use these regularly.

Indexes are created with the CREATE INDEX statement (which varies dramatically from one DBMS to another). The following statement creates a simple index on the Products table’s product name column:

```python
CREATE INDEX prod_name_ind 
ON PRODUCTS (prod_name);
```

Every index must be uniquely named. Here the name prod_name_ind is defined after the keywords CREATE INDEX. ON is used to specify the table being indexed, and the columns to include in the index (just one in this example) are specified in parentheses after the table name.

> **Tip: Revisiting Indexes**
>
> Index effectiveness changes as table data is added or changed. Many database administrators find that what once was an ideal set of indexes might not be so ideal after several months of data manipulation. It is always a good idea to revisit indexes on a regular basis to fine-tune them as needed.



## Understanding Triggers

Triggers are special stored procedures that are executed automatically when specific database activity occurs. Triggers might be associated with **INSERT, UPDATE, and DELETE operations** (or any combination thereof) on specific tables. 

Unlike stored procedures (which are simply stored SQL statements), triggers are tied to individual tables. A trigger associated with INSERT operations on the Orders table will be executed only when a row is inserted into the Orders table. Similarly, a trigger on INSERT and UPDATE operations on the Customers table will be executed only when those specific operations occur on that table. 

Within triggers, your code has access to the following: 

- All new data in INSERT operations 
- All new data and old data in UPDATE operations 
- Deleted data in DELETE operations 

Depending on the DBMS being used, triggers can be executed before or after a specified operation is performed.

The following are some common uses for triggers: 

- Ensuring data consistency—For example, converting all state names to uppercase during an INSERT or UPDATE operation 
- Performing actions on other tables based on changes to a table—For example, writing an audit trail record to a log table each time a row is updated or deleted 
- Performing additional validation and rolling back data if needed—For example, making sure a customer’s available credit has not been exceeded and blocking the insertion if it has 
- Calculating computed column values or updating timestamps As you probably expect by now, trigger creation syntax varies dramatically from one DBMS to another. Check your documentation for more details.

As you probably expect by now, trigger creation syntax varies dramatically from one DBMS to another. Check your documentation for more details.

The following example creates a trigger that converts the cust_state column in the Customers table to uppercase on all INSERT and UPDATE operations.

This is the SQL Server version:

```python
CREATE TRIGGER customer_state 
ON Customers 
FOR INSERT, UPDATE 
AS 
UPDATE Customers 
SET cust_state = Upper(cust_state) 
WHERE Customers.cust_id = inserted.cust_id;
```

This is the Oracle and PostgreSQL version:

```python
CREATE TRIGGER customer_state 
AFTER INSERT OR UPDATE 
FOR EACH ROW 
BEGIN 
UPDATE Customers 
SET cust_state = Upper(cust_state) 
WHERE Customers.cust_id = :OLD.cust_id 
END;
```

> **Tip: Constraints Are Faster Than Triggers** 
>
> As a rule, constraints are processed more quickly than triggers, so whenever possible, use constraints instead.



## Database Security

There is nothing more valuable to an organization than its data, and data should always be protected from would-be thieves or casual browsers. Of course, at the same time data must be accessible to users who need access to it, and so most DBMSs provide administrators with mechanisms by which to grant or restrict access to data.

The foundation of any security system is user authorization and authentication. This is the process by which a user is validated to ensure he is who he says he is and that he is allowed to perform the operation he is trying to perform. Some DBMSs integrate with operating system security for this, others maintain their own user and password lists, and still others integrate with external directory services servers.

Some operations that are often secured

- Access to database administration features (creating tables, altering or dropping existing tables, and so on)
- Access to specific databases or tables
- The type of access (read-only, access to specific columns, and so on)
- Access to tables via views or stored procedures only
- Creation of multiple levels of security, thus allowing varying degrees of access and control based on login
- Restricting the ability to manage user accounts

Security is managed via the SQL GRANT and REVOKE statements, although most DBMSs provide interactive administration utilities that use the GRANT and REVOKE statements internally.



## Summary 

In this lesson, you learned how to use some advanced SQL features. Constraints are an important part of enforcing referential integrity; indexes can improve data retrieval performance; triggers can be used to perform pre- or post-execution processing; and security options can be used to manage data access. Your own DBMS probably offers some form of these features. Refer to your DBMS documentation for more details.

