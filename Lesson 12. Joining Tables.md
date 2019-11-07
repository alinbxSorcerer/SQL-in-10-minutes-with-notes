# Lesson 12. Joining Tables

In this lesson, you’ll learn what joins are, why they are used, and how to create SELECT statements using them.

## Understanding Joins

One of SQL’s most powerful features is the capability to join tables on-the-fly within data retrieval queries. Joins are one of the most important operations that you can perform using SQL SELECT, and a good understanding of joins and join syntax is an extremely important part of learning SQL.

Before you can effectively use joins, you must understand relational tables and the basics of relational database design. What follows is **by no means** complete coverage of the subject, but it should be enough to g**et you up and running.**

## Understanding Relational Tables

The best way to understand relational tables is to look at a real-world example.

Suppose you had a database table containing a product catalog, with each catalog item in its own row. The kind of information you would store with each item would include a product description and price, along with vendor information about the company that creates the product.

Now suppose that you had multiple catalog items created by the same vendor. Where would you store the vendor information (things like vendor name, address, and contact information)? You wouldn’t want to store that data along with the products for several reasons:

- Because the vendor information is the same for each product that vendor produces, repeating the information for each product is a waste of time and storage space.
- If vendor information changes (for example, if the vendor moves or his area code changes), you would need to update every occurrence of the vendor information.
- When data is repeated, (that is, the vendor information is used with each product), there is a high likelihood that the data will not be entered exactly the same way each time. Inconsistent data is extremely difficult to use in reporting.

The key here is that having multiple occurrences of the same data is never a good thing, and that principle is **the basis for relational database design**. Relational tables are designed so that information is split into multiple tables, one for each data type. The tables are related to each other through common values (and thus the relational in relational design).

In our example, you can create two tables, one for vendor information and one for product information. The Vendors table contains all the vendor information, one table row per vendor, along with a unique identifier for each vendor. This value, called a primary key, can be a vendor ID, or any other unique value.

The Products table stores only product information, and no vendor specific information other than the vendor ID (the Vendors table’s primary key). **This key relates** the Vendors table to the Products table, and using this vendor ID enables you to use the Vendors table to find the details about the appropriate vendor.

What does this do for you? Well, consider the following:

- Vendor information is never repeated, and so time and space are not wasted.
- If vendor information changes, you can update a single record, the one in the Vendors table. Data in related tables does not change.
- As no data is repeated, the data used is obviously consistent, making data reporting and manipulation much simpler.

The bottom line is that relational data can be stored efficiently and manipulated easily. Because of this, **relational databases scale far better** than non-relational databases.

> **Scale**
>
> Able to handle an increasing load without failing. A well-designed database or application is said to scale well.

### Why Use Join

As just explained, **breaking data into multiple** tables enables 

- more efficient storage, 

- easier manipulation, 

- and greater scalability. 

  But these benefits come with a price.

If data is stored in multiple tables, how can you retrieve that data with a single SELECT statement?

The answer is to use a join. Simply put, a join is a mechanism used to associate tables within a SELECT statement (and thus the name join). Using a special syntax, multiple tables can be joined so that a single set of output is returned, and the join associates the correct rows in each table on-the-fly.

> **Note: Using Interactive DBMS Tools**
>
> Understand that a join is not a **physical entity**—in other words, it does not exist in the actual database tables. A join is created by the DBMS as needed, and it **persists** for the duration of the query execution.
>
> Many DBMSs provide graphical interfaces that can be used to define table relationships interactively. These tools can be **invaluable** in helping to maintain referential integrity. When using relational tables, it is important that only valid data is inserted into relational columns. Going back to the example, if an invalid vendor ID is stored in the Products table, those products would be inaccessible because they would not be related to any vendor. To prevent this from occurring, the database can be instructed to only allow valid values (ones present in the Vendors table) in the vendor ID column in the Products table. Referential integrity means that the DBMS enforces data integrity rules. And these rules are often managed through DBMS provided interfaces.



## Creating a Join

Creating a join is very simple. You must specify all the tables to be included and how they are related to each other. Look at the following example:

```python

MySQL [distributor]> select vend_name, prod_name, prod_price
    -> from vendors, products
    -> where vendors.vend_id = products.vend_id;
+-----------------+---------------------+------------+
| vend_name       | prod_name           | prod_price |
+-----------------+---------------------+------------+
| Doll House Inc. | Fish bean bag toy   |       3.49 |
| Doll House Inc. | Bird bean bag toy   |       3.49 |
| Doll House Inc. | Rabbit bean bag toy |       3.49 |
| Bears R Us      | 8 inch teddy bear   |       5.99 |
| Bears R Us      | 12 inch teddy bear  |       8.99 |
| Bears R Us      | 18 inch teddy bear  |      11.99 |
| Doll House Inc. | Raggedy Ann         |       4.99 |
| Fun and Games   | King doll           |       9.49 |
| Fun and Games   | Queen doll          |       9.49 |
+-----------------+---------------------+------------+
9 rows in set (0.263 sec)
```

Let’s take a look **at the preceding code.** The SELECT statement starts in the same way as all the statements you’ve looked at thus far, by specifying the columns to be retrieved. The big difference here is that two of the specified columns (prod_name and prod_price) are in one table, whereas the other (vend_name) is in another table.

Now look at the FROM clause. Unlike all the **prior** SELECT statements, this one has two tables listed in the FROM clause, Vendors and Products. These are the names of the two tables that are being joined in this SELECT statement. The tables are correctly joined with a WHERE clause that instructs the DBMS to match vend_id in the Vendors table with vend_id in the Products table.

You’ll notice that the columns are specified as Vendors.vend_id and Products.vend_id. This fully qualified column name is required here because if you just specified vend_id, the DBMS cannot tell which vend_id columns you are referring to. (There are two of them, one in each table.) As you can see in the preceding output, a single SELECT statement returns data from two different tables.

> **Caution: Fully Qualifying Column Names**
>
> As noted in the previous lesson, you must use the fully qualified column name (table and column separated by a period) whenever there is a possible ambiguity about which column you are referring to. Most DBMSs will return an error message if you refer to an ambiguous column name without fully qualifying it with a table name.

### The Importance of the WHERE Clause

It might seem strange to use a WHERE clause to set the join relationship, but actually, there is a very good reason for this. Remember, when tables are joined in a SELECT statement, that relationship is constructed on-the-fly. There is nothing in the database table definitions that can instruct the DBMS how to join the tables. **You have to do that yourself.** When you join two tables, what you are actually doing is pairing every row in the first table with every row in the second table. The WHERE clause acts as a filter to only include rows that match the **specified filter condition—the join condition,** in this case. Without the WHERE clause, every row in the first table will be paired with every row in the second table, regardless of if they logically go together or not.

```python
MySQL [distributor]> select vend_name, prod_name, prod_price
    -> from vendors, products;
+-----------------+---------------------+------------+
| vend_name       | prod_name           | prod_price |
+-----------------+---------------------+------------+
| Bear Emporium   | Fish bean bag toy   |       3.49 |
| Bears R Us      | Fish bean bag toy   |       3.49 |
| Doll House Inc. | Fish bean bag toy   |       3.49 |
| Fun and Games   | Fish bean bag toy   |       3.49 |
| Furball Inc.    | Fish bean bag toy   |       3.49 |
| Jouets et ours  | Fish bean bag toy   |       3.49 |
| Bear Emporium   | Bird bean bag toy   |       3.49 |
| Bears R Us      | Bird bean bag toy   |       3.49 |
| Doll House Inc. | Bird bean bag toy   |       3.49 |
| Fun and Games   | Bird bean bag toy   |       3.49 |
| Furball Inc.    | Bird bean bag toy   |       3.49 |
| Jouets et ours  | Bird bean bag toy   |       3.49 |
| Bear Emporium   | Rabbit bean bag toy |       3.49 |
| Bears R Us      | Rabbit bean bag toy |       3.49 |
| Doll House Inc. | Rabbit bean bag toy |       3.49 |
| Fun and Games   | Rabbit bean bag toy |       3.49 |
| Furball Inc.    | Rabbit bean bag toy |       3.49 |
| Jouets et ours  | Rabbit bean bag toy |       3.49 |
| Bear Emporium   | 8 inch teddy bear   |       5.99 |
| Bears R Us      | 8 inch teddy bear   |       5.99 |
| Doll House Inc. | 8 inch teddy bear   |       5.99 |
| Fun and Games   | 8 inch teddy bear   |       5.99 |
| Furball Inc.    | 8 inch teddy bear   |       5.99 |
| Jouets et ours  | 8 inch teddy bear   |       5.99 |
| Bear Emporium   | 12 inch teddy bear  |       8.99 |
| Bears R Us      | 12 inch teddy bear  |       8.99 |
| Doll House Inc. | 12 inch teddy bear  |       8.99 |
| Fun and Games   | 12 inch teddy bear  |       8.99 |
| Furball Inc.    | 12 inch teddy bear  |       8.99 |
| Jouets et ours  | 12 inch teddy bear  |       8.99 |
| Bear Emporium   | 18 inch teddy bear  |      11.99 |
| Bears R Us      | 18 inch teddy bear  |      11.99 |
| Doll House Inc. | 18 inch teddy bear  |      11.99 |
| Fun and Games   | 18 inch teddy bear  |      11.99 |
| Furball Inc.    | 18 inch teddy bear  |      11.99 |
| Jouets et ours  | 18 inch teddy bear  |      11.99 |
| Bear Emporium   | Raggedy Ann         |       4.99 |
| Bears R Us      | Raggedy Ann         |       4.99 |
| Doll House Inc. | Raggedy Ann         |       4.99 |
| Fun and Games   | Raggedy Ann         |       4.99 |
| Furball Inc.    | Raggedy Ann         |       4.99 |
| Jouets et ours  | Raggedy Ann         |       4.99 |
| Bear Emporium   | King doll           |       9.49 |
| Bears R Us      | King doll           |       9.49 |
| Doll House Inc. | King doll           |       9.49 |
| Fun and Games   | King doll           |       9.49 |
| Furball Inc.    | King doll           |       9.49 |
| Jouets et ours  | King doll           |       9.49 |
| Bear Emporium   | Queen doll          |       9.49 |
| Bears R Us      | Queen doll          |       9.49 |
| Doll House Inc. | Queen doll          |       9.49 |
| Fun and Games   | Queen doll          |       9.49 |
| Furball Inc.    | Queen doll          |       9.49 |
| Jouets et ours  | Queen doll          |       9.49 |
+-----------------+---------------------+------------+
54 rows in set (0.044 sec)
```

As you can see in the preceding output, the Cartesian product is seldom what you want. The data returned here has matched every product with every vendor, including products with the incorrect vendor (and even vendors with no products at all).

> **Caution: Don’t Forget the WHERE Clause** 
>
> Make sure all your joins have WHERE clauses, or the DBMS will return far more data than you want. Similarly, make sure your WHERE clauses are correct. An incorrect filter condition will cause the DBMS to return incorrect data.

> **Tip: Cross Joins** 
>
> Sometimes you’ll hear the type of join that returns a **Cartesian Product** referred to as a cross join.



### Inner Joins 

The join you have been using so far is called an equijoin—a join based on the testing of equality between two tables. This kind of join is also called an inner join. In fact, you may use a slightly different syntax for these joins, specifying the type of join explicitly. The following SELECT statement returns the exact same data as the preceding example:

```python 
MySQL [distributor]> select vend_name, prod_name, prod_price
    -> from vendors inner join products
    -> on vendors.vend_id = products.vend_id;
+-----------------+---------------------+------------+
| vend_name       | prod_name           | prod_price |
+-----------------+---------------------+------------+
| Doll House Inc. | Fish bean bag toy   |       3.49 |
| Doll House Inc. | Bird bean bag toy   |       3.49 |
| Doll House Inc. | Rabbit bean bag toy |       3.49 |
| Bears R Us      | 8 inch teddy bear   |       5.99 |
| Bears R Us      | 12 inch teddy bear  |       8.99 |
| Bears R Us      | 18 inch teddy bear  |      11.99 |
| Doll House Inc. | Raggedy Ann         |       4.99 |
| Fun and Games   | King doll           |       9.49 |
| Fun and Games   | Queen doll          |       9.49 |
+-----------------+---------------------+------------+
9 rows in set (0.033 sec)
```

The SELECT in the statement is the same as the preceding SELECT statement, but the FROM clause is different. Here the relationship between the two tables is part of the FROM clause specified as INNER JOIN. When using this syntax the join condition is specified using the special ON clause instead of a WHERE clause. The actual condition passed to ON is the same as would be passed to WHERE.

Refer to your DBMS documentation to see which syntax is preferred.

> **Note: The “Right” Syntax** 
>
> Per the ANSI SQL specification, use of the INNER JOIN syntax is preferred over the simple equijoins syntax used previously. Indeed, SQL purists tend to look upon **the simple synta**x with disdain. That being said, DBMSs do indeed support both the simpler and the standard formats, so my recommendation is that you take the time to understand both formats, but use whichever you feel more comfortable with.

### Joining Multiple Tables

SQL imposes no limit to the number of tables that may be joined in a SELECT statement. The basic rules for creating the join remain the same. First list all the tables, and then define the relationship between each. Here is an example:

```python
MySQL [distributor]> select prod_name, vend_name, prod_price, quantity
    -> from orderitems, products, vendors
    -> where products.vend_id = vendors.vend_id
    -> and orderitems.prod_id = products.prod_id
    -> and order_num = 20007;
+---------------------+-----------------+------------+----------+
| prod_name           | vend_name       | prod_price | quantity |
+---------------------+-----------------+------------+----------+
| 18 inch teddy bear  | Bears R Us      |      11.99 |       50 |
| Fish bean bag toy   | Doll House Inc. |       3.49 |      100 |
| Bird bean bag toy   | Doll House Inc. |       3.49 |      100 |
| Rabbit bean bag toy | Doll House Inc. |       3.49 |      100 |
| Raggedy Ann         | Doll House Inc. |       4.99 |       50 |
+---------------------+-----------------+------------+----------+
5 rows in set (0.065 sec)
```

This example displays the items in order number 20007. Order items are stored in the OrderItems table. Each product is stored by its product ID, which refers to a product in the Products table. The products are linked to the appropriate vendor in the Vendors table by the vendor ID, which is stored with each product record. The FROM clause here lists the three tables, and the WHERE clause defines both of those join conditions. An additional WHERE condition is then used to filter just the items for order 20007.

> **Caution: Performance Considerations** 
>
> DBMSs process joins at run-time relating each table as specified. This process can become very resource intensive so be careful not to join tables unnecessarily. The more tables you join the more performance will degrade.

> **Caution: Maximum Number of Tables in a Join** 
>
> While it is true that SQL itself has no maximum number of tables per join restriction, many DBMSs do indeed have restrictions. Refer to your DBMS documentation to determine what restrictions there are, if any.

Now would be a good time to revisit the following example from Lesson 11, “Working with Subqueries.” As you will recall, this SELECT statement returns a list of customers who ordered product RGAN01:

```python
SELECT cust_name, cust_contact 
FROM Customers 
WHERE cust_id IN (SELECT cust_id 
                  FROM Orders 
                  WHERE order_num IN (SELECT order_num 
                                      FROM OrderItems 
                                      WHERE prod_id = 'RGAN01'));
#这用于思考的过程
```

As I mentioned in Lesson 11, subqueries are not always the most efficient way to perform complex SELECT operations, and so as promised, here is the same query using joins:

```python
MySQL [distributor]>  select cust_name, cust_contact
    -> from Customers, Orders, OrderItems
    -> where customers.cust_id = orders.cust_id
    -> and orders.order_num = orderitems.order_num
    -> and prod_id = 'rgan01';
+---------------+--------------------+
| cust_name     | cust_contact       |
+---------------+--------------------+
| Fun4All       | Denise L. Stephens |
| The Toy Store | Kim Howard         |
+---------------+--------------------+
2 rows in set (0.012 sec)

```

As explained in Lesson 11, returning the data needed in this query requires the use of three tables. But instead of using them within nested subqueries, here two joins are used to connect the tables. There are three WHERE clause conditions here. The first two connect the tables in the join, and the last one filters the data for product RGAN01.

> **Tip: It Pays to Experiment** 
>
> As you can see, there is often more than one way to perform any given SQL operation. And there is rarely a definitive right or wrong way. Performance can be affected by the type of operation, the DBMS being used, the amount of data in the tables, whether or not indexes and keys are present, and a whole slew of other criteria. Therefore, it is often worth experimenting with different selection mechanisms to find the one that works best for you.





## Summary

Joins are one of the most important and powerful features in SQL, and using them effectively requires a basic understanding of relational database design. In this lesson, you learned some of the basics of relational database design as an introduction to learning about joins. You also learned how to create an **equijoin** (also known as an inner join), which is the most commonly used form of join. In the next lesson, you’ll learn how to create other types of joins.

> equijoin这个名字起的好呀.

