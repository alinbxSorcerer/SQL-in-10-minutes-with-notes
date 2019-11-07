# Lesson 3. Sorting Retrieved Data

In this lesson, you will learn how to use the SELECT statement’s ORDER BY clause to sort retrieved data as needed.

## Sorting Data

As you learned in the last lesson, the following SQL statement returns a single column from a database table. But look at the output. The data appears to be displayed in no particular order at all.

```python
MySQL [distributor]> select prod_name from products
    -> ;
+---------------------+
| prod_name           |
+---------------------+
| Fish bean bag toy   |
| Bird bean bag toy   |
| Rabbit bean bag toy |
| 8 inch teddy bear   |
| 12 inch teddy bear  |
| 18 inch teddy bear  |
| Raggedy Ann         |
| King doll           |
| Queen doll          |
+---------------------+
9 rows in set (0.037 sec)
```

Actually, the retrieved data is not displayed in a mere random order. If unsorted, data will typically be displayed in the order in which it appears in the underlying tables. This could be the order in which the data was added to the tables initially. However, if data was subsequently updated or deleted, the order will be affected by how the DBMS reuses reclaimed storage space. The end result is that you cannot (and should not) rely on the sort order if you do not explicitly control it. Relational database design theory states that the sequence of retrieved data cannot be assumed to have significance if ordering was not explicitly specified.

> **Clause**
>
> SQL statements are made up of clauses, some required and some optional. A clause usually consists of a keyword and supplied data. An example of this is the SELECT statement’s FROM clause, which you saw in the last lesson.

```python
MySQL [distributor]> select prod_name from products order by prod_name;
+---------------------+
| prod_name           |
+---------------------+
| 12 inch teddy bear  |
| 18 inch teddy bear  |
| 8 inch teddy bear   |
| Bird bean bag toy   |
| Fish bean bag toy   |
| King doll           |
| Queen doll          |
| Rabbit bean bag toy |
| Raggedy Ann         |
+---------------------+
9 rows in set (0.023 sec)
```

This statement is identical to the earlier statement, except it also specifies an ORDER BY clause instructing the DBMS software to sort the data by the prod_name column. The results are as follows:

> **Caution: Position of ORDER BY Clause**
>
> When specifying an ORDER BY clause, be sure that it is the last clause in your SELECT statement. If it is not the last clause, an error will be generated.

> **Tip: Sorting by Nonselected Columns** 
>
> Although more often than not the columns used in an ORDER BY clause will be ones selected for display, this is actually not required. It is perfectly legal to sort data by a column that is not retrieved.

## Sorting by Multiple Columns

It is often necessary to sort data by more than one column. For example, if you are displaying an employee list, you might want to display it sorted by last name and first name (first by last name, and then within each last name sort by first name). This would be useful if there are multiple employees with the same last name.

To sort by multiple columns, simply specify the column names separated by commas (just as you do when you are selecting multiple columns).

The following code retrieves three columns and sorts the results by two of them—first by price and then by name.

```python
MySQL [distributor]> select prod_id, prod_price, prod_name
    -> from products
    -> order by prod_price, prod_name;
+---------+------------+---------------------+
| prod_id | prod_price | prod_name           |
+---------+------------+---------------------+
| BNBG02  |       3.49 | Bird bean bag toy   |
| BNBG01  |       3.49 | Fish bean bag toy   |
| BNBG03  |       3.49 | Rabbit bean bag toy |
| RGAN01  |       4.99 | Raggedy Ann         |
| BR01    |       5.99 | 8 inch teddy bear   |
| BR02    |       8.99 | 12 inch teddy bear  |
| RYL01   |       9.49 | King doll           |
| RYL02   |       9.49 | Queen doll          |
| BR03    |      11.99 | 18 inch teddy bear  |
+---------+------------+---------------------+
9 rows in set (0.020 sec)
```

It is important to understand that when you are sorting by multiple columns, the sort sequence is exactly as specified. In other words, using the output in the example above, the products are sorted by the prod_name column only when multiple rows have the same prod_price value. If all the values in the prod_price column had been unique, no data would have been sorted by prod_name.



## Sorting by Column Position

In addition to being able to specify sort order using column names, ORDER BY also supports ordering specified by relative column position. The best way to understand this is to look at an example:

```python
MySQL [distributor]> select prod_id, prod_price, prod_name from products order by 2, 3;
+---------+------------+---------------------+
| prod_id | prod_price | prod_name           |
+---------+------------+---------------------+
| BNBG02  |       3.49 | Bird bean bag toy   |
| BNBG01  |       3.49 | Fish bean bag toy   |
| BNBG03  |       3.49 | Rabbit bean bag toy |
| RGAN01  |       4.99 | Raggedy Ann         |
| BR01    |       5.99 | 8 inch teddy bear   |
| BR02    |       8.99 | 12 inch teddy bear  |
| RYL01   |       9.49 | King doll           |
| RYL02   |       9.49 | Queen doll          |
| BR03    |      11.99 | 18 inch teddy bear  |
+---------+------------+---------------------+
9 rows in set (0.000 sec)
```

As you can see, the output is identical to that of the query above. The difference here is in the ORDER BY clause. Instead of specifying column names, the relative positions of selected columns in the SELECT list are specified. ORDER BY 2 means sort by the second column in the SELECT list, the prod_price column. ORDER BY 2, 3 means sort by prod_price and then by prod_name.

The primary advantage of this technique is that it saves retyping the column names. But there are some downsides too. First, not explicitly listing column names increases the likelihood of you mistakenly specifying the wrong column. Second, it is all too easy to mistakenly reorder data when making changes to the SELECT list (forgetting to make the corresponding changes to the ORDER BY clause). And finally, obviously you cannot use this technique when sorting by columns that are not in the SELECT list.

> **Tip: Sorting by Nonselected Columns** 
>
> Obviously, this technique cannot be used when sorting by columns that do not appear in the SELECT list. However, you can mix and match actual column names and relative column positions in a single statement if needed.



## Specifying Sort Direction

Data sorting is not limited to ascending sort orders (from A to Z). Although this is the default sort order, the ORDER BY clause can also be used to sort in descending order (from Z to A). To sort by descending order, the keyword DESC must be specified.

The following example sorts the products by price in descending order (most expensive first):

```python
MySQL [distributor]> select prod_id, prod_price, prod_name
    -> from products
    -> order by prod_price desc;
+---------+------------+---------------------+
| prod_id | prod_price | prod_name           |
+---------+------------+---------------------+
| BR03    |      11.99 | 18 inch teddy bear  |
| RYL01   |       9.49 | King doll           |
| RYL02   |       9.49 | Queen doll          |
| BR02    |       8.99 | 12 inch teddy bear  |
| BR01    |       5.99 | 8 inch teddy bear   |
| RGAN01  |       4.99 | Raggedy Ann         |
| BNBG01  |       3.49 | Fish bean bag toy   |
| BNBG02  |       3.49 | Bird bean bag toy   |
| BNBG03  |       3.49 | Rabbit bean bag toy |
+---------+------------+---------------------+
9 rows in set (0.060 sec)
```

The DESC keyword only applies to the column name that directly precedes it. In the example above, DESC was specified for the prod_price column, but not for the prod_name column. Therefore, the prod_price column is sorted in descending order, but the prod_name column (within each price) is still sorted in standard ascending order.

> **Caution: Sorting Descending on Multiple Columns** 
>
> If you want to sort descending on multiple columns, be sure each column has its own DESC keyword.

It is worth noting that DESC is short for DESCENDING, and both keywords may be used. The opposite of DESC is ASC (or ASCENDING), which may be specified to sort in ascending order. In practice, however, ASC is not usually used because ascending order is the default sequence (and is assumed if neither ASC nor DESC are specified).

> **Tip: Case-Sensitivity and Sort Orders**
>
> When you are sorting textual data, is A the same as a? And does a come before B or after Z? These are not theoretical questions, and the answers depend on how the database is set up.
>
> In dictionary sort order, A is treated the same as a, and that is the default behavior for most Database Management Systems. However, most good DBMSs enable database administrators to change this behavior if needed. (If your database contains lots of foreign language characters, this might become necessary.)
>
> The key here is that, if you do need an alternate sort order, you may not be able to accomplish this with a simple ORDER BY clause. You may need to contact your database administrator.



## Summary

In this lesson, you learned how to sort retrieved data using the SELECT statement’s ORDER BY clause. This clause, w**hich must be the last in the SELECT statement**, can be used to sort data on one or more columns as needed.











#### `order_by()`[¶](https://docs.djangoproject.com/en/2.1/ref/models/querysets/#order-by)

- `order_by`(**fields*)[¶](https://docs.djangoproject.com/en/2.1/ref/models/querysets/#django.db.models.query.QuerySet.order_by)


By default, results returned by a `QuerySet` are ordered by the ordering tuple given by the `ordering` option in the model’s `Meta`. You can override this on a per-`QuerySet` basis by using the `order_by` method.

Example:

```
Entry.objects.filter(pub_date__year=2005).order_by('-pub_date', 'headline')
```

The result above will be ordered by `pub_date` descending, then by `headline` ascending. The negative sign in front of `"-pub_date"` indicates *descending* order. Ascending order is implied. To order randomly, use `"?"`, like so:

```
Entry.objects.order_by('?')
```

Note: `order_by('?')` queries may be expensive and slow, depending on the database backend you’re using.

To order by a field in a different model, use the same syntax as when you are querying across model relations. That is, the name of the field, followed by a double underscore (`__`), followed by the name of the field in the new model, and so on for as many models as you want to join. For example:

```
Entry.objects.order_by('blog__name', 'headline')
```

If you try to order by a field that is a relation to another model, Django will use the default ordering on the related model, or order by the related model’s primary key if there is no [`Meta.ordering`](https://docs.djangoproject.com/en/2.1/ref/models/options/#django.db.models.Options.ordering) specified. For example, since the `Blog` model has no default ordering specified:

```
Entry.objects.order_by('blog')
```

…is identical to:

```
Entry.objects.order_by('blog__id')
```

If `Blog` had `ordering = ['name']`, then the first queryset would be identical to:

```
Entry.objects.order_by('blog__name')
```

You can also order by [query expressions](https://docs.djangoproject.com/en/2.1/ref/models/expressions/) by calling `asc()` or `desc()` on the expression:

```
Entry.objects.order_by(Coalesce('summary', 'headline').desc())
```

Be cautious when ordering by fields in related models if you are also using [`distinct()`](https://docs.djangoproject.com/en/2.1/ref/models/querysets/#django.db.models.query.QuerySet.distinct). See the note in [`distinct()`](https://docs.djangoproject.com/en/2.1/ref/models/querysets/#django.db.models.query.QuerySet.distinct) for an explanation of how related model ordering can change the expected results.

Note

It is permissible to specify a multi-valued field to order the results by (for example, a [`ManyToManyField`](https://docs.djangoproject.com/en/2.1/ref/models/fields/#django.db.models.ManyToManyField) field, or the reverse relation of a [`ForeignKey`](https://docs.djangoproject.com/en/2.1/ref/models/fields/#django.db.models.ForeignKey) field).

Consider this case:

```
class Event(Model):
   parent = models.ForeignKey(
       'self',
       on_delete=models.CASCADE,
       related_name='children',
   )
   date = models.DateField()

Event.objects.order_by('children__date')
```

Here, there could potentially be multiple ordering data for each `Event`; each `Event` with multiple `children` will be returned multiple times into the new `QuerySet` that `order_by()` creates. In other words, using `order_by()` on the `QuerySet` could return more items than you were working on to begin with - which is probably neither expected nor useful.

Thus, take care when using multi-valued field to order the results. **If** you can be sure that there will only be one ordering piece of data for each of the items you’re ordering, this approach should not present problems. If not, make sure the results are what you expect.

There’s no way to specify whether ordering should be case sensitive. With respect to case-sensitivity, Django will order results however your database backend normally orders them.

You can order by a field converted to lowercase with [`Lower`](https://docs.djangoproject.com/en/2.1/ref/models/database-functions/#django.db.models.functions.Lower) which will achieve case-consistent ordering:

```
Entry.objects.order_by(Lower('headline').desc())
```

If you don’t want any ordering to be applied to a query, not even the default ordering, call [`order_by()`](https://docs.djangoproject.com/en/2.1/ref/models/querysets/#django.db.models.query.QuerySet.order_by) with no parameters.

You can tell if a query is ordered or not by checking the [`QuerySet.ordered`](https://docs.djangoproject.com/en/2.1/ref/models/querysets/#django.db.models.query.QuerySet.ordered) attribute, which will be `True` if the `QuerySet` has been ordered in any way.

Each `order_by()` call will clear any previous ordering. For example, this query will be ordered by `pub_date` and not `headline`:

```
Entry.objects.order_by('headline').order_by('pub_date')
```

Warning

Ordering is not a free operation. Each field you add to the ordering incurs a cost to your database. Each foreign key you add will implicitly include all of its default orderings as well.

If a query doesn’t have an ordering specified, results are returned from the database in an unspecified order. A particular ordering is guaranteed only when ordering by a set of fields that uniquely identify each object in the results. For example, if a `name` field isn’t unique, ordering by it won’t guarantee objects with the same name always appear in the same order.