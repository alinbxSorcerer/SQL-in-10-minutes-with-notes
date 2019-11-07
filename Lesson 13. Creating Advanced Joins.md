## Lesson 13. Creating Advanced Joins

In this lesson, you’ll learn all about additional join types—what they are, and how to use them. You’ll also learn how to use table aliases and how to use aggregate functions with joined tables.



## Using Table Aliases

Back In Lesson 7, “Creating Calculated Fields,” you learned how to use aliases to refer to retrieved table columns. The syntax to alias a column looks like this:

```python
MySQL [distributor]> select concat(vend_name, " (", "vend_country", ") ")
    -> as vend_title
    -> from vendors
    -> order by vend_name;
+---------------------------------+
| vend_title                      |
+---------------------------------+
| Bear Emporium (vend_country)    |
| Bears R Us (vend_country)       |
| Doll House Inc. (vend_country)  |
| Fun and Games (vend_country)    |
| Furball Inc. (vend_country)     |
| Jouets et ours (vend_country)   |
+---------------------------------+
6 rows in set (0.073 sec)
```

In addition to using aliases for column names and calculated fields, SQL also enables you to alias table names. There are two primary reasons to do this: 

- To shorten the SQL syntax 

-  To enable multiple uses of the same table within a single SELECT statement 

Take a look at the following SELECT statement. It is basically the same statement as an example used in the previous lesson, but it has been modified to use aliases:

```python
MySQL [distributor]> select cust_name, cust_contact
    -> from customers as c, orders as o, orderitems as oi
    -> where c.cust_id=o.cust_id
    -> and oi.order_num = o.order_num
    -> and prod_id = "rgan01";
+---------------+--------------------+
| cust_name     | cust_contact       |
+---------------+--------------------+
| Fun4All       | Denise L. Stephens |
| The Toy Store | Kim Howard         |
+---------------+--------------------+
2 rows in set (0.271 sec)
```

You’ll notice that the three tables in the FROM clauses all have aliases. Customers AS C establishes C as an alias for Customers, and so on. This enables you to use the abbreviated C instead of the full text Customers. In this example, the table aliases were used only in the WHERE clause, but aliases are not limited to just WHERE. You can use aliases in the SELECT list, the ORDER BY clause, and in any other part of the statement as well.

> **Caution: No AS in Oracle** 
>
> Oracle does not support the AS keyword. To use aliases in Oracle simply specify the alias without AS (so Customers C instead of Customers AS C).

It is also worth noting that table aliases are only used during query execution. Unlike column aliases, table aliases are never returned to the client.

## Using Different Join Types

So far, you have used only simple joins known as inner joins or equijoins. You’ll Now take a look at three additional join types: the self join, the natural join, and the outer join.

### Self Joins

As I mentioned earlier, one of the primary reasons to use table aliases is to be able to refer to the same table more than once in a single SELECT statement. An example will demonstrate this.

Suppose you wanted to send a mailing to all the customer contacts who work for the same company for which Jim Jones works. This query requires that you first find out which company Jim Jones works for, and next which customers work for that company. The following is one way to approach this problem:

```python
MySQL [distributor]> select cust_id, cust_name, cust_contact
    -> from customers
    -> where cust_name = (select cust_name from customers where cust_contact="Jim Jones");
+------------+-----------+--------------------+
| cust_id    | cust_name | cust_contact       |
+------------+-----------+--------------------+
| 1000000003 | Fun4All   | Jim Jones          |
| 1000000004 | Fun4All   | Denise L. Stephens |
+------------+-----------+--------------------+
2 rows in set (0.075 sec)
```

This first solution uses subqueries. The inner SELECT statement does a simple retrieval to return the cust_name of the company that Jim Jones works for. That name is the one used in the WHERE clause of the outer query so that all employees who work for that company are retrieved. (You learned all about subqueries in Lesson 11, “Working with Subqeries,” refer to that lesson for more information.)

Now look at the same query using a join:

```python
MySQL [distributor]> select c1.cust_id, c1.cust_name, c1.cust_contact
    -> from customers as c1, customers as c2
    -> where c1.cust_name = c2.cust_name
    -> and c2.cust_contact = "Jim Jones";
+------------+-----------+--------------------+
| cust_id    | cust_name | cust_contact       |
+------------+-----------+--------------------+
| 1000000003 | Fun4All   | Jim Jones          |
| 1000000004 | Fun4All   | Denise L. Stephens |
+------------+-----------+--------------------+
2 rows in set (0.040 sec)
```

The two tables needed in this query are actually the same table, and so the Customers table appears in the FROM clause twice. Although this is perfectly legal, any references to table Customers would be ambiguous because the DBMS does not know which Customers table you are referring to.

To resolve this problem table aliases are used. The first occurrence of Customers has an alias of C1, and the second has an alias of C2. Now those aliases can be used as table names. The SELECT statement, for example, uses the C1 prefix to explicitly state the full name of the desired columns. If it did not, the DBMS would return an error because there are two of each column named cust_id, cust_name, and cust_contact. It cannot know which one you want. (Even though they are the same.) The WHERE clause first joins the tables and then filters the data by cust_contact in the second table to return only the wanted data.

> **Tip: Self-Joins Instead of Subqueries** 
>
> Self-joins are often used to replace statements using subqueries that retrieve data from the same table as the outer statement. Although the end result is the same, many DBMSs process joins far more quickly than they do subqueries. It is usually worth experimenting with both to determine which performs better.

> 这个self的思路很有意思,仿佛回到了小学时代.



### Natural Joins

Whenever tables are joined, at least one column will appear in more than one table (the columns being used to create the join). Standard joins (the inner joins that you learned about in the last lesson) return all data, even **multiple occurrences** of the same column. A natural join simply eliminates those multiple occurrences so that only one of each column is returned.

How does it do this? The answer is it doesn’t—you do it. A natural join is a join in which you select only columns that are unique. This is typically done using a wildcard (SELECT *) for one table and explicit subsets of the columns for all other tables. The following is an example:

![Screen Shot 2018-08-12 at 10.32.46 PM](http://heropublic.oss-cn-beijing.aliyuncs.com/143411.png)

### Outer Joins

Most joins relate rows in one table with rows in another. But occasionally, you want to include rows that have no related rows. For example, you might use joins to accomplish the following tasks:

- Count how many orders were placed by each customer, including customers that have yet to place an order.
- List all products with order quantities, including products not ordered by anyone.
- Calculate average sale sizes, taking into account customers that have not yet placed an order.

In each of these examples, the join includes table rows that have no associated rows in the related table. This type of join is called an outer join.

> **Caution: Syntax Differences**
>
> It is important to note that the syntax used to create an outer join can vary slightly among different SQL implementations. The various forms of syntax described in the following section cover most implementations, but refer to your DBMS documentation to verify its syntax before proceeding.

The following SELECT statement is a simple inner join. It retrieves a list of all customers and their orders:

```python
MySQL [distributor]> select customers.cust_id, orders.order_num
    -> from customers inner join orders
    -> on customers.cust_id = orders.cust_id;
+------------+-----------+
| cust_id    | order_num |
+------------+-----------+
| 1000000001 |     20005 |
| 1000000001 |     20009 |
| 1000000003 |     20006 |
| 1000000004 |     20007 |
| 1000000005 |     20008 |
+------------+-----------+
5 rows in set (0.051 sec)
```

Outer join syntax is similar. To retrieve a list of all customers including those who have placed no orders, you can do the following:

```python
MySQL [distributor]> select customers.cust_id, orders.order_num
    -> from customers left outer join orders
    -> on customers.cust_id = orders.cust_id;
+------------+-----------+
| cust_id    | order_num |
+------------+-----------+
| 1000000001 |     20005 |
| 1000000001 |     20009 |
| 1000000002 |      NULL |
| 1000000003 |     20006 |
| 1000000004 |     20007 |
| 1000000005 |     20008 |
+------------+-----------+
6 rows in set (0.012 sec)
```

Like the inner join seen in the last lesson, this SELECT statement uses the keywords OUTER JOIN to specify the join type (instead of specifying it in the WHERE clause). But unlike inner joins, which relate rows in both tables, outer joins also include rows with no related rows. When using OUTER JOIN syntax you must use the RIGHT or LEFT keywords to specify the table from which to include all rows (RIGHT for the one on the right of OUTER JOIN, and LEFT for the one on the left). The previous example uses LEFT OUTER JOIN to select all the rows from the table on the left in the FROM clause (the Customers table). To select all the rows from the table on the right, you use a RIGHT OUTER JOIN as seen in this next example:

```python
MySQL [distributor]> select customers.cust_id, orders.order_num
    -> from customers right outer join orders
    -> on orders.cust_id = customers.cust_id;
+------------+-----------+
| cust_id    | order_num |
+------------+-----------+
| 1000000001 |     20005 |
| 1000000001 |     20009 |
| 1000000003 |     20006 |
| 1000000004 |     20007 |
| 1000000005 |     20008 |
+------------+-----------+
5 rows in set (0.042 sec)
```

> **Tip: Outer Join Types** 
>
> Remember that there are always two basic forms of outer joins—the left outer join and the right outer join. The only difference between them is the order of the tables that they are relating. In other words, a left outer join can be turned into a right outer join simply by reversing the order of the tables in the FROM or WHERE clause. As such, the two types of outer join can be used interchangeably, and the decision about which one is used is based purely on convenience.

There is one other variant of the outer join, and that is the full outer join that retrieves all rows from both tables and relates those that can be related. Unlike a left outer join or right outer join, which includes unrelated rows from a single table, the full outer join includes unrelated rows from both tables. The syntax for a full outer join is as follows:



## Using Joins with Aggregate Functions

As you learned in Lesson 9, “Summarizing Data,” aggregate functions are used to summarize data. Although all the examples of aggregate functions thus far only summarized data from a single table, these functions can also be used with joins.

To demonstrate this, let’s look at an example. You want to retrieve a list of all customers and the number of orders that each has placed. The following code uses the COUNT() function to achieve this:

```python
MySQL [distributor]> select customers.cust_id, count(orders.order_num) as num_order
    -> from customers, orders
    -> where customers.cust_id = orders.cust_id
    -> group by customers.cust_id;
+------------+-----------+
| cust_id    | num_order |
+------------+-----------+
| 1000000001 |         2 |
| 1000000003 |         1 |
| 1000000004 |         1 |
| 1000000005 |         1 |
+------------+-----------+
4 rows in set (0.025 sec)
```

This SELECT statement uses INNER JOIN to relate the Customers and Orders tables to each other. The GROUP BY clause groups the data by customer, and so the function call COUNT(Orders.order_num) counts the number of orders for each customer and returns it as num_ord.

Aggregate functions can be used just as easily with other join types. See the following example:

```python
MySQL [distributor]> select customers.cust_id, count(orders.order_num) as num_order from customers, orders where customers.cust_id = orders.cust_id group by customers.cust_id;
+------------+-----------+
| cust_id    | num_order |
+------------+-----------+
| 1000000001 |         2 |
| 1000000003 |         1 |
| 1000000004 |         1 |
| 1000000005 |         1 |
+------------+-----------+
4 rows in set (0.043 sec)
```

This SELECT statement uses INNER JOIN to relate the Customers and Orders tables to each other. The GROUP BY clause groups the data by customer, and so the function call COUNT(Orders.order_num) counts the number of orders for each customer and returns it as num_ord.

Aggregate functions can be used just as easily with other join types. See the following example:

```python
MySQL [distributor]> select customers.cust_id, count(orders.order_num) as num_order
    -> from customers left outer join orders
    -> on customers.cust_id = orders.cust_id
    -> group by customers.cust_id
    -> ;
+------------+-----------+
| cust_id    | num_order |
+------------+-----------+
| 1000000001 |         2 |
| 1000000002 |         0 |
| 1000000003 |         1 |
| 1000000004 |         1 |
| 1000000005 |         1 |
+------------+-----------+
5 rows in set (0.042 sec)
```



## Using Join and Join Conditions

Before I wrap up our two lesson discussion on joins, I think it is worthwhile to summarize some key points regarding joins and their use: 

- Pay careful attention to the type of join being used. More often than not, you’ll want an inner join, but there are often valid uses for outer joins, too. 
-  Check your DBMS documentation for the exact join syntax it supports. (Most DBMSs use one of the forms of syntax described in these two lessons.) 
- Make sure you use the correct join condition (regardless of the syntax being used), or you’ll return incorrect data.
- Make sure you always provide a join condition, or you’ll end up with the Cartesian product.
- You may include multiple tables in a join and even have different join types for each. Although this is legal and often useful, make sure you test each join separately before testing them together. This will make troubleshooting far simpler.

## Summary

This lesson was a continuation of the last lesson on joins. This lesson started by teaching you how and why to use aliases, and then continued with a discussion on different join types and various forms of syntax used with each. You also learned how to use aggregate functions with joins, and some important do’s and dont’s to keep in mind when working with joins.





