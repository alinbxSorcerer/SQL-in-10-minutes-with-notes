# Lesson 10. Grouping Data

In this lesson, you’ll learn how to group data so that you can summarize subsets of table contents. This involves two new SELECT statement clauses: the GROUP BY clause and the HAVING clause.

## Understanding Data Grouping

In the last lesson, you learned that the SQL aggregate functions can be used to summarize data. This enables you to count rows, calculate sums and averages, and obtain high and low values without having to retrieve all the data.

All the calculations thus far were performed on all the data in a table or on data that matched a **specific** WHERE clause. As a reminder, the following example returns the number of products offered by vendor DLL01:

```python
MySQL [distributor]> select count(*) as num_prods
    -> from products
    -> where vend_id = "dll01";
+-----------+
| num_prods |
+-----------+
|         4 |
+-----------+
1 row in set (0.090 sec)
```

But what if you wanted to return the number of products offered by each vendor? Or products offered by vendors who offer a single product, or only those who offer more than ten products?

This is where groups come into play. Grouping lets you divide data into logical sets so that you can perform aggregate calculations on each group.



## Creating Groups

Groups are created using the GROUP BY clause in your SELECT statement. The best way to understand this is to look at an example:

```python
MySQL [distributor]> select vend_id, count(*) as num_prods
    -> from products
    -> group by vend_id;
+---------+-----------+
| vend_id | num_prods |
+---------+-----------+
| BRS01   |         3 |
| DLL01   |         4 |
| FNG01   |         2 |
+---------+-----------+
3 rows in set (0.069 sec)
```

The above SELECT statement specifies two columns, vend_id, which contains the ID of a product’s vendor, and num_prods, which is a calculated field (created using the COUNT(*) function). The GROUP BY clause instructs the DBMS to sort the data and group it by vend_id. This causes num_prods to be calculated once per vend_id rather than once for the entire table. As you can see in the output, vendor BRS01 has 3 products listed, vendor DLL01 has 4 products listed, and vendor FNG01 has 2 products listed.

Because you used GROUP BY, you did not have to **specify** each group to be evaluated and calculated. That was done automatically. The GROUP BY clause instructs the DBMS to group the data and then perform the aggregate on each group rather than on the entire result set.

Before you use GROUP BY, here are some important rules about its use that you need to know:

- GROUP BY clauses can contain as many columns as you want. This enables you to nest groups, providing you with more granular control over how data is grouped.
- If you have nested groups in your GROUP BY clause, data is summarized at the last specified group. In other words, all the columns specified are evaluated together when grouping is established (so you won’t get data back for each individual column level).
- Every column listed in GROUP BY must be a retrieved column or a valid expression (but not an aggregate function). If an expression is used in the SELECT, that same expression must be specified in GROUP BY. Aliases cannot be used.
- Most SQL implementations do not allow GROUP BY columns with variable length datatypes (such as text or memo fields).
- Aside from the aggregate calculations statements, every column in your SELECT statement must be present in the GROUP BY clause
-  If the grouping column contains a row with a NULL value, NULL will be returned as a group. If there are multiple rows with NULL values, they’ll all be grouped together.
- The GROUP BY clause must come after any WHERE clause and before any ORDER BY clause.

> **Caution: Specifying Columns by Relative Position**
>
> Some SQL implementations allow you to specify GROUP BY columns by the position in the SELECT list. For example, GROUP BY 2,1 can mean group by the second column selected and then by the first. Although this shorthand syntax is convenient, it is not supported by all SQL implementations. It’s use is also risky in that it is highly susceptible to the introduction of errors when editing SQL statements.



## Filtering Groups

In addition to being able to group data using GROUP BY, SQL also allows you to filter which groups to include and which to exclude. For example, you might want a list of all customers who have made at least two orders. To obtain this data you must filter based on the complete group, not on individual rows.

You’ve already seen the WHERE clause in action (that was introduced back in Lesson 4, “Filtering Data.”) But WHERE does not work here because **WHERE filters specific rows**, not groups. As a matter of fact, WHERE has no idea what a group is.

So what do you use instead of WHERE? SQL provides yet another clause for this purpose: the HAVING clause. HAVING is very similar to WHERE. In fact, all types of WHERE clauses you learned about thus far can also be used with HAVING. The only difference is that WHERE filters rows and HAVING filters groups.

> **Tip: HAVING Supports All WHERE’s Operators**
>
>  In Lesson 4 and Lesson 5, “Advanced Data Filtering,” you learned about WHERE clause conditions (including wildcard conditions and clauses with multiple operators). All the techniques and options that you learned about WHERE can be applied to HAVING. The syntax is identical; just the keyword is different.

So how do you filter groups? Look at the following example:

```python
MySQL [distributor]> select cust_id, count(*) as orders
    -> from orders
    -> group by cust_id
    -> having count(*) >=2 ;
+------------+--------+
| cust_id    | orders |
+------------+--------+
| 1000000001 |      2 |
+------------+--------+
1 row in set (0.030 sec)
```

The first three lines of this SELECT statement are similar to the statements seen above. The final line adds a HAVING clause that filters on those groups with a COUNT(*) >= 2—two or more orders.

As you can see, a WHERE clause does not work here because the filtering is based on the group aggregate value, not on the values of **specific rows.**

> **Note: The Difference Between HAVING and WHERE**
>
>  Here’s another way to look it: WHERE filters before data is grouped, and HAVING filters after data is grouped. This is an important distinction; rows that are eliminated by a WHERE clause will not be included in the group. This could change the calculated values which in turn could affect which groups are filtered based on the use of those values in the HAVING clause.

So is there ever a need to use both WHERE and HAVING clauses in one statement? Actually, yes, there is. Suppose you want to further filter the above statement so that it returns any customers who placed two or more orders in the past 12 months. To do that, you can add a WHERE clause that filters out just the orders placed in the past 12 months. You then add a HAVING clause to filter just the groups with two or more rows in them.

To better demonstrate this, look at the following example which lists all vendors who have two or more products priced at 4 or more:

```python
MySQL [distributor]> select vend_id, count(*) as num_prods
    -> from products
    -> where prod_price >= 4
    -> group by vend_id
    -> having count(*) >= 2;
+---------+-----------+
| vend_id | num_prods |
+---------+-----------+
| BRS01   |         3 |
| FNG01   |         2 |
+---------+-----------+
2 rows in set (0.029 sec)
```

This statement warrants an explanation. The first line is a basic SELECT using an aggregate function—much like the examples thus far. The WHERE clause filters all rows with a prod_price of at least 4. Data is then grouped by vend_id, and then a HAVING clause filters just those groups with a count of 2 or more. Without the WHERE clause an extra row would have been retrieved (vendor DLL01 who sells four products all priced under 4) as seen here:

```python
MySQL [distributor]> select vend_id, count(*) as num_prods from products group by vend_id having count(*) >=2 ;
+---------+-----------+
| vend_id | num_prods |
+---------+-----------+
| BRS01   |         3 |
| DLL01   |         4 |
| FNG01   |         2 |
+---------+-----------+
3 rows in set (0.010 sec)
```

> **Note: Using HAVING and WHERE**
>
> HAVING is so similar to WHERE that most DBMSs treat them as the same thing if no GROUP BY is specified. Nevertheless, you should make that distinction yourself. Use HAVING only in conjunction with GROUP BY clauses. Use WHERE for standard row-level filtering.



## Grouping and Sorting

It is important to understand that GROUP BY and ORDER BY are very different, even though they often accomplish the same thing. Table 10.1 summarizes the differences between them.

![Screen Shot 2018-08-12 at 3.07.00 PM](http://heropublic.oss-cn-beijing.aliyuncs.com/070716.png)

The first difference listed in Table 10.1 is extremely important. More often than not, you will find that data grouped using GROUP BY will indeed be output in group order. **But that is not always the case,** and it is not actually required by the SQL specifications. Furthermore, even if your particular DBMS does, in fact, always sort the data by the specified GROUP BY clause, you might actually want it sorted differently. Just because you group data one way (to obtain group specific aggregate values) does not mean that you want the output sorted that same way. You should always provide an explicit ORDER BY clause as well, even if it is identical to the GROUP BY clause.

> **Tip: Don’t Forget ORDER BY** 
>
> As a rule, anytime you use a GROUP BY clause, you should also specify an ORDER BY clause. That is the only way to ensure that data will be sorted properly. Never rely on GROUP BY to sort your data.

To demonstrate the use of both GROUP BY and ORDER BY, let’s look at an example. The following SELECT statement is similar to the ones seen **previously.** It retrieves the order number and number of items ordered for all orders containing three or more items:

```python
MySQL [distributor]> select order_num, count(*) as items
    -> from orderitems
    -> group by order_num
    -> having count(*) >= 3;
+-----------+-------+
| order_num | items |
+-----------+-------+
|     20006 |     3 |
|     20007 |     5 |
|     20008 |     5 |
|     20009 |     3 |
+-----------+-------+
4 rows in set (0.082 sec)
```

```python
MySQL [distributor]> select order_num, count(*) as items from orderitems group by order_num having count(*) >= 3 order by items, order_num;
+-----------+-------+
| order_num | items |
+-----------+-------+
|     20006 |     3 |
|     20009 |     3 |
|     20007 |     5 |
|     20008 |     5 |
+-----------+-------+
4 rows in set (0.029 sec)
```

In this example, the GROUP BY clause is used to group the data by order number (the order_num column) so that the COUNT(*) function can return the number of items in each order. The HAVING clause filters the data so that only orders with three or more items are returned. Finally, the output is sorted using the ORDER BY clause.

## Select  Clause Ordering

This is probably a good time to review the order in which SELECT statement clauses are to be specified. Table 10.2 lists all the clauses we have learned thus far, in the order they must be used.

![Screen Shot 2018-08-12 at 3.15.04 PM](http://heropublic.oss-cn-beijing.aliyuncs.com/071606.png)



## Summary

In Lesson 9, “Summarizing Data,” you learned how to use the SQL aggregate functions to perform summary calculations on your data. In this lesson, you learned how to use the GROUP BY clause to perform these calculations on groups of data, returning results for each group. You saw how to use the HAVING clause to filter specific groups. You also learned the difference between ORDER BY and GROUP BY and between WHERE and HAVING.

