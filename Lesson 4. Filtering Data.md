# Lesson 4. Filtering Data

In this lesson, you will learn how to use the SELECT statement’s WHERE clause to specify search conditions.

## Using the WHERE Clause

Database tables usually contain large amounts of data, and you seldom need to retrieve all the rows in a table. More often than not you’ll want to extract a subset of the table’s data as needed for specific operations or reports. Retrieving just the data you want involves specifying search criteria, also known as a filter condition.

Within a SELECT statement, data is **filtered** by specifying search criteria in the WHERE clause. The WHERE clause is specified right after the table name (the FROM clause) as follows:

```python
MySQL [distributor]> select prod_name, prod_price
    -> from products
    -> where prod_price = 3.49;
#This statement retrieves two columns from the products table, but instead of returning #all rows, only rows with a prod_price value of 3.49 are returned, as follows:
+---------------------+------------+
| prod_name           | prod_price |
+---------------------+------------+
| Fish bean bag toy   |       3.49 |
| Bird bean bag toy   |       3.49 |
| Rabbit bean bag toy |       3.49 |
+---------------------+------------+
3 rows in set (0.061 sec)
```

This example uses a simple equality test: It checks to see if a column has a specified value, and it filters the data accordingly. But SQL lets you do more than just test for equality.

> **Tip: How Many Zeros?**
>
> As you try the examples in this lesson you may see results displayed as 3.49, 3.490, 3.4900, and so on. This behavior tends to be somewhat DBMS specific as it is tied to the data types used and their default behavior. So, if your output is a little different from mine, don’t sweat it, after all, 3.49 and 3.4900 are mathematically identical anyway.

> **Tip: SQL Versus Application Filtering**
>
> **Data can also be filtered at the application level.** To do this, the SQL SELECT statement retrieves more data than is actually required for the client application, and the client code loops through the returned data to **extract** just the needed rows.
>
> As a rule, this practice is strongly discouraged. **Databases are optimized to perform filtering quickly and efficiently.** Making the client application (or development language) do the database’s job will dramatically **impact** application performance and will create applications that cannot scale properly. In addition, if data is filtered at the client, the server has to send unneeded data across the network connections, resulting in a waste of network bandwidth usage.

> **Caution: WHERE Clause Position**
>
> When using both ORDER BY and WHERE clauses, make sure that ORDER BY comes after the WHERE, otherwise an error will be generated. (See Lesson 3, “Sorting Retrieved Data,” for more information on using ORDER BY.)



## The WHERE Clause Operators

The first WHERE clause we looked at tests for equality—determining if a column contains a specific value. SQL supports a whole range of conditional operators as listed in Table 4.1.

![Screen Shot 2018-08-11 at 10.03.13 PM](http://heropublic.oss-cn-beijing.aliyuncs.com/140327.png)

> **Caution: Operator Compatibility** 
>
> Some of the operators listed in Table 4.1 are redundant (for example, <> is the same as !=. !< [not less than] accomplishes the same effect as >= [greater than or equal to]. Not all of these operators are supported by all DBMSs. Refer to your DBMS documentation to determine exactly what it supports.



### Checking Against a Single Value

We have already seen an example of testing for equality. Let’s take a look at a few examples to demonstrate the use of other operators.

This first example lists all products that cost less than $10:

```python
MySQL [distributor]> select prod_name, prod_price
    -> from products
    -> where prod_price < 10;
+---------------------+------------+
| prod_name           | prod_price |
+---------------------+------------+
| Fish bean bag toy   |       3.49 |
| Bird bean bag toy   |       3.49 |
| Rabbit bean bag toy |       3.49 |
| 8 inch teddy bear   |       5.99 |
| 12 inch teddy bear  |       8.99 |
| Raggedy Ann         |       4.99 |
| King doll           |       9.49 |
| Queen doll          |       9.49 |
+---------------------+------------+
8 rows in set (0.063 sec)
```

This next statement retrieves all products costing `$10` or less (although the result will be the same as in the previous example because there are no items with a price of exactly `$10`):

```python
MySQL [distributor]> select prod_name, prod_price from products where prod_price <=10 ;
+---------------------+------------+
| prod_name           | prod_price |
+---------------------+------------+
| Fish bean bag toy   |       3.49 |
| Bird bean bag toy   |       3.49 |
| Rabbit bean bag toy |       3.49 |
| 8 inch teddy bear   |       5.99 |
| 12 inch teddy bear  |       8.99 |
| Raggedy Ann         |       4.99 |
| King doll           |       9.49 |
| Queen doll          |       9.49 |
+---------------------+------------+
8 rows in set (0.002 sec)
```



### Checking for Nonmatches 

This next example lists all products not made by vendor DLL01:

```python
MySQL [distributor]> select vend_id, prod_name from products where vend_id != "DLL01";
+---------+--------------------+
| vend_id | prod_name          |
+---------+--------------------+
| BRS01   | 8 inch teddy bear  |
| BRS01   | 12 inch teddy bear |
| BRS01   | 18 inch teddy bear |
| FNG01   | King doll          |
| FNG01   | Queen doll         |
+---------+--------------------+
5 rows in set (0.026 sec)
```

> **Tip: When to Use Quotes** 
>
> If you look closely at the conditions used in the above WHERE clauses, you will notice that some values are enclosed within single quotes, and others are not. The single quotes are used to delimit a string. If you are comparing a value against a column that is **a string datatype**, the delimiting quotes are required. Quotes are not used to delimit values used with numeric columns.

> **Caution: != Or <>?** 
>
> != and <> can usually be used interchangeably. However, not all DBMSs support both forms of the non-equality operator. Microsoft Access, for example, supports <> but does not support !=. If in doubt, consult your DBMS documentation.



### Checking for a Range of Values

To check for a range of values, you can use the BETWEEN operator. Its syntax is a little different from other WHERE clause operators because it requires two values: the beginning and end of the range. The BETWEEN operator can be used, for example, to check for all products that cost between $5 and $10 or for all dates that fall between specified start and end dates.

The following example demonstrates the use of the BETWEEN operator by retrieving all products with a price between `$5` and `$10`:

```python
MySQL [distributor]> select prod_name, prod_price from products where prod_price between 3.49 and 11.99;
+---------------------+------------+
| prod_name           | prod_price |
+---------------------+------------+
| Fish bean bag toy   |       3.49 |
| Bird bean bag toy   |       3.49 |
| Rabbit bean bag toy |       3.49 |
| 8 inch teddy bear   |       5.99 |
| 12 inch teddy bear  |       8.99 |
| 18 inch teddy bear  |      11.99 |
| Raggedy Ann         |       4.99 |
| King doll           |       9.49 |
| Queen doll          |       9.49 |
+---------------------+------------+
9 rows in set (0.005 sec)

```

As seen in this example, when BETWEEN is used, two values must be specified—the low end and high end of the desired range. The two values must also be separated by the AND keyword. BETWEEN matches all the values in the range, including the specified start and end values.



### Checking for No Value

When a table is created, the table designer can specify whether or not individual columns can contain no value. When a column contains no value, it is said to contain a NULL value.

> **NULL** 
>
> No value, as opposed to a field containing 0, or an empty string, or just spaces.

To determine if a value is NULL, you cannot simply check to see if = NULL. Instead, the SELECT statement has a special WHERE clause that can be used to check for columns with NULL values—the IS NULL clause. The syntax looks like this:

```python
MySQL [distributor]> select prod_name
    -> from products
    -> where prod_price is null;
Empty set (0.013 sec)
```

This statement returns a list of all products that have no price (an empty prod_price field, not a price of 0), and because there are none, no data is returned. The Customers table, however, does contain columns with NULL values —the cust_email column will contain NULL if a customer has no e-mail address on file:

```python
MySQL [distributor]> select cust_name
    -> from customers
    -> where cust_email is null;
+---------------+
| cust_name     |
+---------------+
| Kids Place    |
| The Toy Store |
+---------------+
2 rows in set (0.049 sec)
```



> **Tip: DBMS Specific Operators**
>
> Many DBMSs extend the standard set of operators, providing advanced filtering options. Refer to your DBMS documentation for more information.

> **Caution: NULL and Non-matches**
>
> You might expect that when you filter to select all rows that do not have a particular value, rows with a NULL will be returned. But they will not. Because of the special meaning of unknown, the database does not know whether or not they match, and so they are not returned when filtering for matches or when filtering for non-matches.
>
> When filtering data, make sure to verify that the rows with a NULL in the filtered column are really present in the returned data.







#### `exclude()`[¶](https://docs.djangoproject.com/en/2.1/ref/models/querysets/#exclude)

- `exclude`(**\*kwargs*)[¶](https://docs.djangoproject.com/en/2.1/ref/models/querysets/#django.db.models.query.QuerySet.exclude)


Returns a new `QuerySet` containing objects that do *not* match the given lookup parameters.

The lookup parameters (`**kwargs`) should be in the format described in [Field lookups](https://docs.djangoproject.com/en/2.1/ref/models/querysets/#id4) below. Multiple parameters are joined via `AND`in the underlying SQL statement, and the whole thing is enclosed in a `NOT()`.

This example excludes all entries whose `pub_date` is later than 2005-1-3 AND whose `headline` is “Hello”:

```
Entry.objects.exclude(pub_date__gt=datetime.date(2005, 1, 3), headline='Hello')
```

In SQL terms, that evaluates to:

```
SELECT ...
WHERE NOT (pub_date > '2005-1-3' AND headline = 'Hello')
```

This example excludes all entries whose `pub_date` is later than 2005-1-3 OR whose headline is “Hello”:

```
Entry.objects.exclude(pub_date__gt=datetime.date(2005, 1, 3)).exclude(headline='Hello')
```

In SQL terms, that evaluates to:

```
SELECT ...
WHERE NOT pub_date > '2005-1-3'
AND NOT headline = 'Hello'
```

Note the second example is more restrictive.

If you need to execute more complex queries (for example, queries with `OR` statements), you can use [`Q objects`](https://docs.djangoproject.com/en/2.1/ref/models/querysets/#django.db.models.Q).



## Summary

In this lesson, you learned how to filter returned data using the SELECT statement’s WHERE clause. You learned how to test for equality, nonequality, greater than and less than, value ranges, as well as for NULL values.







#### `range`[¶](https://docs.djangoproject.com/en/2.1/ref/models/querysets/#range)

Range test (inclusive).

Example:

```
import datetime
start_date = datetime.date(2005, 1, 1)
end_date = datetime.date(2005, 3, 31)
Entry.objects.filter(pub_date__range=(start_date, end_date))
```

SQL equivalent:

```
SELECT ... WHERE pub_date BETWEEN '2005-01-01' and '2005-03-31';
```

You can use `range` anywhere you can use `BETWEEN` in SQL — for dates, numbers and even characters.

Warning

Filtering a `DateTimeField` with dates won’t include items on the last day, because the bounds are interpreted as “0am on the given date”. If `pub_date` was a `DateTimeField`, the above expression would be turned into this SQL:

```
SELECT ... WHERE pub_date BETWEEN '2005-01-01 00:00:00' and '2005-03-31 00:00:00';
```

Generally speaking, you can’t mix dates and datetimes.

#### `gt`[¶](https://docs.djangoproject.com/en/2.1/ref/models/querysets/#gt)

Greater than.

Example:

```
Entry.objects.filter(id__gt=4)
```

SQL equivalent:

```
SELECT ... WHERE id > 4;
```

#### `gte`[¶](https://docs.djangoproject.com/en/2.1/ref/models/querysets/#gte)

Greater than or equal to.

#### `lt`[¶](https://docs.djangoproject.com/en/2.1/ref/models/querysets/#lt)

Less than.

#### `lte`[¶](https://docs.djangoproject.com/en/2.1/ref/models/querysets/#lte)

Less than or equal to.

#### `startswith`[¶](https://docs.djangoproject.com/en/2.1/ref/models/querysets/#startswith)

Case-sensitive starts-with.

Example:

```
Entry.objects.filter(headline__startswith='Lennon')
```

SQL equivalent:

```
SELECT ... WHERE headline LIKE 'Lennon%';
```

SQLite doesn’t support case-sensitive `LIKE` statements; `startswith` acts like `istartswith` for SQLite.