 # Lesson 7. Creating Calculated Fields

In this lesson, you will learn what calculated fields are, how to create them, and how to use aliases to refer to them from within your application.

## Understanding Calculated Fields

Data stored within a database’s tables is often not available in the exact format needed by your applications. Here are some examples:

- You need to display a field containing the name of a company along with the company’s location, but that information is stored in separated table columns.
- City, State, and ZIP codes are stored in separate columns (as they should be), but your mailing label printing program needs them retrieved as one correctly formatted field.
- Column data is in mixed upper and lowercase, and your report needs all data presented in uppercase.
- An Order Items table stores item price and quantity, but not the expanded price (price multiplied by quantity) of each item. To print invoices, you need that **expanded price**.
- You need total, averages, or other calculations based on table data.

In each of these examples, the data stored in the table is not exactly what your application needs. Rather than retrieve the data as it is, and then reformat it within your client application or report, what you really want is to retrieve **converted, calculated, or reformatted data** directly from the database.

This is where calculated fields **come in.** Unlike all the columns that we retrieved in the lessons thus far, calculated fields don’t actually exist in database tables. Rather, a calculated field is created **on-the-fly** within a SQL SELECT statement.

> **Field**
>
> Essentially means the same thing as column and often used interchangeably, although database columns are typically called columns and the term fields is usually used in conjunction with calculated fields.

It is important to note that only the database knows which columns in a SELECT statement are actual table columns and which are calculated fields. From the perspective of a client (for example, your application), a calculated field’s data is returned in the same way as data from any other column.

> **Tip: Client Versus Server Formatting**
>
> Many of the conversions and reformatting that can be performed within SQL statements can also be performed directly in your client application. However, as a rule, it is far quicker to perform these operations on the database server than it is to perform them within the client.



## Concatenating Fields

To demonstrate working with calculated fields, let’s start with a simple example— creating a title that is made up of two columns.

The Vendors table contains vendor name and address information. Imagine that you were generating a vendor report, and needed to list the vendor location as part of the vendor name, in the format name (location).

The reports wants a single value, and the data in the table is stored in two columns: vend_name and vend_country. In addition, you need to surround vend_country with parentheses, and those are definitely not stored in the database table. The SELECT statement that returns the vendor names and locations is simple enough, but how would you create this combined value?

>  **Concatenate**
>
> Joining values together (by appending them to each other) to form a single long value.

The solution is to concatenate the three columns. In SQL SELECT statements, you can concatenate columns using a special operator. Depending on what DBMS you are using, this can be a plus sign (+) or two pipes (||). And in the case of MySQL and MariaDB, a special function must be used.

> **Note: + or ||?**
>
> Access and SQL Server support + for concatenation. DB2, Oracle, PostgreSQL, SQLite, and Open Office Base support ||. Refer to your DBMS documentation for more details.

Here’s an example using the plus sign (the syntax used by most DBMSs):

```python
MySQL [distributor]> select vend_name + " (" + vend_country + ")" from vendors order by vend_name;
+---------------------------------------+
| vend_name + " (" + vend_country + ")" |
+---------------------------------------+
|                                     0 |
|                                     0 |
|                                     0 |
|                                     0 |
|                                     0 |
|                                     0 |
+---------------------------------------+
6 rows in set, 24 warnings (0.049 sec)
```

And here’s what you’ll need to do if using MySQL or MariaDB:

```python
MySQL [distributor]> select concat(vend_name, "(", vend_country, ")")
    -> from vendors
    -> order by vend_name;
+-------------------------------------------+
| concat(vend_name, "(", vend_country, ")") |
+-------------------------------------------+
| Bear Emporium(USA)                        |
| Bears R Us(USA)                           |
| Doll House Inc.(USA)                      |
| Fun and Games(England)                    |
| Furball Inc.(USA)                         |
| Jouets et ours(France)                    |
+-------------------------------------------+
6 rows in set (0.022 sec)
```

> con + cat 抓到一起

The above SELECT statements concatenate the following elements:

- The name stored in the vend_name column 
-  A string containing a space and an open parenthesis 
- The state stored in the vend_country column 
- A string containing the close parenthesis

As you can see in the output shown above, the SELECT statement returns a single column (a calculated field) containing all these four elements as one unit.

 Look again at the output returned by the SELECT statement. The two columns that are incorporated into the calculated field are padded with spaces. Many databases (although not all) save text values padded to the column width, so your own results may indeed not contain those extraneous spaces. To return the data formatted properly, you must trim those padded spaces. This can be done using the SQL RTRIM() function, as follows:

The RTRIM() function trims all space from the right of a value. By using RTRIM(), the individual columns are all trimmed properly.

> **Note: The TRIM Functions** 
>
> Most DBMSs support RTRIM() (which as just seen, trims the right side of a string), as well as LTRIM() which trims the left side of a string, and TRIM() which trims both the right and left.

### Using Aliases

The SELECT statement used to concatenate the address field works well, as seen in the above output. But what is the name of this new calculated column? Well, the truth is, it has no name; it is simply a value. Although this can be fine if you are just looking at the results in a SQL query tool, an unnamed column cannot be used within a client application because there is no way for the client to refer to that column.

To solve this problem, SQL supports column aliases. An alias is just that, an alternate name for a field or value. Aliases are assigned with the AS keyword. Take a look at the following SELECT statement:

```python
MySQL [distributor]> select concat(vend_name, " (", vend_country, " )") as vend_title
    -> from vendors
    -> order by vend_name;
+--------------------------+
| vend_title               |
+--------------------------+
| Bear Emporium (USA )     |
| Bears R Us (USA )        |
| Doll House Inc. (USA )   |
| Fun and Games (England ) |
| Furball Inc. (USA )      |
| Jouets et ours (France ) |
+--------------------------+
6 rows in set (0.012 sec)
```

The SELECT statement itself is the same as the one used in the previous code snippet, except that here the calculated field is followed by the text AS vend_title. This instructs SQL to create a calculated field named vend_title containing the calculation specified. As you can see in the output, the results are the same as before, but the column is now named vend_title, and any client application can refer to this column by name, just as it would to any actual table column.

>  **Note: AS Often Optional**
>
>  Use of the AS keyword is optional in many DBMSs, but using it is considered a best practice.

> **Tip: Other Uses for Aliases** 
>
> Aliases have other uses too. Some common uses include renaming a column if the real table column name contains illegal characters (for example, spaces), and expanding column names if the original names are either ambiguous or easily misread.

> **Caution: Alias Names** 
>
> Aliases may be single words or complete strings. If the latter is used then the string should be enclosed within quotes. This practice is legal, but is strongly discouraged. While multiword names are indeed highly readable, they create all sorts of problems for many client applications. So much so, that one of the most common uses of aliases is to rename multiword column names to single word names (as explained above).

> **Note: Derived Columns** 
>
> Aliases are also sometimes referred to as “derived columns,” so regardless of the term you run across, they mean the same thing.



## Performing Mathematical Calcualtions

Another frequent use for calculated fields is performing mathematical calculations on retrieved data. Let’s take a look at an example. The Orders table contains all orders received, and the OrderItems table contains the individual items within each order. The following SQL statement retrieves all the items in order number 20008:

```python
MySQL [distributor]> select prod_id, quantity, item_price
    -> from orderitems
    -> where order_num = 20008;
+---------+----------+------------+
| prod_id | quantity | item_price |
+---------+----------+------------+
| RGAN01  |        5 |       4.99 |
| BR03    |        5 |      11.99 |
| BNBG01  |       10 |       3.49 |
| BNBG02  |       10 |       3.49 |
| BNBG03  |       10 |       3.49 |
+---------+----------+------------+
5 rows in set (0.046 sec)

MySQL [distributor]> select * from orderitems;
+-----------+------------+---------+----------+------------+
| order_num | order_item | prod_id | quantity | item_price |
+-----------+------------+---------+----------+------------+
|     20005 |          1 | BR01    |      100 |       5.49 |
|     20005 |          2 | BR03    |      100 |      10.99 |
|     20006 |          1 | BR01    |       20 |       5.99 |
|     20006 |          2 | BR02    |       10 |       8.99 |
|     20006 |          3 | BR03    |       10 |      11.99 |
|     20007 |          1 | BR03    |       50 |      11.49 |
|     20007 |          2 | BNBG01  |      100 |       2.99 |
|     20007 |          3 | BNBG02  |      100 |       2.99 |
|     20007 |          4 | BNBG03  |      100 |       2.99 |
|     20007 |          5 | RGAN01  |       50 |       4.49 |
|     20008 |          1 | RGAN01  |        5 |       4.99 |
|     20008 |          2 | BR03    |        5 |      11.99 |
|     20008 |          3 | BNBG01  |       10 |       3.49 |
|     20008 |          4 | BNBG02  |       10 |       3.49 |
|     20008 |          5 | BNBG03  |       10 |       3.49 |
|     20009 |          1 | BNBG01  |      250 |       2.49 |
|     20009 |          2 | BNBG02  |      250 |       2.49 |
|     20009 |          3 | BNBG03  |      250 |       2.49 |
+-----------+------------+---------+----------+------------+
18 rows in set (0.012 sec)
```

The item_price column contains the per unit price for each item in an order. To expand the item price (item price multiplied by quantity ordered), you simply do the following:

```python
MySQL [distributor]> select prod_id, 
    -> quantity,
    -> item_price,
    -> quantity*item_price as expanded_price
    -> from orderitems
    -> where order_num = 20008;
+---------+----------+------------+----------------+
| prod_id | quantity | item_price | expanded_price |
+---------+----------+------------+----------------+
| RGAN01  |        5 |       4.99 |          24.95 |
| BR03    |        5 |      11.99 |          59.95 |
| BNBG01  |       10 |       3.49 |          34.90 |
| BNBG02  |       10 |       3.49 |          34.90 |
| BNBG03  |       10 |       3.49 |          34.90 |
+---------+----------+------------+----------------+
5 rows in set (0.027 sec)
```

The **expanded_price** column shown in the output above is a calculated field; the calculation is simply quantity*item_price. The client application can now use this new calculated column just as it would any other column.

SQL supports the basic mathematical operators listed in Table 7.1. In addition, parentheses can be used to establish order of precedence. Refer to Lesson 5, “Advanced Data Filtering,” for an explanation of precedence.

**Table 7.1. SQL Mathematical Operators**

![Screen Shot 2018-08-12 at 10.40.56 AM](http://heropublic.oss-cn-beijing.aliyuncs.com/024821.jpg)



> **Tip: How to Test Calculations**
>
> SELECT provides a great way to test and experiment with functions and calculations. Although SELECT is usually used to retrieve data from a table, the FROM clause may be omitted to simply access and work with expressions. For example, SELECT 3 * 2; would return 6, SELECT Trim(' abc '); would return abc, and SELECT Now(); uses the Now() function to return the current date and time. You get the idea—use SELECT to experiment as needed.

## Summary

In this lesson, you learned what calculated fields are and how to create them. You used examples demonstrating the use of calculated fields for both string concatenation and mathematical operations. In addition, you learned how to create and use aliases so that your application can refer to calculated fields.