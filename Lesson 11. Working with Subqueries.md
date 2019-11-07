# Lesson 11. Working with Subqueries

In this lesson, you’ll learn what subqueries are and how to use them.

## Understanding Subqueries



SELECT statements are SQL queries. All the SELECT statements we have seen thus far are simple queries: single statements retrieving data from individual database tables.

> **Query** 
>
> Any SQL statement. However, the term is usually used to refer to SELECT statements.

SQL also enables you to create subqueries: queries that are embedded into other queries. Why would you want to do this? The best way to understand this concept is to look at a couple of examples.

## Filtering by Subquery

The database tables used in all the lessons in this book are **relational tables**. (See Appendix A, “Sample Table Scripts,” for a description of each of the tables and their relationships.) Orders are stored in two tables. The Orders table stores a single row for each order canontaining order number, customer ID, and order date. The individual order items are stored in the related OrderItems table. The Orders table does not store customer information. It only stores a customer ID. The actual customer information is stored in the Customers table.

Now suppose you wanted a list of all the customers who ordered item RGAN01. What would you have to do to retrieve this information? Here are the steps:

1. Retrieve the order numbers of all orders containing item RGAN01. 
2. Retrieve the customer ID of all the customers who have orders listed in the order numbers returned in the previous step.
3. Retrieve the customer information for all the customer IDs returned in the previous step.

Each of these steps can be executed as a separate query. By doing so, you use the results returned by one SELECT statement to populate the WHERE clause of the next SELECT statement.

You can also use subqueries to combine all three queries into one single statement.

The first SELECT statement should be self-explanatory by now. It retrieves the order_num column for all order items with a prod_id of RGAN01. The output lists the two orders containing this item:

```python
MySQL [distributor]> select order_num from orderitems where prod_id = "rgan01";
+-----------+
| order_num |
+-----------+
|     20007 |
|     20008 |
+-----------+
2 rows in set (0.097 sec)
```

Now that we know which orders contain the desired item, the next step is to retrieve the customer IDs associated with those orders numbers, 20007 and 20008. Using the IN clause described in Lesson 5, “Advanced Data Filtering,” you can create a SELECT statement as follows:

```python
MySQL [distributor]> select cust_id from orders where order_num in (20007, 20008);
+------------+
| cust_id    |
+------------+
| 1000000004 |
| 1000000005 |
+------------+
2 rows in set (0.019 sec)
```

Now, combine the two queries by turning the first (the one that returned the order numbers) into a subquery. Look at the following SELECT statement:

```python
MySQL [distributor]> select cust_id from orders
    -> where order_num in (select order_num from orderitems where prod_id = "rgan01");
+------------+
| cust_id    |
+------------+
| 1000000004 |
| 1000000005 |
+------------+
2 rows in set (0.104 sec)
```

Subqueries are always processed starting with the innermost SELECT statement and working outward. When the preceding SELECT statement is processed, the DBMS actually performs two operations.

First it runs the subquery:

```python
select order_num from orderitems where prod_id = "rgan01"
```

That query returns the two order numbers 20007 and 20008. Those two values are then passed to the WHERE clause of the outer query in the comma-delimited format required by the IN operator. The outer query now becomes

```python
SELECT cust_id FROM orders WHERE order_num IN (20007,20008)
```

As you can see, the output is correct and exactly the same as the output returned by the hard-coded WHERE clause above.

> **Tip: Formatting Your SQL** 
>
> SELECT statements containing subqueries can be difficult to read and debug, especially as they grow in complexity. Breaking up the queries over multiple lines and indenting the lines appropriately as shown here can greatly simplify working with subqueries.
>
> Incidentally, this is where color coding also becomes invaluable, and the better DBMS clients do indeed color code SQL for just this reason. And this is also why the SQL statements in this book have been printed in color for you, it makes reading them, isolating their sections, and troubleshooting them, so much easier.

You now have the IDs of all the customers who ordered item RGAN01. The next step is to retrieve the customer information for each of those customer IDs. The SQL statement to retrieve the two columns is

```python
MySQL [distributor]> select cust_name, cust_contact
    -> from customers
    -> where cust_id in ("1000000004", "1000000005");
+---------------+--------------------+
| cust_name     | cust_contact       |
+---------------+--------------------+
| Fun4All       | Denise L. Stephens |
| The Toy Store | Kim Howard         |
+---------------+--------------------+
2 rows in set (0.080 sec)
```

Instead of hard-coding those customer IDs, you can turn this WHERE clause into a subquery:

```python
MySQL [distributor]> select cust_name, cust_contact from customers where cust_id in (select cust_id from orders where order_num in (select order_num from orderitems where prod_id = "rgan01"));
+---------------+--------------------+
| cust_name     | cust_contact       |
+---------------+--------------------+
| Fun4All       | Denise L. Stephens |
| The Toy Store | Kim Howard         |
+---------------+--------------------+
2 rows in set (0.045 sec)

```

To execute the above SELECT statement, the DBMS had to actually perform three SELECT statements. The innermost subquery returned a list of order numbers that were then used as the WHERE clause for the subquery above it. That subquery returned a list of customer IDs that were used as the WHERE clause for the top-level query. The top-level query actually returned the desired data.

As you can see, using subqueries in a WHERE clause enables you to write extremely powerful and flexible SQL statements. There is no limit imposed on the number of subqueries that can be nested, although in practice you will find that performance will tell you when you are nesting too deeply.

> **Caution: Single Column Only** 
>
> Subquery SELECT statements can only retrieve a single column. Attempting to retrieve multiple columns will return an error.

> **Caution: Subqueries and Performance** 
>
> The code shown here works, and it achieves the desired result. However, using subqueries is not always the most efficient way to perform this type of data retrieval. More on this in Lesson 12, “Joining Tables,” where you will revisit this same example.



## Using Subqueries as Calculated Fields

Another way to use subqueries is in creating calculated fields. Suppose you want to **display the total number of orders placed by every customer** in your Customers table. Orders are stored in the Orders table along with the appropriate customer ID.

To perform this operation, follow these steps:

1. Retrieve the list of customers from the Customers table. 
2. For each customer retrieved, count the number of associated orders in the Orders table.

As you learned in the previous two lessons, you can use SELECT COUNT(*) to count rows in a table, and by providing a WHERE clause to filter a specific customer ID, you can count just that customer’s orders. For example, the following code counts the number of orders placed by customer 1000000001:

```python
MySQL [distributor]> select cust_id, count(*) as orders from orders group by cust_id;
+------------+--------+
| cust_id    | orders |
+------------+--------+
| 1000000001 |      2 |
| 1000000003 |      1 |
| 1000000004 |      1 |
| 1000000005 |      1 |
+------------+--------+
4 rows in set (0.000 sec)
```

To perform that COUNT(*) calculation for each customer, use COUNT* as a subquery. Look at the following code:

```python
MySQL [distributor]> 
select cust_name, cust_state, 
(select count(*) from orders where orders.cust_id = customers.cust_id) as orders 
from customers 
order by cust_name;
+---------------+------------+--------+
| cust_name     | cust_state | orders |
+---------------+------------+--------+
| Fun4All       | IN         |      1 |
| Fun4All       | AZ         |      1 |
| Kids Place    | OH         |      0 |
| The Toy Store | IL         |      1 |
| Village Toys  | MI         |      2 |
+---------------+------------+--------+
5 rows in set (0.098 sec)
```

> cust_name 一一对应,不需要group
>
> 奇怪的地方.

```python
MySQL [distributor]> select cust_name, cust_state, 
    -> (select count(*) from orders where orders.cust_id = customers.cust_id) as orders 
    -> from customers 
    -> group by cust_name
    -> order by cust_name;
ERROR 1055 (42000): Expression #2 of SELECT list is not in GROUP BY clause and contains nonaggregated column 'distributor.customers.cu
```



This SELECT statement returns three columns for every customer in the Customers table: cust_name, cust_state, and orders. Orders is a calculated field that is set by a subquery that is provided in parentheses. That subquery is executed once for every customer retrieved. In the example above, the subquery is executed five times because five customers were retrieved.

The WHERE clause in the subquery is a little different from the WHERE clauses used previously because it uses fully qualified column names, instead of just a column name (cust_id), it specifies the table and the column name (as Orders.cust_id and Customers.cust_id). The following WHERE clause tells SQL to compare the cust_id in the Orders table to the one currently being retrieved from the Customers table:

```python
WHERE Orders.cust_id = Customers.cust_id
```

will always return the total number of orders in the Orders table, the results will not be what you expected:

```python
MySQL [distributor]> select cust_id, cust_state,
    -> (select count(*) from orders where cust_id = cust_id) as orders 
    -> from customers
    -> order by cust_name;
+------------+------------+--------+
| cust_id    | cust_state | orders |
+------------+------------+--------+
| 1000000003 | IN         |      5 |
| 1000000004 | AZ         |      5 |
| 1000000002 | OH         |      5 |
| 1000000005 | IL         |      5 |
| 1000000001 | MI         |      5 |
+------------+------------+--------+
5 rows in set (0.037 sec)
```

Although subqueries are extremely useful in constructing this type of SELECT statement, care must be taken to properly qualify ambiguous column names.

> **Caution: Fully Qualified Column Names**
>
> You just saw a very important reason to use fully qualified column names, without the extra specificity the wrong results were returned because the DBMS misunderstood what you intended. Sometimes the **ambiguity** caused by the presence of conflicting column names will actually cause the DBMS to throw an error. For example, if you WHERE or ORDER BY clause specified a column name that was present in multiple tables. A good rule is that if you are ever working with more than one table in a SELECT statement, then use **fully qualified column names** to avoid any and all ambiguity.

> **Tip: Subqueries May Not Always Be the Best Option**
>
> As explained earlier in this lesson, although the sample code shown here works, it is often not the most efficient way to perform this type of data retrieval. You will revisit this example when you learn about JOINs in the next two lessons.

## Summary

In this lesson, you learned what subqueries are and how to use them. The most common uses for subqueries are in WHERE clause IN operators and for populating calculated columns. You saw examples of both of these types of operations.







#### `in`[¶](https://docs.djangoproject.com/en/dev/ref/models/querysets/#in)

In a given iterable; often a list, tuple, or queryset. It’s not a common use case, but strings (being iterables) are accepted.

Examples:

```
Entry.objects.filter(id__in=[1, 3, 4])
Entry.objects.filter(headline__in='abc')
```

SQL equivalents:

```
SELECT ... WHERE id IN (1, 3, 4);
SELECT ... WHERE headline IN ('a', 'b', 'c');
```

You can also use a queryset to dynamically evaluate the list of values instead of providing a list of literal values:

```
inner_qs = Blog.objects.filter(name__contains='Cheddar')
entries = Entry.objects.filter(blog__in=inner_qs)
```

This queryset will be evaluated as subselect statement:

```
SELECT ... WHERE blog.id IN (SELECT id FROM ... WHERE NAME LIKE '%Cheddar%')
```

If you pass in a `QuerySet` resulting from `values()` or `values_list()` as the value to an `__in` lookup, you need to ensure you are only extracting one field in the result. For example, this will work (filtering on the blog names):

```
inner_qs = Blog.objects.filter(name__contains='Ch').values('name')
entries = Entry.objects.filter(blog__name__in=inner_qs)
```

This example will raise an exception, since the inner query is trying to extract two field values, where only one is expected:

```
# Bad code! Will raise a TypeError.
inner_qs = Blog.objects.filter(name__contains='Ch').values('name', 'id')
entries = Entry.objects.filter(blog__name__in=inner_qs)
```

Performance considerations

Be cautious about using nested queries and understand your database server’s performance characteristics (if in doubt, benchmark!). Some database backends, most notably MySQL, don’t optimize nested queries very well. It is more efficient, in those cases, to extract a list of values and then pass that into the second query. That is, execute two queries instead of one:

```
values = Blog.objects.filter(
        name__contains='Cheddar').values_list('pk', flat=True)
entries = Entry.objects.filter(blog__in=list(values))
```

Note the `list()` call around the Blog `QuerySet` to force execution of the first query. Without it, a nested query would be executed, because [QuerySets are lazy](https://docs.djangoproject.com/en/dev/topics/db/queries/#querysets-are-lazy).



