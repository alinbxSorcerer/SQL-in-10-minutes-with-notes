# Lesson 6. Using Wildcard Filtering



In this lesson, you’ll learn what wildcards are, how they are used, and how to perform wildcard searches using the LIKE operator for sophisticated filtering of retrieved data.



## Using Wildcard Filtering

All the previous operators we studied filter against **known values**. Be it matching one or more values, testing for greater-than or less-than known values, or checking a range of values, the common **denominator** is that the values used in the filtering are known.

But filtering data that way does not always work. For example, how could you search for all products that contained the text bean bag within the product name? That cannot be done with simple comparison operators; that’s a job for wildcard searching. Using wildcards, you can create **search patterns** that can be compared against your data. In this example, if you want to find all products that contain the words bean bag, you can construct a wildcard search pattern enabling you to find that bean bag text anywhere within a product name.

> **Wildcards**
>
> Special characters used to match parts of a value.

> **Search pattern**
>
> A search condition made up of literal text, wildcard characters, or any combination of the above.

The wildcards themselves are actually characters that have special meanings within SQL WHERE clauses, and SQL supports several different wildcard types.

To use wildcards in search clauses, the LIKE operator must be used. LIKE instructs the DBMS that the following search pattern is to be compared using a wildcard match rather than a straight equality match.

> **Predicates**
>
> When is an operator not an operator? When it is a “predicate.” Technically, LIKE is a predicate, not an operator. The end result is the same, just be aware of this term in case you run across it in SQL documentation or manuals.

Wildcard searching can only be used with text fields (strings), you can’t use wildcards to search fields of non-text datatypes.



### The Percent Sign (%) Wildcard

The most frequently used wildcard is the percent sign (%). Within a search string, % means, match any number of occurrences of any character. For example, to find all products that started with the word Fish, you can issue the following SELECT statement:

```python
MySQL [distributor]> select prod_id, prod_name from products where prod_name like "fish%";
+---------+-------------------+
| prod_id | prod_name         |
+---------+-------------------+
| BNBG01  | Fish bean bag toy |
+---------+-------------------+
1 row in set (0.052 sec)
```

This example uses a search pattern of 'Fish%'. When this clause is evaluated, any value that starts with Fish will be retrieved. The % tells the DBMS to accept any characters after the word Fish, regardless of how many characters there are.

Wildcards can be used anywhere within the search pattern, and multiple wildcards may be used as well. The following example uses two wildcards, one at either end of the pattern:

```python
MySQL [distributor]> select prod_id, prod_name 
    -> from products
    -> where prod_name like "%bean bag%";
+---------+---------------------+
| prod_id | prod_name           |
+---------+---------------------+
| BNBG01  | Fish bean bag toy   |
| BNBG02  | Bird bean bag toy   |
| BNBG03  | Rabbit bean bag toy |
+---------+---------------------+
3 rows in set (0.001 sec)
```

The search pattern '%bean bag%' means match any value that contains the text bean bag anywhere within it, regardless of any characters before or after that text.

Wildcards can also be used in the middle of search pattern, although that is rarely useful. The following example finds all products that begin with an F and end with a y.

```python
MySQL [distributor]> select prod_name from products where prod_name like "f%y";
+-------------------+
| prod_name         |
+-------------------+
| Fish bean bag toy |
+-------------------+
1 row in set (0.003 sec)
```

> **Tip: Searching for Partial Email Addresses**
>
> There is one situation in which wildcards may indeed be useful in the middle of a search pattern, and that is looking for e-mail addresses based on a partial address, such as WHERE email LIKE b%@forta.com.

It is important to note that, In addition to matching one or more characters, % also matches zero characters. % represents zero, one, or more characters at the specified location in the search pattern.

> **Note: Watch for Trailing Spaces**
>
> Many DBMSs, including Microsoft Access, pad field contents with spaces. For example, if a column expects 50 characters and the text stored is Fish bean bag toy (17 characters), 33 spaces may be appended to the text so as to fully fill the column. This usually has no real impact on data and how it is used, but it could negatively affect the just used SQL statement. The clause WHERE prod_name LIKE 'F%y' will only match prod_name if it starts with F and ends with y, and if the value is padded with spaces then it will not end with y and so Fish bean bag toy will not be retrieved. One simple solution to this problem is to append a second % to the search pattern, 'F%y%' will also match characters (or spaces) after the y. A better solution would be to **trim** the spaces using functions, as will be discussed in Lesson 8, “Using Data Manipulation Functions.”

> **Caution: Watch for NULL**
>
> it may seem that the % wildcard matches anything, there is one exception, NULL. Not even the clause WHERE prod_name LIKE '%' will match a row with the value NULL as the product name.



### **The Underscore (_) Wildcard**

Another useful wildcard is the underscore (_). The underscore is used just like %, but instead of matching multiple characters the underscore matches just a single character.

```python
MySQL [distributor]> select prod_name, prod_price
    -> from products
    -> where prod_name like "__ inch teddy bear";
+--------------------+------------+
| prod_name          | prod_price |
+--------------------+------------+
| 12 inch teddy bear |       8.99 |
| 18 inch teddy bear |      11.99 |
+--------------------+------------+
2 rows in set (0.013 sec)
```

The search pattern used in this WHERE clause specified two wildcards followed by literal text. The results shown are the only rows that match the search pattern: The underscore matches 12 in the first row and 18 in the second row. The 8 inch teddy bear product did not match because the search pattern required two wildcard matches, not one. By contrast, the following SELECT statement uses the % wildcard and returns three matching products:

```python
MySQL [distributor]> select prod_id, prod_name
    -> from products
    -> where prod_name like "% inch teddy bear";
+---------+--------------------+
| prod_id | prod_name          |
+---------+--------------------+
| BR01    | 8 inch teddy bear  |
| BR02    | 12 inch teddy bear |
| BR03    | 18 inch teddy bear |
+---------+--------------------+
3 rows in set (0.000 sec)
```

Unlike % which can match 0 characters, _ always matches 1 character—no more and no less.



### The Brackets ([]) Wildcard

The brackets ([]) wildcard is used to specify a set of characters, any one of which must match a character in the specified position (the location of the wildcard).

> **Note: Sets Are Not Commonly Supported** 
>
> **Unlike the wildcards describes thus far, the use of [] to create sets is not supported by all DBMSs.** Sets are supported by Microsoft Access and Microsoft SQL Server. Consult your DBMS documentation to determine if sets are supported.

For example, to find all contacts whose names begin with the letter J or the letter M, you can do the following:

```python
MySQL [distributor]> select cust_contact from customers where cust_contact rlike "[JM].*" order by 
    -> cust_contact;
+----------------+
| cust_contact   |
+----------------+
| Jim Jones      |
| John Smith     |
| Kim Howard     |
| Michelle Green |
+----------------+
4 rows in set (0.014 sec)
```

> 我找到了一个从wildcards入手,切入到regex的给Alina讲解的方法.

The WHERE clause in this statement is '[JM]%'. This search pattern uses two different wildcards. The [JM] matches any contact name that begins with either of the letters within the brackets, and it also matches only a single character. Therefore, any names longer than one character will not match. The % wildcard after the [JM] matches any number of characters after the first character, returning the desired results.

This wildcard can be negated by prefixing the characters with ^ (the carat character). For example, the following matches any contact name that does not begin with the letter J or the letter M (the opposite of the previous example):

```python
MySQL [distributor]> select cust_contact from customers where cust_contact rlike "[^JM].*" order by cust_contact;
+--------------------+
| cust_contact       |
+--------------------+
| Denise L. Stephens |
| Jim Jones          |
| John Smith         |
| Kim Howard         |
| Michelle Green     |
+--------------------+
5 rows in set (0.063 secs)
```

> **Note:**
>
>  Negating Sets in Microsoft Access If you are using Microsoft Access, then you may need to use ! instead of ^ to negate a set, so [!JM] instead of [^JM].

Of course, you can accomplish the same result using the NOT operator. The only advantage of ^ is that it can simplify the syntax if you are using multiple WHERE clauses:



## Tips for Using Wildcards

As you can see, SQL’s wildcards are extremely powerful. But that power comes with a price: Wildcard searches typically take far longer to process than any other search types discussed previously. Here are some rules to keep in mind when using wildcards:

- Don’t overuse wildcards. If another search operator will do, use it instead.
- When you do use wildcards, try to not use them at the beginning of the search pattern unless absolutely necessary. Search patterns that begin with wildcards are the slowest to process.
- Pay careful attention to the placement of the wildcard symbols. If they are misplaced, you might not return the data you intended.

Having said that, wildcards are an important and useful search tool, and one that you will use frequently.



## Summary

In this lesson, you learned what wildcards are and how to use SQL wildcards within your WHERE clauses. You also learned that wildcards should be used carefully and never overused.





