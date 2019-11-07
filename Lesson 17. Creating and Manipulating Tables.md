# Lesson 17. Creating and Manipulating Tables

In this lesson you’ll learn the basics of table creation, alteration, and deletion.

## Creating Tables

SQL is not used just for table data manipulation. Rather, SQL can be used to perform all database and table operations, including the **creation and manipulation** of tables themselves.

There are generally two ways to create database tables:

- Most DBMSs **come with** an administration tool that can be used to create and manage database tables interactively.
- Tables may also be manipulated directly with SQL statements.

To create tables programmatically, the CREATE TABLE SQL statement is used. It is worth noting that when you use interactive tools, you are actually using SQL statements. Instead of your writing these statements, however, the interface generates and executes the SQL seamlessly for you (the same is true for changes to existing tables).

> **Caution: Syntax Differences**
>
> The exact syntax of the CREATE TABLE statement can vary from one SQL implementation to another. Be sure to refer to your DBMS documentation for more information on exactly what syntax and features it supports.

Complete coverage of all the options available when creating tables is beyond the scope of this lesson, but here are the basics. I’d recommend that You review your DBMS documentation for more information and **specifics.**

> **Note: DBMS Specific Examples**
>
> For examples of DBMS specific CREATE TABLE statements, see the example table creation scripts described in Appendix A, “The Example Tables.”



### Basic Table Creation

To create a table using CREATE TABLE, you must specify the following information:

- The name of the new table specified after the keywords CREATE TABLE.
- The name and definition of the table columns separated by commas.
- Some DBMSs require that you also specify the table location.

The following SQL statement creates the Products table used throughout this book:

```python
CREATE TABLE Products 
( 
    prod_id CHAR(10)  NOT NULL,
    vend_id CHAR(10)  NOT NULL,
    prod_name CHAR(254)  NOT NULL,
    prod_price DECIMAL(8,2) NOT NULL,
    prod_desc VARCHAR(1000)  NULL,
);
```

As you can see in the above statement, the table name is specified immediately following the CREATE TABLE keywords. The actual table definition (all the columns) is enclosed within parentheses. The columns themselves are separated by commas. This particular table is made up of five columns. Each column definition starts with the column name (which must be unique within the table), followed by the column’s datatype. (Refer to Lesson 1, “Understanding SQL,” for an explanation of datatypes. In addition, Appendix D, “Using SQL Datatypes,” lists commonly used datatypes and their compatibility.) The entire statement is terminated with a semicolon after the closing parenthesis.

I mentioned earlier that CREATE TABLE syntax varies greatly from one DBMS to another, and the simple script just seen demonstrates this. While the statement will work as is on Oracle, PostgreSQL, SQL Server, and SQLite, for MySQL the **varchar** must be replaced with text, and for DB2 the NULL must be removed from the final column. This is why I had to create a different SQL table creation script for each DBMS (as explained in Appendix A).

> **Tip: Statement Formatting** 
>
> As you will recall, white-space is ignored in SQL statements. Statements can be typed on one long line or broken up over many lines.
>
> **It makes no difference at all.** This enables you to format your SQL as **best suits you.** The preceding CREATE TABLE statement is a good example of SQL statement formatting—the code is specified over multiple lines, with the column definitions indented for easier reading and editing. Formatting your SQL in this way is entirely optional, but highly recommended.

> **Tip: Replacing Existing Tables** 
>
> When you create a new table, the table name specified must not exist or you’ll generate an error. To prevent accidental overwriting, SQL requires that you first manually remove a table (see later sections for details) and then recreate it, **rather than just overwriting it.**



### Working With Null Values

Back in Lesson 4, “Filtering Data,” you learned that NULL values are no values or the lack of a value. A column that allows NULL values also allows rows to be inserted with no value at all in that column. A column that does not allow NULL values does not accept rows with no value—in other words, that column will always be required when rows are inserted or updated.

Every table column is either a NULL column or a NOT NULL column, and that state is specified in the table definition at creation time. Take a look at the following example:

```python
MySQL [distributor]> create table contracts
    -> (contract_num integer not null, 
    -> contract_date datetime not null, 
    -> contract_id char(10) not null);
Query OK, 0 rows affected (0.149 sec)
```

This statement creates the Orders table used throughout this book. Orders contains three columns: order number, order date, and the customer ID. All three columns are required, and so each contains the keyword NOT NULL. This will prevent the insertion of columns with no value. If someone tries to insert no value, an error will be returned, and the insertion will fail.

This next example creates a table with a mixture of NULL and NOT NULL columns:

```python
MySQL [distributor]> create table suppliers
    -> supplier_id char(10) not null,
    -> supplier_name char(50) not null,
    -> supplier_address char(50),
    -> supplier_city char(50),
    -> supplier_state char(50),
    -> supplier_zip char(10)  --漏掉了comma, 
    -> supplier_country char(50) );
ERROR 1064 (42000): You have an error in your SQL syntax; check the manual that corresponds to your MySQL server version for the right syntax to use near 'supplier_id char(10) not null,
supplier_name char(50) not null,
supplier_address' at line 2
```

This statement creates the Vendors table used throughout this book. The vendor ID and vendor name columns are both required, and are, therefore, specified as NOT NULL. The five remaining columns all allow NULL values, and so NOT NULL is not specified. NULL is the default setting, so if NOT NULL is not specified NULL is assumed.

> **Caution: Specifying NULL**
>
>  Most DBMSs treat the absence of NOT NULL to mean NULL. However, not all do. Some DBMSs requires the keyword NULL and will generate an error if it is not specified. Refer to your DBMS documentation for complete syntax information.

> **Tip: Primary Keys and NULL Values** 
>
> Back in Lesson 1, you learned that primary keys are columns whose values uniquely identify every row in a table. Only columns that do not allow NULL values can be used in primary keys. Columns that allow no value at all cannot be used as unique identifiers.

> **Caution: Understanding NULL** 
>
> Don’t confuse NULL values with empty strings. A NULL value is the lack of a value; it is not an empty string. If you were to specify '' (two single quotes with nothing in between them), that would be allowed in a NOT NULL column. An empty string is a valid value; it is not no value. NULL values are specified with the keyword NULL, not with an empty string.



### Specifying Default Values

SQL enables you to specify default values to be used if no value is specified when a row is inserted. Default values are specified using the DEFAULT keyword in the column definitions in the CREATE TABLE statement.

Look at the following example:

```python
MySQL [distributor]> create table ContractItems
    -> (
    -> contract_num integer not null,
    -> contract_item integer not null,
    -> prod_id char(10) not null,
    -> quantity integer not null default 1, 
    -> item_price decimal(8,2) not null
    -> );
Query OK, 0 rows affected (0.135 sec)
```

This statement creates the OrderItems table that contains the individual items that make up an order. (The order itself is stored in the Orders table.) The quantity column contains the quantity for each item in an order. In this example, adding the text DEFAULT 1 to the column description instructs the DBMS to use a quantity of 1 if no quantity is specified.

Default values are often used to store values in date or time stamp columns. For example, the system date can be used as a default date by specifying the function or variable used to refer to the system date. For example, MySQL users may **specify DEFAULT CURRENT_DATE(),** while Oracle users may specify DEFAULT SYSDATE, and SQL Server users may specify DEFAULT GETDATE().

![Screen Shot 2018-08-13 at 11.11.20 AM](http://heropublic.oss-cn-beijing.aliyuncs.com/031136.png)



> **Tip: Using DEFAULT Instead of NULL Values**
>
> Many database developers use DEFAULT values instead of NULL columns, especially in columns that will be used in calculations or data groupings.





## Updating Tables

To update table definitions, the ALTER TABLE statement is used. Although all DBMSs support ALTER TABLE, what they allow you to alter **varies dramatically from one to another.** Here are some points to consider when using ALTER TABLE:

-  I**deally, tables should never be altered after they contain data.** You should spend sufficient time anticipating future needs during t**he table design pro**cess so that extensive changes are not required later on.
- All DBMSs allow you to add columns to existing tables, although some restrict the datatypes that may be added (as well as NULL and DEFAULT usage).
- Many DBMSs do not allow you to remove or change columns in a table.
- Most DBMSs allow you to rename columns.
- Many DBMSs restrict the kinds of changes you can make on columns that are populated and enforce fewer restrictions on unpopulated columns.

As you can see, making changes to existing tables is **neither simple nor consistent**. Be sure to refer to your own DBMS documentation to determine exactly what you can alter

To change a table using ALTER TABLE, you must specify the following information: 

- The name of the table to be altered after the keywords ALTER TABLE. (The table must exist or an error will be generated.) 
- The list of changes to be made. 

Because adding columns to an existing table is about the only operation supported by all DBMSs, I’ll use that for an example:

```python
MySQL [distributor]> alter table suppliers add supplier_phone char(20);
Query OK, 0 rows affected (0.089 sec)
Records: 0  Duplicates: 0  Warnings: 0
```

This statement adds a column named vend_phone to the Vendors table. The datatype must be specified. Other alter operations, for example, changing or dropping columns, or adding constraints or keys, use a similar syntax. (Note that the following example will not work with all DBMSs):

```python
MySQL [distributor]> alter table suppliers
    -> drop column supplier_phone;
Query OK, 0 rows affected (0.219 sec)
Records: 0  Duplicates: 0  Warnings: 0
```

Complex table structure changes usually require a manual move process involving these steps: 

1. Create a new table with the new column layout. 
2. Use the INSERT SELECT statement (see Lesson 15, “Inserting Data,” for details of this statement) to copy the data from the old table to the new table. Use conversion functions and calculated fields, if needed.
3. Verify that the new table contains the desired data. 
4. Rename the old table (or delete it, if you are really brave). 
5. Rename the new table with the name previously used by the old table. 
6. Recreate any triggers, stored procedures, indexes, and foreign keys as needed.

> **Note: ALTER TABLE and SQLite**
>
> SQLite limits the operations that may be performed using ALTER TABLE. One of the most important limitations is that it does not support the use of ALTER TABLE to defined primary and foreign keys, these must be specified at initial CREATE TABLE time.

> **Caution: Use ALTER TABLE Carefully**
>
> Use ALTER TABLE with extreme caution, and be sure you have a complete set of backups (both schema and data) before proceeding. Database table changes cannot be undone—and if you add columns you don’t need, you might not be able to remove them. Similarly, if you drop a column that you do need, you might lose all the data in that column.



## Deleting Tables

Deleting tables (actually removing the entire table, not just the contents) is very easy —arguably too easy. Tables are deleted using the DROP TABLE statement:

```python
DROP TABLE CustCopy;
```

This statement deletes the CustCopy table. (You created that one in Lesson 15.) There is no confirmation, nor is there an undo—executing the statement will permanently remove the table.

> **Tip: Using Relational Rules to Prevent Accidental Deletion**
>
> Many DBMSs allow you to enforce rules that prevent the dropping of tables that are related to other tables. When these rules are enforced, if you issue a DROP TABLE statement against a table that is part of a relationship, the DBMS blocks the operation until the relationship was removed. It is a good idea to enable these options, if available, to prevent the accidental dropping of needed tables.



## Renaming Tables

Table renaming is supported differently by each DBMS. There is no hard and fast standard for this operation. DB2, MariaDB, MySQL, Oracle, and PostgreSQL users can use the RENAME statement. SQL Server users can use the supplied sp_rename stored procedure. SQLite supports the renaming of tables via the ALTER TABLE statement.

The basic syntax for all rename operations requires that you specify the old name and a new name, however there are DBMS implementation differences. Refer to your own DBMS documentation for details on supported syntax.

## Summary

In this lesson, you learned several new SQL statements. CREATE TABLE is used to create new tables, ALTER TABLE is used to **change table columns** (or other objects like constraints or indexes), and DROP TABLE is used to completely delete a table. These statements should be used with **extreme caution**, and only after backups have been made. As the exact syntax of each of these statements varies from one DBMS to another, you should consult your own DBMS documentation for more information.



> Foreign-Key首先是数据类型其次才是connecting point

