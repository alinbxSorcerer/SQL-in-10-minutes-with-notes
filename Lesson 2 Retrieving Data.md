# Lesson 2 Retrieving Data

In this lesson, you’ll learn how to use the SELECT statement to retrieve one or more columns of data from a table.

## The Select Stateement

As explained in Lesson 1, “Understanding SQL,” SQL statements are made up of plain English terms. These terms are called keywords, and every SQL statement is made up of one or more keywords. The SQL statement that you’ll probably use most frequently is the SELECT statement. Its purpose is to retrieve information from one or more tables.

>  **Keyword**
>
> A reserved word that is part of the SQL language. Never name a table or column using a keyword. Appendix E, “SQL Reserved Words,”

To use SELECT to retrieve table data you must, at a minimum, specify two pieces of information—what you want to select, and from where you want to select it.

> **Note: Following Along with the Examples**
>
> The sample SQL statements (and sample output) throughout the lessons in this book use a set of data files that are described in Appendix A, “Sample Table Scripts.” If you’d like to follow along and try the examples yourself (I strongly recommend that you do so), refer to Appendix A which contains instructions on how to download or create these data files.
>
> It is important to understand that SQL is a language, not an application. The way that you specify SQL statements and display statement output varies from one application to the next. To assist you in adapting the examples to your own environment, Appendix B, “Working in Popular Applications,” explains how to issue the statements taught throughout this book using many popular applications and development environments. And if you need an application with which to follow along, Appendix B has recommendations for you too.

## Retrieving Individual Column

We’ll start with a simple SQL SELECT statement, as follows:

- Input

  ```python
  select prod_name from products;
  ```

- Analysis

  The previous statement uses the SELECT statement to retrieve a single column called prod_name from the Products table. The desired column name is specified right after the SELECT keyword, and the FROM keyword specifies the name of the table from which to retrieve the data. The output from this statement is shown in the following:

- Output

  ```python
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
  9 rows in set (0.009 sec)
  ```

  > **Note: Unsorted Data**
  >
  > If you tried this query yourself you might have discovered that the data was displayed in a different order than shown here. If this is the case, don’t worry—it is working exactly as it is supposed to. If query results are not explicitly sorted (we’ll get to that in the next lesson) then data will be returned in no order of any significance. It may be the order in which the data was added to the table, but it may not. As long as your query returned the same number of rows then it is working.

  > 这么一点简单的功能,似乎用ORM不能实现呀.

A simple SELECT statement similar to the one used above returns all the rows in a table. Data is not filtered (so as to retrieve a subset of the results), nor is it sorted. We’ll discuss these topics in the next few lessons.

> **Tip: Terminating Statements**
>
> Multiple SQL statements must be separated by semicolons (the ; character). Most DBMSs do not require that a semicolon be specified after single statements. But if your particular DBMS **complains**, you might have to add it there. Of course, you can always add a semicolon if you wish. It’ll do no harm, even if it is, in fact, not needed.

> **Tip: SQL Statement and Case**
>
> It is important to note that SQL statements are **case-insensitive,** so SELECT is the same as select, which is the same as Select. Many SQL developers find that using uppercase for all SQL keywords and lowercase for column and table names **makes code easier to read and debug.** However, be aware that while the SQL language is case- insensitive, the names of tables, columns, and values may not be (that depends on your DBMS and how it is configured).

> **Tip: Use of White Space**
>
> All extra white space within a SQL statement is ignored when that statement is processed. SQL statements can be specified on one long line or broken up over many lines. So, the following three statements are functionality identical:
>
> ```sql
> SELECT prod_name 
> FROM Products;
> 
> SELECT prod_name FROM Products;
> 
> SELECT 
> prod_name 
> FROM 
> Products;
> ```
>
> Most SQL developers find that breaking up statements over multiple lines makes them easier to read and debug.



## Retrieving Multiple Columns

To retrieve multiple columns from a table, the same SELECT statement is used. The only difference is that multiple column names must be specified after the SELECT keyword, and each column must be separated by a comma.

> **Tip: Take Care with Commas** 
>
> When selecting multiple columns be sure to specify a comma between each column name, but not **after the last column name**. Doing so will generate an error.

The following SELECT statement retrieves three columns from the products table:

```python
MySQL [distributor]> select prod_id, prod_name, prod_price from products;
+---------+---------------------+------------+
| prod_id | prod_name           | prod_price |
+---------+---------------------+------------+
| BNBG01  | Fish bean bag toy   |       3.49 |
| BNBG02  | Bird bean bag toy   |       3.49 |
| BNBG03  | Rabbit bean bag toy |       3.49 |
| BR01    | 8 inch teddy bear   |       5.99 |
| BR02    | 12 inch teddy bear  |       8.99 |
| BR03    | 18 inch teddy bear  |      11.99 |
| RGAN01  | Raggedy Ann         |       4.99 |
| RYL01   | King doll           |       9.49 |
| RYL02   | Queen doll          |       9.49 |
+---------+---------------------+------------+
9 rows in set (0.040 sec)

```



> **Note: Presentation of Data**
>
>  As you will notice in the above output, SQL statements typically return raw, unformatted data. Data formatting is a presentation issue, not a retrieval issue. Therefore, presentation (for example, displaying the above price values as currency amounts with the correct number of decimal places) is typically specified in the application that displays the data. Actual retrieved data (without application-provided formatting) is rarely used.



## Retrieving All Columns

In addition to being able to specify desired columns (one or more, as seen above), SELECT statements can also request all columns without having to list them individually. This is done using the asterisk (*) wildcard character in lieu of actual column names, as follows:

![Screen Shot 2018-08-11 at 7.30.45 PM](http://heropublic.oss-cn-beijing.aliyuncs.com/124541.jpg)

> **Caution: Using Wildcards**
>
> As a rule, you are better off not using the * wildcard unless you really do need every column in the table. Even though use of wildcards may save you the time and effort needed to list the desired columns explicitly, retrieving unnecessary columns usually slows down the performance of your retrieval and your application.

> **Tip: Retrieving Unknown Columns**
>
> There is one big advantage to using wildcards. As you do not explicitly specify column names (because the asterisk retrieves every column), it is possible to retrieve columns whose names are unknown.



## Retrieving Distinct Column

As you have seen, SELECT returns all matched rows. But what if you do not want every occurrence of every value? For example, suppose you want the vendor ID of all vendors with products in your products table:

```python
MySQL [distributor]> select vend_id from products;
+---------+
| vend_id |
+---------+
| BRS01   |
| BRS01   |
| BRS01   |
| DLL01   |
| DLL01   |
| DLL01   |
| DLL01   |
| FNG01   |
| FNG01   |
+---------+
9 rows in set (0.054 sec)
```

The SELECT statement returned 14 rows (even though there are only four vendors in that list) because there are 14 products listed in the products table. So how could you retrieve a list of distinct values?

The solution is to use the DISTINCT keyword which, as its name implies, instructs the database to only return distinct values.

```python
MySQL [distributor]> select distinct vend_id from products;
+---------+
| vend_id |
+---------+
| BRS01   |
| DLL01   |
| FNG01   |
+---------+
3 rows in set (0.076 sec)
```

SELECT DISTINCT vend_id tells the DBMS to only return distinct (unique) vend_id rows, and so only four rows are returned, as seen in the following output. If used, the DISTINCT keyword must be placed directly in front of the column names.

> **Caution: Can’t Be Partially DISTINCT**
>
> The DISTINCT keyword applies to all columns, not just the one it precedes. If you were to specify SELECT DISTINCT vend_id, prod_price, all rows would be retrieved unless both of the specified columns were distinct.



## Limiting Results

SELECT statements return all matched rows, possibly every row in the specified table. What if you want to return just the first row or a set number of rows? This is doable, **but unfortunately, this is one of those situations where all SQL implementations are not created equal.**

If you are using MySQL, MariaDB, PostgreSQL, or SQLite, you can use the LIMIT clause, as follows:

```python
MySQL [distributor]> select prod_name from products limit 5;
+---------------------+
| prod_name           |
+---------------------+
| Fish bean bag toy   |
| Bird bean bag toy   |
| Rabbit bean bag toy |
| 8 inch teddy bear   |
| 12 inch teddy bear  |
+---------------------+
5 rows in set (0.011 sec)
```

The previous statement uses the SELECT statement to retrieve a single column. LIMIT 5 instructs the supported DBMSs to return no more than five rows. The output from this statement is shown in the following code.

To get the next five rows, specify both where to start and the number of rows to retrieve, like this:

```python

MySQL [distributor]> select prod_name from products limit 5 offset 5;
+--------------------+
| prod_name          |
+--------------------+
| 18 inch teddy bear |
| Raggedy Ann        |
| King doll          |
| Queen doll         |
+--------------------+
4 rows in set (0.011 sec)
```

LIMIT 5 OFFSET 5 instructs supported DBMSs to return five rows starting from row 5. The first number is where to start, and the second is the number of rows to retrieve. The output from this statement is shown in the following code:

So, LIMIT specifies the number of rows to return. LIMIT with an OFFSET specifies where to start from. In our example there are only nine products in the Products table, so LIMIT 5 OFFSET 5 returned just four rows (as there was no fifth).

> **Caution: Row 0**
>
> The first row retrieved is row 0, not row 1. As such, LIMIT 1 OFFSET 1 will retrieve the second row, not the first one.

>  **Tip: MySQL and MariaDB Shortcut**
>
> MySQL and MariaDB support a shorthand version of LIMIT 4 OFFSET 3, enabling you to combine them as LIMIT 3,4. Using this syntax, the value before the , is the LIMIT and the value after the , is the OFFSET.

> **Note: Not ALL SQL Is Created Equal** 
>
> I included this section on limiting results for one reason only, to demonstrate that while SQL is usually quite consistent across implementations, you can’t rely on it always being so. While very basic statements tend to be very portable, more complex ones tend to be less so. Keep that in mind as you search for SQL solutions to specific problems.



## Using Comments



As you have seen, SQL statements are instructions that are processed by your DBMS. But what if you wanted to include text that you’d not want processed and executed? Why would you ever want to do this? Here are a few reasons:

- The SQL statements we’ve been using here are all very short and very simple. But, as your SQL statement grow (in length and complexity), you’ll want to include descriptive comments (for your own future reference or for whoever has to work on the project next). These comments need to be embedded in the SQL scripts, but they are obviously not intended for actual DBMS processing. (For an example of this, see the create.sql and populate.sql files used in Appendix B).
-  The same is true for headers at the top of SQL file, perhaps containing the programmer contact information and a description and notes. (This use case is also seen in the Appendix B .sql files.).
- Another important use for comments is to temporarily stop SQL code from being executed. If you were working with a long SQL statement, and wanted to test just part of it, you could comment out some of the code so that MariaDB saw it as comments and ignored it.

Most DBMSs supports several forms of comment syntax. We’ll Start with inline comments:

```python
SELECT prod_name -- this is a comment FROM Products;
```

Comments may be embedded inline using -- (two hyphens). Anything after the -- is considered comment text, making this a good option for describing columns in a CREATE TABLE statement, for example.

Here is another form of inline comment (although less commonly supported):

```python
# This is a comment 
SELECT prod_name 
FROM Products;
```

A `#` at the start of a line makes the entire line a comment. You can see this format comment used in the accompanying create.sql and populate.sql scripts.

You can also create **multi line** comments, and comments that stop and start anywhere within the script:

```python
/* SELECT prod_name, 
vend_id FROM Products; */ 
SELECT prod_name FROM Products;
```

`/*` starts a comments, and `*/` ends it. Anything between `/*` and `*/` is comment text. This type of comment is often used to comment out code, as seen in this example. Here, two SELECT statements are defined, but the first won’t execute because it has been commented out.



## Summaries

In this lesson, you learned how to use the **SQL SELECT** statement to retrieve a single table column, multiple table columns, and all table columns. You also learned how to return distinct values and how to comment your code. And unfortunately, you were also introduced to the fact that more complex SQL tends to be less portable SQL. Next you’ll learn how to sort the retrieved data.





Django查找的是精确的条目

**[django models selecting single field](https://stackoverflow.com/questions/7503241/django-models-selecting-single-field)**

    Employees.objects.values_list('eng_name', flat=True)

That creates a flat list of all `eng_name`s. If you want more than one field per row, you can't do a flat list: this will create a list of tuples:

    Employees.objects.values_list('eng_name', 'rank')

----

In addition to `values_list` as [Daniel](https://stackoverflow.com/users/104349/daniel-roseman) [mentions](https://stackoverflow.com/a/7503368/1523238) you can also use [`only`](https://docs.djangoproject.com/en/1.10/ref/models/querysets/#only) (or [`defer`](https://docs.djangoproject.com/en/1.10/ref/models/querysets/#django.db.models.query.QuerySet.defer) for the opposite effect) to get a queryset of objects only having their id and specified fields:

    Employees.objects.only('eng_name')

This will run a single query:

    SELECT id, eng_name FROM employees



#### `defer()`[¶](https://docs.djangoproject.com/en/2.0/ref/models/querysets/#defer)

- `defer`(**fields*)[¶](https://docs.djangoproject.com/en/2.0/ref/models/querysets/#django.db.models.query.QuerySet.defer)


In some complex data-modeling situations, your models might contain a lot of fields, some of which could contain a lot of data (for example, text fields), or require expensive processing to convert them to Python objects. If you are using the results of a queryset in some situation where you don’t know if you need those particular fields when you initially fetch the data, you can tell Django not to retrieve them from the database.

This is done by passing the names of the fields to not load to `defer()`:

```
Entry.objects.defer("headline", "body")
```

A queryset that has deferred fields will still return model instances. Each deferred field will be retrieved from the database if you access that field (one at a time, not all the deferred fields at once).

You can make multiple calls to `defer()`. Each call adds new fields to the deferred set:

```
# Defers both the body and headline fields.
Entry.objects.defer("body").filter(rating=5).defer("headline")
```

The order in which fields are added to the deferred set does not matter. Calling `defer()` with a field name that has already been deferred is harmless (the field will still be deferred).

You can defer loading of fields in related models (if the related models are loading via [`select_related()`](https://docs.djangoproject.com/en/2.0/ref/models/querysets/#django.db.models.query.QuerySet.select_related)) by using the standard double-underscore notation to separate related fields:

```
Blog.objects.select_related().defer("entry__headline", "entry__body")
```

If you want to clear the set of deferred fields, pass `None` as a parameter to `defer()`:

```
# Load all fields immediately.
my_queryset.defer(None)
```

Some fields in a model won’t be deferred, even if you ask for them. You can never defer the loading of the primary key. If you are using [`select_related()`](https://docs.djangoproject.com/en/2.0/ref/models/querysets/#django.db.models.query.QuerySet.select_related) to retrieve related models, you shouldn’t defer the loading of the field that connects from the primary model to the related one, doing so will result in an error.

> **Note**
>
> The `defer()` method (and its cousin, [`only()`](https://docs.djangoproject.com/en/2.0/ref/models/querysets/#django.db.models.query.QuerySet.only), below) are only for advanced use-cases. They provide an optimization for when you have analyzed your queries closely and understand *exactly*what information you need and have measured that the difference between returning the fields you need and the full set of fields for the model will be significant.
>
> Even if you think you are in the advanced use-case situation, **only use defer() when you cannot, at queryset load time, determine if you will need the extra fields or not**. If you are frequently loading and using a particular subset of your data, the best choice you can make is to normalize your models and put the non-loaded data into a separate model (and database table). If the columns *must* stay in the one table for some reason, create a model with `Meta.managed = False` (see the [`managed attribute`](https://docs.djangoproject.com/en/2.0/ref/models/options/#django.db.models.Options.managed) documentation) containing just the fields you normally need to load and use that where you might otherwise call `defer()`. This makes your code more explicit to the reader, is slightly faster and consumes a little less memory in the Python process.
>
> For example, both of these models use the same underlying database table:
>
> ```python
> 
> class CommonlyUsedModel(models.Model):
>     f1 = models.CharField(max_length=10)
> 
>     class Meta:
>         managed = False
>         db_table = 'app_largetable'
> 
> class ManagedModel(models.Model):
>     f1 = models.CharField(max_length=10)
>     f2 = models.CharField(max_length=10)
> 
>     class Meta:
>         db_table = 'app_largetable'
> 
> # Two equivalent QuerySets:
> CommonlyUsedModel.objects.all()
> ManagedModel.objects.all().defer('f2')
> ```
>
> If many fields need to be duplicated in the unmanaged model, it may be best to create an abstract model with the shared fields and then have the unmanaged and managed models inherit from the abstract model.

> **Note**
>
> When calling [`save()`](https://docs.djangoproject.com/en/2.0/ref/models/instances/#django.db.models.Model.save) for instances with deferred fields, only the loaded fields will be saved. See[`save()`](https://docs.djangoproject.com/en/2.0/ref/models/instances/#django.db.models.Model.save) for more details.





#### `only()`[¶](https://docs.djangoproject.com/en/2.0/ref/models/querysets/#only)

- `only`(**fields*)[¶](https://docs.djangoproject.com/en/2.0/ref/models/querysets/#django.db.models.query.QuerySet.only)

The `only()` method is more or less the opposite of [`defer()`](https://docs.djangoproject.com/en/2.0/ref/models/querysets/#django.db.models.query.QuerySet.defer). You call it with the fields that should *not* be deferred when retrieving a model. If you have a model where almost all the fields need to be deferred, using`only()` to specify the complementary set of fields can result in simpler code.

Suppose you have a model with fields `name`, `age` and `biography`. The following two querysets are the same, in terms of deferred fields:

```python
Person.objects.defer("age", "biography")
Person.objects.only("name")
```

Whenever you call `only()` it *replaces* the set of fields to load immediately. The method’s name is mnemonic: **only** those fields are loaded immediately; the remainder are deferred. Thus, successive calls to `only()` result in only the final fields being considered: 

```python
# This will defer all fields except the headline.
Entry.objects.only("body", "rating").only("headline")
```

Since `defer()` acts incrementally (adding fields to the deferred list), you can combine calls to `only()` and `defer()` and things will behave logically:

```python
# Final result is that everything except "headline" is deferred.
Entry.objects.only("headline", "body").defer("body")

# Final result loads headline and body immediately (only() replaces any
# existing set of fields).
Entry.objects.defer("body").only("headline", "body")
```

All of the cautions in the note for the [`defer()`](https://docs.djangoproject.com/en/2.0/ref/models/querysets/#django.db.models.query.QuerySet.defer) documentation apply to `only()` as well. Use it cautiously and only after exhausting your other options.

Using [`only()`](https://docs.djangoproject.com/en/2.0/ref/models/querysets/#django.db.models.query.QuerySet.only) and omitting a field requested using [`select_related()`](https://docs.djangoproject.com/en/2.0/ref/models/querysets/#django.db.models.query.QuerySet.select_related) is an error as well.

> **Note**
>
> When calling [`save()`](https://docs.djangoproject.com/en/2.0/ref/models/instances/#django.db.models.Model.save) for instances with deferred fields, only the loaded fields will be saved. See[`save()`](https://docs.djangoproject.com/en/2.0/ref/models/instances/#django.db.models.Model.save) for more details.





#### `values()`[¶](https://docs.djangoproject.com/en/2.0/ref/models/querysets/#values)

- `values`(**fields*, **\*expressions*)[¶](https://docs.djangoproject.com/en/2.0/ref/models/querysets/#django.db.models.query.QuerySet.values)


Returns a `QuerySet` that returns dictionaries, rather than model instances, when used as an iterable.

Each of those dictionaries represents an object, with the keys corresponding to the attribute names of model objects.

This example compares the dictionaries of `values()` with the normal model objects:

```
# This list contains a Blog object.
>>> Blog.objects.filter(name__startswith='Beatles')
<QuerySet [<Blog: Beatles Blog>]>

# This list contains a dictionary.
>>> Blog.objects.filter(name__startswith='Beatles').values()
<QuerySet [{'id': 1, 'name': 'Beatles Blog', 'tagline': 'All the latest Beatles news.'}]>
```

The `values()` method takes optional positional arguments, `*fields`, which specify field names to which the `SELECT` should be limited. If you specify the fields, each dictionary will contain only the field keys/values for the fields you specify. If you don’t specify the fields, each dictionary will contain a key and value for every field in the database table.

Example:

```
>>> Blog.objects.values()
<QuerySet [{'id': 1, 'name': 'Beatles Blog', 'tagline': 'All the latest Beatles news.'}]>
>>> Blog.objects.values('id', 'name')
<QuerySet [{'id': 1, 'name': 'Beatles Blog'}]>
```

The `values()` method also takes optional keyword arguments, `**expressions`, which are passed through to [`annotate()`](https://docs.djangoproject.com/en/2.0/ref/models/querysets/#django.db.models.query.QuerySet.annotate):

```
>>> from django.db.models.functions import Lower
>>> Blog.objects.values(lower_name=Lower('name'))
<QuerySet [{'lower_name': 'beatles blog'}]>
```

An aggregate within a `values()` clause is applied before other arguments within the same `values()` clause. If you need to group by another value, add it to an earlier `values()` clause instead. For example:

```
>>> from django.db.models import Count
>>> Blog.objects.values('entry__authors', entries=Count('entry'))
<QuerySet [{'entry__authors': 1, 'entries': 20}, {'entry__authors': 1, 'entries': 13}]>
>>> Blog.objects.values('entry__authors').annotate(entries=Count('entry'))
<QuerySet [{'entry__authors': 1, 'entries': 33}]>
```

A few subtleties that are worth mentioning:

- If you have a field called `foo` that is a [`ForeignKey`](https://docs.djangoproject.com/en/2.0/ref/models/fields/#django.db.models.ForeignKey), the default `values()` call will return a dictionary key called `foo_id`, since this is the name of the hidden model attribute that stores the actual value (the `foo`attribute refers to the related model). When you are calling `values()` and passing in field names, you can pass in either `foo` or `foo_id` and you will get back the same thing (the dictionary key will match the field name you passed in).

  For example:

  ```
  >>> Entry.objects.values()
  <QuerySet [{'blog_id': 1, 'headline': 'First Entry', ...}, ...]>
  
  >>> Entry.objects.values('blog')
  <QuerySet [{'blog': 1}, ...]>
  
  >>> Entry.objects.values('blog_id')
  <QuerySet [{'blog_id': 1}, ...]>
  ```

- When using `values()` together with [`distinct()`](https://docs.djangoproject.com/en/2.0/ref/models/querysets/#django.db.models.query.QuerySet.distinct), be aware that ordering can affect the results. See the note in [`distinct()`](https://docs.djangoproject.com/en/2.0/ref/models/querysets/#django.db.models.query.QuerySet.distinct) for details.

- If you use a `values()` clause after an [`extra()`](https://docs.djangoproject.com/en/2.0/ref/models/querysets/#django.db.models.query.QuerySet.extra) call, any fields defined by a `select` argument in the [`extra()`](https://docs.djangoproject.com/en/2.0/ref/models/querysets/#django.db.models.query.QuerySet.extra) must be explicitly included in the `values()` call. Any [`extra()`](https://docs.djangoproject.com/en/2.0/ref/models/querysets/#django.db.models.query.QuerySet.extra) call made after a `values()` call will have its extra selected fields ignored.

- Calling [`only()`](https://docs.djangoproject.com/en/2.0/ref/models/querysets/#django.db.models.query.QuerySet.only) and [`defer()`](https://docs.djangoproject.com/en/2.0/ref/models/querysets/#django.db.models.query.QuerySet.defer) after `values()` doesn’t make sense, so doing so will raise a `NotImplementedError`.

It is useful when you know you’re only going to need values from a small number of the available fields and you won’t need the functionality of a model instance object. It’s more efficient to select only the fields you need to use.

Finally, note that you can call `filter()`, `order_by()`, etc. after the `values()` call, that means that these two calls are identical:

```
Blog.objects.values().order_by('id')
Blog.objects.order_by('id').values()
```

The people who made Django prefer to put all the SQL-affecting methods first, followed (optionally) by any output-affecting methods (such as `values()`), but it doesn’t really matter. This is your chance to really flaunt your individualism.

You can also refer to fields on related models with reverse relations through `OneToOneField`, `ForeignKey`and `ManyToManyField` attributes:

```
>>> Blog.objects.values('name', 'entry__headline')
<QuerySet [{'name': 'My blog', 'entry__headline': 'An entry'},
     {'name': 'My blog', 'entry__headline': 'Another entry'}, ...]>
```

Warning

Because [`ManyToManyField`](https://docs.djangoproject.com/en/2.0/ref/models/fields/#django.db.models.ManyToManyField) attributes and reverse relations can have multiple related rows, including these can have a multiplier effect on the size of your result set. This will be especially pronounced if you include multiple such fields in your `values()` query, in which case all possible combinations will be returned.

Changed in Django 1.11:

Support for `**expressions` was added.







#### `values_list()`[¶](https://docs.djangoproject.com/en/2.0/ref/models/querysets/#values-list)

- `values_list`(**fields*, *flat=False*, *named=False*)[¶](https://docs.djangoproject.com/en/2.0/ref/models/querysets/#django.db.models.query.QuerySet.values_list)


This is similar to `values()` except that instead of returning dictionaries, it returns tuples when iterated over. Each tuple contains the value from the respective field or expression passed into the `values_list()` call — so the first item is the first field, etc. For example:

```
>>> Entry.objects.values_list('id', 'headline')
<QuerySet [(1, 'First entry'), ...]>
>>> from django.db.models.functions import Lower
>>> Entry.objects.values_list('id', Lower('headline'))
<QuerySet [(1, 'first entry'), ...]>
```

If you only pass in a single field, you can also pass in the `flat` parameter. If `True`, this will mean the returned results are single values, rather than one-tuples. An example should make the difference clearer:

```
>>> Entry.objects.values_list('id').order_by('id')
<QuerySet[(1,), (2,), (3,), ...]>

>>> Entry.objects.values_list('id', flat=True).order_by('id')
<QuerySet [1, 2, 3, ...]>
```

It is an error to pass in `flat` when there is more than one field.

You can pass `named=True` to get results as a [`namedtuple()`](https://docs.python.org/3/library/collections.html#collections.namedtuple):

```
>>> Entry.objects.values_list('id', 'headline', named=True)
<QuerySet [Row(id=1, headline='First entry'), ...]>
```

Using a named tuple may make use of the results more readable, at the expense of a small performance penalty for transforming the results into a named tuple.

If you don’t pass any values to `values_list()`, it will return all the fields in the model, in the order they were declared.

A common need is to get a specific field value of a certain model instance. To achieve that, use `values_list()` followed by a `get()` call:

```
>>> Entry.objects.values_list('headline', flat=True).get(pk=1)
'First entry'
```

`values()` and `values_list()` are both intended as optimizations for a specific use case: retrieving a subset of data without the overhead of creating a model instance. This metaphor falls apart when dealing with many-to-many and other multivalued relations (such as the one-to-many relation of a reverse foreign key) because the “one row, one object” assumption doesn’t hold.

For example, notice the behavior when querying across a [`ManyToManyField`](https://docs.djangoproject.com/en/2.0/ref/models/fields/#django.db.models.ManyToManyField):

```
>>> Author.objects.values_list('name', 'entry__headline')
<QuerySet [('Noam Chomsky', 'Impressions of Gaza'),
 ('George Orwell', 'Why Socialists Do Not Believe in Fun'),
 ('George Orwell', 'In Defence of English Cooking'),
 ('Don Quixote', None)]>
```

Authors with multiple entries appear multiple times and authors without any entries have `None` for the entry headline.

Similarly, when querying a reverse foreign key, `None` appears for entries not having any author:

```
>>> Entry.objects.values_list('authors')
<QuerySet [('Noam Chomsky',), ('George Orwell',), (None,)]>
```

Changed in Django 1.11:

Support for expressions in `*fields` was added.

Changed in Django 2.0:

The `named` parameter was added.



**Distinct** /dɪˈstɪŋkt/ 

1. 助记

   dis- "apart" (see [dis-](https://www.etymonline.com/word/dis-?ref=etymonline_crossreference)) + 

   1560s, from Middle French distinguiss-, stem of distinguer, or directly from Latin distinguere "to separate between, keep separate, mark off, distinguish," perhaps literally "separate by pricking," from dis- "apart" (see [dis-](https://www.etymonline.com/word/dis-?ref=etymonline_crossreference)) + -stinguere "to prick" (compare [extinguish](https://www.etymonline.com/word/extinguish?ref=etymonline_crossreference) and Latin instinguere "to incite, impel").

2. 词源, 

   **Origin**

   Late Middle English (in the sense ‘differentiated’): from Latin distinctus ‘separated, distinguished’, from the verb distinguere (see distinguish).

3. 释义,

   clearly separate and different (from something else)

   as distinct from(rather  than)

   She's a [personal](https://dictionary.cambridge.org/zhs/%E8%AF%8D%E5%85%B8/%E8%8B%B1%E8%AF%AD-%E6%B1%89%E8%AF%AD-%E7%AE%80%E4%BD%93/personal) [assistant](https://dictionary.cambridge.org/zhs/%E8%AF%8D%E5%85%B8/%E8%8B%B1%E8%AF%AD-%E6%B1%89%E8%AF%AD-%E7%AE%80%E4%BD%93/assistant), as distinct from a [secretary](https://dictionary.cambridge.org/zhs/%E8%AF%8D%E5%85%B8/%E8%8B%B1%E8%AF%AD-%E6%B1%89%E8%AF%AD-%E7%AE%80%E4%BD%93/secretary). 







#### `distinct()`[¶](https://docs.djangoproject.com/en/2.1/ref/models/querysets/#distinct)

- `distinct`(**fields*)[¶](https://docs.djangoproject.com/en/2.1/ref/models/querysets/#django.db.models.query.QuerySet.distinct)


Returns a new `QuerySet` that uses `SELECT DISTINCT` in its SQL query. This eliminates duplicate rows from the query results.

By default, a `QuerySet` will not eliminate duplicate rows. In practice, this is rarely a problem, because simple queries such as `Blog.objects.all()` don’t introduce the possibility of duplicate result rows. However, if your query spans multiple tables, it’s possible to get duplicate results when a `QuerySet` is evaluated. That’s when you’d use `distinct()`.

Note

Any fields used in an [`order_by()`](https://docs.djangoproject.com/en/2.1/ref/models/querysets/#django.db.models.query.QuerySet.order_by) call are included in the SQL `SELECT` columns. This can sometimes lead to unexpected results when used in conjunction with `distinct()`. If you order by fields from a related model, those fields will be added to the selected columns and they may make otherwise duplicate rows appear to be distinct. Since the extra columns don’t appear in the returned results (they are only there to support ordering), it sometimes looks like non-distinct results are being returned.

Similarly, if you use a [`values()`](https://docs.djangoproject.com/en/2.1/ref/models/querysets/#django.db.models.query.QuerySet.values) query to restrict the columns selected, the columns used in any [`order_by()`](https://docs.djangoproject.com/en/2.1/ref/models/querysets/#django.db.models.query.QuerySet.order_by) (or default model ordering) will still be involved and may affect uniqueness of the results.

The moral here is that if you are using `distinct()` be careful about ordering by related models. Similarly, when using `distinct()` and [`values()`](https://docs.djangoproject.com/en/2.1/ref/models/querysets/#django.db.models.query.QuerySet.values) together, be careful when ordering by fields not in the [`values()`](https://docs.djangoproject.com/en/2.1/ref/models/querysets/#django.db.models.query.QuerySet.values) call.

On PostgreSQL only, you can pass positional arguments (`*fields`) in order to specify the names of fields to which the `DISTINCT` should apply. This translates to a `SELECT DISTINCT ON` SQL query. Here’s the difference. For a normal `distinct()` call, the database compares *each* field in each row when determining which rows are distinct. For a `distinct()` call with specified field names, the database will only compare the specified field names.

Note

When you specify field names, you *must* provide an `order_by()` in the `QuerySet`, and the fields in `order_by()` must start with the fields in `distinct()`, in the same order.

For example, `SELECT DISTINCT ON (a)` gives you the first row for each value in column `a`. If you don’t specify an order, you’ll get some arbitrary row.

Examples (those after the first will only work on PostgreSQL):

```
>>> Author.objects.distinct()
[...]

>>> Entry.objects.order_by('pub_date').distinct('pub_date')
[...]

>>> Entry.objects.order_by('blog').distinct('blog')
[...]

>>> Entry.objects.order_by('author', 'pub_date').distinct('author', 'pub_date')
[...]

>>> Entry.objects.order_by('blog__name', 'mod_date').distinct('blog__name', 'mod_date')
[...]

>>> Entry.objects.order_by('author', 'pub_date').distinct('author')
[...]
```

Note

Keep in mind that [`order_by()`](https://docs.djangoproject.com/en/2.1/ref/models/querysets/#django.db.models.query.QuerySet.order_by) uses any default related model ordering that has been defined. You might have to explicitly order by the relation `_id` or referenced field to make sure the `DISTINCT ON` expressions match those at the beginning of the `ORDER BY` clause. For example, if the `Blog` model defined an [`ordering`](https://docs.djangoproject.com/en/2.1/ref/models/options/#django.db.models.Options.ordering) by `name`:

```
Entry.objects.order_by('blog').distinct('blog')
```

…wouldn’t work because the query would be ordered by `blog__name` thus mismatching the `DISTINCT ON` expression. You’d have to explicitly order by the relation _id field (`blog_id` in this case) or the referenced one (`blog__pk`) to make sure both expressions match.


