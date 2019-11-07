# Lesson 18. Using Views

In this lesson you’ll learn exactly what views are, how they work, and when they should be used. You’ll also see how views can be used to simplify some of the SQL operations performed in earlier lessons.



## Understanding Views

**Views are virtual tables.** Unlike tables that contain data, views simply contain queries that dynamically retrieve data when used.

> **Note: DBMS Specific Support**
>
>  Microsoft Access does support views, but not in any way that is consistent with how SQL views usually work. As such, this lesson does not apply to Microsoft Access.
>
> Support for views was added to MySQL version 5. As such, this lesson will not be applicable if you are using earlier versions of MySQL.
>
> SQLite only supports read-only views, so views may be created and read, but their contents cannot be updated.
>

The best way to understand views is to look at an example. Back in Lesson 12, “Joining Tables,” you used the following SELECT statement to retrieve data from three tables:

```python
SELECT cust_name, cust_contact 
FROM Customers, Orders, OrderItems 
WHERE Customers.cust_id = Orders.cust_id 
AND OrderItems.order_num = Orders.order_num 
AND prod_id = 'RGAN01';
```

That query was used to retrieve the customers who had ordered a specific product. Anyone needing this data would have to understand the table structure, as well as how to create the query and join the tables. To retrieve the same data for another product (or for multiple products), the last WHERE clause would have to be modified.

Now imagine that you could wrap that entire query in a virtual table called ProductCustomers. You could then simply do the following to retrieve the same data:

```python
SELECT cust_name, cust_contact 
FROM ProductCustomers 
WHERE prod_id = 'RGAN01';
```

This is where views come into play. ProductCustomers is a view, and as a view, it does not contain any columns or data. Instead it contains a query—the same query used above to join the tables properly.

> **Tip: DBMS Consistenc**y 
>
> You’ll be relieved to know that view creation syntax is supported pretty consistently by all the major DBMSs.



### Why Use Views

You’ve already seen one use for views. Here are some other common uses:

- To reuse SQL statements.
- To simplify complex SQL operations. After the query is written, it can be reused easily, without having to know the details of the underlying query itself.
- To expose parts of a table instead of complete tables.
- To secure data. Users can be given access to specific subsets of tables instead of to entire tables.
- To change data formatting and representation. Views can return data formatted and presented differently from their underlying tables.

For the most part, after views are created, they can be used in the same way as tables. You can perform SELECT operations, filter and sort data, join views to other views or tables, and possibly even add and update data. (There are some restrictions on this last item. More on that in a moment.)

The important thing to remember is views are just that, views into data stored elsewhere. Views contain no data themselves, so the data they return is retrieved from other tables. When data is added or changed in those tables, the views will return that changed data.

> **Caution: Performance Issues**
>
> Because views contain no data, any retrieval needed to execute a query must be processed every time the view is used. If you create complex views with multiple joins and filters, or if you nest views, you may find that performance is dramatically degraded. Be sure you test execution before deploying applications that use views extensively.



### View Rules and Restrictions

Before you create views yourself, there are some restrictions of which you should be aware. Unfortunately, the restrictions tend to be very DBMS specific, so check your own DBMS documentation before proceeding.

Here are some of the most common rules and restrictions governing view creation and usage:

- Like tables, views must be uniquely named. (They cannot be named with the name of any other table or view).
- There is no limit to the number of views that can be created.
- To create views, you must have security access. This is usually granted by the database administrator.
- Views can be nested; that is, a view may be built using a query that retrieves data from another view. The exact number of nested levels allowed varies from DBMS to DBMS. (Nesting views may seriously degrade query performance, so test this thoroughly before using it in production environments.)
- Many DBMSs prohibit the use of the ORDER BY clause in view queries.
- Some DBMSs require that every column returned be named—this will require the use of aliases if columns are calculated fields. (See Lesson 7, “Creating Calculated Fields,” for more information on column aliases.)
- Views cannot be indexed, nor can they have triggers or default values associated with them.
- Some DBMSs treat views as read-only queries—meaning you can retrieve data from views but not write data back to the underlying tables. Refer to your DBMS documentation for details.
- Some DBMSs allow you to create views that do not allow rows to be inserted or updated if that insertion or update will cause that row to no longer be part of the view. For example, if you have a view that retrieves only customers with email addresses, updating a customer to remove his email address would make that customer fall out of the view. This is the default behavior and is allowed, but depending on your DBMS you might be able to prevent this from occurring.

> **Tip: Refer to Your DBMS Documentation**
>
>  That’s a long list of rules, and your own DBMS documentation will likely contain additional rules, too. It is worth taking the time to understand what restrictions you must adhere to before creating views.



## Creating Views

So now that you know what views are (and the rules and restrictions that govern them), let’s look at view creation.

Views are created using the CREATE VIEW statement. Like CREATE TABLE, CREATE VIEW can only be used to create a view that does not exist.

> **Note: Renaming Views**
>
>  To remove a view, the DROP statement is used. The syntax is simply DROP VIEW viewname;.
>
> To overwrite (or update) a view you must first DROP it and then recreate it.



### Using Views to Simplify Complex Joins

```python
MySQL [distributor]> create view  ProductCustomers as
    -> select cust_name,cust_contact, prod_id
    -> from Customers, Orders, OrderItems
    -> where Customers.cust_id = Orders.cust_id
    -> and Orders.order_num = OrderItems.order_num;
Query OK, 0 rows affected (0.170 sec)

MySQL [distributor]> select * from ProductCustomers;
+---------------+--------------------+---------+
| cust_name     | cust_contact       | prod_id |
+---------------+--------------------+---------+
| Village Toys  | John Smith         | BR01    |
| Village Toys  | John Smith         | BR03    |
| Village Toys  | John Smith         | BNBG01  |
| Village Toys  | John Smith         | BNBG02  |
| Village Toys  | John Smith         | BNBG03  |
| Fun4All       | Jim Jones          | BR01    |
| Fun4All       | Jim Jones          | BR02    |
| Fun4All       | Jim Jones          | BR03    |
| Fun4All       | Denise L. Stephens | BR03    |
| Fun4All       | Denise L. Stephens | BNBG01  |
| Fun4All       | Denise L. Stephens | BNBG02  |
| Fun4All       | Denise L. Stephens | BNBG03  |
| Fun4All       | Denise L. Stephens | RGAN01  |
| The Toy Store | Kim Howard         | RGAN01  |
| The Toy Store | Kim Howard         | BR03    |
| The Toy Store | Kim Howard         | BNBG01  |
| The Toy Store | Kim Howard         | BNBG02  |
| The Toy Store | Kim Howard         | BNBG03  |
+---------------+--------------------+---------+
18 rows in set (0.110 sec)
```

This statement creates a view named ProductCustomers, which joins three tables to return a list of all customers who have ordered any product. If you were to SELECT * FROM ProductCustomers, you’d list every customer who ordered anything.

To retrieve a list of customers who ordered product RGAN01 you can do the following:

```python
MySQL [distributor]> select cust_name, cust_contact
    -> from ProductCustomers
    -> where prod_id = "rgan01";
+---------------+--------------------+
| cust_name     | cust_contact       |
+---------------+--------------------+
| Fun4All       | Denise L. Stephens |
| The Toy Store | Kim Howard         |
+---------------+--------------------+
2 rows in set (0.035 sec)
```

This statement retrieves specific data from the view by issuing a WHERE clause. When the DBMS processes the request, it adds the specified WHERE clause to any existing WHERE clauses in the view query so that the data is filtered correctly.

As you can see, views can greatly simplify the use of complex SQL statements. Using views, you can write the underlying SQL once and then reuse it as needed.

> **Tip: Creating Reusable Views** 
>
> It is a good idea to create views that are not tied to specific data. For example, the view created above returns customers for all products, not just product RGAN01 (for which the view was first created). Expanding the scope of the view enables it to be reused, making it even more useful. It also eliminates the need for you to create and maintain multiple similar views.

### Using Views to Reformat Retrieved Data

As mentioned above, another common use of views is for reformatting retrieved data. The following SELECT statement (from Lesson 7) returns vendor name and location in a single combined calculated column:

```python
order by vend_name' at line 2
MySQL [distributor]> select concat(trim(vend_name), " (", trim(vend_country), ") ") as vend_title from vendors order by vend_name;
+--------------------------+
| vend_title               |
+--------------------------+
| Bear Emporium (USA)      |
| Bears R Us (USA)         |
| Doll House Inc. (USA)    |
| Fun and Games (England)  |
| Furball Inc. (USA)       |
| Jouets et ours (France)  |
+--------------------------+
6 rows in set (0.096 sec)
```

Now suppose that you regularly needed results in this format. Rather than perform the concatenation each time it was needed, you could create a view and use that instead. To turn this statement into a view, you can do the following:

> **Note: SELECT Restrictions All Appl**y 
>
> Earlier in this lesson I stated that the syntax used to create views was a rather consistent between DBMSs. So why multiple versions of statements? A view simply wraps a SELECT statement, and the syntax of that SELECT must adhere to all the rules and restrictions of the DBMS being used.



### Using Views to Filter Unwanted Data

Views are also useful for applying common WHERE clauses. For example, you might want to define a CustomerEMailList view so that it filters out customers without e-mail addresses. To do this, you can use the following statement

```python
MySQL [distributor]> CREATE VIEW CustomerEMailList AS
    -> SELECT cust_id, cust_name, cust_email
    -> FROM Customers
    -> WHERE cust_email IS NOT NULL;
Query OK, 0 rows affected (0.026 sec)

MySQL [distributor]> select * from customeremaillist;
+------------+---------------+-----------------------+
| cust_id    | cust_name     | cust_email            |
+------------+---------------+-----------------------+
| 1000000001 | Village Toys  | sales@villagetoys.com |
| 1000000003 | Fun4All       | jjones@fun4all.com    |
| 1000000004 | Fun4All       | dstephens@fun4all.com |
| 1000000005 | The Toy Store | kim@thetoystore.com   |
| 1000000006 | toy land      | sam@toyland.com       |
+------------+---------------+-----------------------+
5 rows in set (0.014 sec)
```

Obviously, when sending e-mail to a mailing list you’d want to ignore users that have no e-mail address. The WHERE clause here filters out those rows that have NULL 

values in the cust_email columns so that they’ll not be retrieved.

View CustomerEMailList can now be used like any table.

> **Note: WHERE Clauses and WHERE Clauses** 
>
> If a WHERE clause is used when retrieving data from the view, the two sets of clauses (the one in the view and the one passed to it) will be combined automatically.

Views are exceptionally useful for simplifying the use of calculated fields. The following is a SELECT statement introduced in Lesson 7. It retrieves the order items for a specific order, calculating the expanded price for each item:

```python
MySQL [distributor]> select prod_id, quantity, item_price, quantity*item_price as expanded_price from orderitems where order_num = 20008;
+---------+----------+------------+----------------+
| prod_id | quantity | item_price | expanded_price |
+---------+----------+------------+----------------+
| RGAN01  |        5 |       4.99 |          24.95 |
| BR03    |        5 |      11.99 |          59.95 |
| BNBG01  |       10 |       3.49 |          34.90 |
| BNBG02  |       10 |       3.49 |          34.90 |
| BNBG03  |       10 |       3.49 |          34.90 |
+---------+----------+------------+----------------+
5 rows in set (0.061 sec)
```

To turn this into a view, do the following:

```python
MySQL [distributor]> select * from OrderItemsExpanded;
+-----------+---------+----------+------------+----------------+
| order_num | prod_id | quantity | item_price | expanded_price |
+-----------+---------+----------+------------+----------------+
|     20005 | BR01    |      100 |       5.49 |         549.00 |
|     20005 | BR03    |      100 |      10.99 |        1099.00 |
|     20006 | BR01    |       20 |       5.99 |         119.80 |
|     20006 | BR02    |       10 |       8.99 |          89.90 |
|     20006 | BR03    |       10 |      11.99 |         119.90 |
|     20007 | BR03    |       50 |      11.49 |         574.50 |
|     20007 | BNBG01  |      100 |       2.99 |         299.00 |
|     20007 | BNBG02  |      100 |       2.99 |         299.00 |
|     20007 | BNBG03  |      100 |       2.99 |         299.00 |
|     20007 | RGAN01  |       50 |       4.49 |         224.50 |
|     20008 | RGAN01  |        5 |       4.99 |          24.95 |
|     20008 | BR03    |        5 |      11.99 |          59.95 |
|     20008 | BNBG01  |       10 |       3.49 |          34.90 |
|     20008 | BNBG02  |       10 |       3.49 |          34.90 |
|     20008 | BNBG03  |       10 |       3.49 |          34.90 |
|     20009 | BNBG01  |      250 |       2.49 |         622.50 |
|     20009 | BNBG02  |      250 |       2.49 |         622.50 |
|     20009 | BNBG03  |      250 |       2.49 |         622.50 |
+-----------+---------+----------+------------+----------------+
18 rows in set (0.047 sec)
```

```python
MySQL [distributor]> select order_num, sum(expanded_price) from OrderItemsExpanded group by order_num;
+-----------+---------------------+
| order_num | sum(expanded_price) |
+-----------+---------------------+
|     20005 |             1648.00 |
|     20006 |              329.60 |
|     20007 |             1696.00 |
|     20008 |              189.60 |
|     20009 |             1867.50 |
+-----------+---------------------+
5 rows in set (0.016 sec)
```

As you can see, views are easy to create and even easier to use. Used correctly, views can greatly simplify complex data manipulation.

## Summary

Views are virtual tables. They do not contain data, but instead, they contain queries that retrieve data as needed. Views provide a level of encapsulation around SQL SELECT statements and can be used to simplify data manipulation, as well as to **reformat or secure underlying data**.

