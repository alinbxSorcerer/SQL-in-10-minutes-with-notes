# Lesson 15. Inserting Data

In this lesson, you will learn how to insert data into tables using the SQL INSERT statement.



## Understanding Data Insertion

SELECT is undoubtedly the most frequently used SQL statement (which is why the last 14 lessons were dedicated to it). But there are three other frequently used SQL statements that you should learn. The first one is INSERT. (You’ll get to the other two in the next lesson.)

As its name suggests, INSERT is used to insert (add) rows to a database table. Insert can be used in several ways:

- Inserting a single complete row
- Inserting a single partial row
- Inserting the results of a query

Let’s now look at each of these.

> **Tip: INSERT and System Security**
>
> Use of the INSERT statement might require special security privileges in client-server DBMSs. Before you attempt to use INSERT, make sure you have **adequate** security privileges to do so.

### Inserting Complete Rows

The simplest way to insert data into a table is to use the basic INSERT syntax, which requires that you specify the table name and the values to be inserted into the new row. Here is an example of this:

```python
MySQL [distributor]> insert into customers
    -> values ("1000000006","toy land", "123 any street", "New York", "NY", "11111", "USA", NULL, NULL);
Query OK, 1 row affected (0.021 sec)
```

The above example inserts a new customer into the Customers table. The data to be stored in each table column is specified in the VALUES clause, and a value must be provided for every column. If a column has no value (for example, the cust_contact and cust_email columns above), the NULL value should be used (assuming the table allows no value to be specified for that column). The columns must be populated in the order in which they appear in the table definition.

> **Tip: The INTO Keyword** 
>
> In some SQL implementations, the INTO keyword following INSERT is optional. However, it is good practice to provide this keyword even if it is not needed. Doing so will ensure that your SQL code is portable between DBMSs.

Although this syntax is indeed simple, it is not at all safe and should generally be avoided at all costs. The above SQL statement is highly dependent on the order in which the columns are defined in the table. It also depends on information about that order being readily available. Even if it is available, there is no guarantee that the columns will be in the exact same order the next time the table is reconstructed. Therefore, writing SQL statements that depend on specific column ordering is very unsafe. If you do so, something will **inevitably** break at some point.

The safer (and unfortunately more **cumbersome**) way to write the INSERT statement is as follows:

```python
MySQL [distributor]> insert into customers (cust_id, cust_name, cust_address, cust_city, cust_state, cust_zip, cust_country, cust_contact,  cust_email) values ("1000000012", "boy land", "456 any street", "New York", "NY", "11111", "USA", NULL, NULL);
Query OK, 1 row affected (0.001 sec)
```

This example does the exact same thing as the **previous** INSERT statement, but this time the column names are explicitly stated in parentheses after the table name. When the row is inserted the DBMS will match each item in the columns list with the **appropriate** value in the VALUES list. The first entry in VALUES corresponds to the first specified column name. The second value corresponds to the second column name, and so on.

Because column names are provided, the VALUES must match the specified column names in the order in which they are specified, and not necessarily in the order that the columns appear in the actual table. The advantage of this is that, even if the table layout changes, the INSERT statement will still **work correctly.**

The following INSERT statement populates all the row columns (just as before), but it does so in a different order. Because the column names are specified, the insertion will work correctly:

```python
INSERT INTO Customers(cust_id, 
                      cust_contact, 
                      cust_email, 
                      cust_name, 
                      cust_address, 
                      cust_city, 
                      cust_state, 
                      cust_zip) 
VALUES('1000000006',
       NULL, 
       NULL, 
       'Toy Land', 
       '123 Any Street', 
       'New York', 
       'NY', 
       '11111');
```

>  **Tip: Always Use a Columns List** 
>
> As a rule, never use INSERT without explicitly specifying the column list. This will greatly increase the probability that your SQL will continue to function **in the event that table changes occur.**

> **Caution: Use VALUES Carefully** 
>
> Regardless of the INSERT syntax being used, the correct number of VALUES must be specified. If no column names are provided, **a value must be present for every table column.** If columns names are provided, a value must be present for each listed column. If none is present, an error message will be **generated,** and the row will not be inserted.



### Inserting Partial Rows

As I just explained, the recommended way to use INSERT is to explicitly specify table column names. Using this syntax, you can also omit columns. This means you only provide values for some columns, but not for others.

Look at the following example:

```python
INSERT INTO Customers(cust_id, 
                      cust_name, 
                      cust_address, 
                      cust_city, 
                      cust_state, 
                      cust_zip, 
                      cust_country) 
VALUES('1000000006',
       'Toy Land', 
       '123 Any Street', 
       'New York', 
       'NY', 
       '11111', 
       'USA');
```

In the examples given earlier in this lesson, values were not provided for two of the columns, cust_contact and cust_email. This means there is no reason to include those columns in the INSERT statement. This INSERT statement, therefore, omits the two columns and the two corresponding values.

> **Caution: Omitting Columns**
>
> You may omit columns from an INSERT operation if the table definition so allows. One of the following conditions must exist:
>
> - The column is defined as allowing NULL values (no value at all).
> - **A default value is specified in the table definition.** This means the default value will be used if no value is specified.

> **Caution: Omitting Required Values**
>
> If you omit a value from a table that does not allow NULL values and does not have a default, the DBMS will generate an error message, and the row will not be inserted.



### Inserting Retrieved Data

INSERT is usually used to add a row to a table using specified values. There is another form of INSERT that can be used to insert the result of a SELECT statement into a table. This is known as INSERT SELECT, and, as **its name suggests,** it is **made up of** an INSERT statement and a SELECT statement.

Suppose you want to merge a list of customers from another table into your Customers table. Instead of reading one row at a time and inserting it with INSERT, you can do the following:

```python
INSERT INTO Customers(cust_id, 
                      cust_contact, 
                      cust_email, 
                      cust_name, 
                      cust_address, 
                      cust_city, 
                      cust_state, 
                      cust_zip, 
                      cust_country)
SELECT cust_id, 
       cust_contact, 
       cust_email, 
       cust_name, 
       cust_address, 
       cust_city, 
       cust_state, 
       cust_zip, 
       cust_country 
       FROM CustNew;
```

> **Note: Instructions Needed for the Next Example**
>
> The following example imports data from a table named CustNew into the Customers table. To try this example, **create and populate the CustNew table first.** The format of the CustNew table should be the same as the Customers table described in Appendix, “Sample Table Scripts.” When populating CustNew, be sure not to use cust_id values that were already used in Customers. (The **subsequent** INSERT operation fails if primary key values are duplicated.)

This example uses INSERT SELECT to import all the data from CustNew into Customers. Instead of listing the VALUES to be inserted, the SELECT statement retrieves them from CustNew. Each column in the SELECT **corresponds** to a column in the specified columns list. How many rows will this statement insert? That depends on how many rows are in the CustNew table. If the table is empty, no rows will be inserted (and no error will be generated because the operation is still valid). If the table does, in fact, contain data, all that data is inserted into Customers.

> **Tip: Column Names in INSERT SELECT**
>
> This example uses the same column names in both the INSERT and SELECT statements for simplicity’s sake. But there is no requirement that the column names match. In fact, the DBMS does not even pay attention to the column names returned by the SELECT. Rather, the column position is used, so the first column in the SELECT (regardless of its name) will be used to populate the first specified table column, and so on.

The SELECT statement used in an INSERT SELECT can include a WHERE clause to filter the data to be inserted.

> **Tip: Inserting Multiple Rows**
>
> INSERT usually inserts only a single row. To insert multiple rows you must execute multiple INSERT statements. The exception to this rule is INSERT SELECT, which can be used to insert multiple rows with a single statement—whatever the SELECT statement returns will be inserted by the INSERT.



## Copying from One Table to Another

There is another form of data insertion that does not use the INSERT statement at all. To copy the contents of a table into a brand new table (one that is created on-the-fly) you can use the SELECT INTO statement.

Unlike INSERT SELECT, which appends data to an existing table, SELECT INTO copies data into a new table (and depending on the DBMS being used, can overwrite the table if it already exists).

> **Note: INSERT SELECT Versus SELECT INTO** 
>
> One way to explain the differences between SELECT INTO and INSERT SELECT is that the former exports data while the latter imports data.

The following example demonstrates the use of SELECT INTO:

```python
SELECT * INTO CustCopy FROM Customers;
```

This SELECT statement creates a new table named CustCopy and copies the entire contents of the Customers table into it. Because SELECT * was used, every column in the Customers table will be created (and populated) in the CustCopy table. To copy only a subset of the available columns, explicit column names can be specified instead of the * wildcard character.

MariaDB, MySQL, Oracle, PostegreSQL, and SQLite use a slightly different syntax:

```python
MySQL [distributor]> create table custcopy as 
    -> select * from customers;
Query OK, 7 rows affected (0.151 sec)
Records: 7  Duplicates: 0  Warnings: 0
```

> **Tip: Making Copies of Tables**
>
> SELECT INTO is a great way to make copies of tables before experimenting with new SQL statements. **By making a copy first, you’ll be able to test your SQL on that copy instead of on live data.**

> **Note: More Examples**
>
> Looking for more examples of INSERT usage? See the example table population scripts described in Appendix A.

## Summary

In this lesson, you learned how to INSERT rows into a database table. You learned several ways to use INSERT and **why explicit column specification is preferred.** You also learned how to use INSERT SELECT to import rows from another table and how to use SELECT INTO to export rows to a new table. In the next lesson, you learn how to use UPDATE and DELETE to further manipulate table data.

 