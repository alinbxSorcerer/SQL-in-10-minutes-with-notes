# Lesson 20. Managing Transaction Processing

In this lesson, you’ll learn what transactions are and how to use COMMIT and ROLLBACK statements to manage transaction processing.



## Understanding Transaction Processing

Transaction processing is used to maintain database integrity by ensuring that batches of SQL operations execute completely or not at all.

As explained back in Lesson 12, “Joining Tables,” relational databases are designed so that data is stored in multiple tables to facilitate easier data manipulation, management, and reuse. Without going in to the hows and whys of relational database design, take it as a given that well-designed database schemas are relational to some degree.

The Orders tables that you’ve been using in the past 18 lessons are a good example of this. Orders are stored in two tables: Orders stores actual orders, and OrderItems stores the individual items ordered. These two tables are related to each other using unique IDs called primary keys (as discussed in Lesson 1, “Understanding SQL”). These tables, in turn, are related to other tables containing customer and product information.

The process of adding an order to the system is as follows:

1. Check if the customer is already in the database. If not, add him or her. 2. Retrieve the customer’s ID.

2. Add a row to the Orders table associating it with the customer ID. 4. Retrieve the new order ID assigned in the Orders table.

3. Add one row to the OrderItems table for each item ordered, associating it with the Orders table by the retrieved ID (and with the Products table by product ID).

Now imagine that some database failure (for example, out of disk space, security restrictions, table locks) prevents this entire sequence from completing. What would happen to your data?

Well, if the failure occurred after the customer was added and before the Orders table was added, there is no real problem. It is perfectly valid to have customers without orders. When you run the sequence again, the inserted customer record will be retrieved and used. You can effectively pick up where you left off.

But what if the failure occurred after the Orders row was added, but before the OrderItems rows were added? Now you’d have an empty order sitting in your database. Worse, what if the system failed during adding the OrderItems rows? Now you’d end up with a partial order in your database, but you wouldn’t know it. How do you solve this problem? That’s where Transaction Processing comes in. Transaction Processing is a mechanism used to manage sets of SQL operations that must be executed in batches so as to ensure that databases never contain the results of partial operations. With Transaction Processing, you can ensure that sets of operations are not aborted mid-processing—they either execute in their entirety or not at all (unless explicitly instructed otherwise). If no error occurs, the entire set of statements is committed (written) to the database tables. If an error does occur, then a rollback (undo) can occur to restore the database to a known and safe state. 

So, looking at the same example, this is how the process would work: 

1. Check if the customer is already in the database; if not add him or her. 
2. Commit the customer information. 
3. Retrieve the customer’s ID. 
4. Add a row to the Orders table. 
5. If a failure occurs while adding the row to Orders, roll back. 
6. Retrieve the new order ID assigned in the Orders table. 
7. Add one row to the OrderItems table for each item ordered. 
8. If a failure occurs while adding rows to OrderItems, roll back all the OrderItems rows added and the Orders row. 

When working with transactions and transaction processing, there are a few keywords that’ll keep reappearing. Here are the terms you need to know: 

- *Transaction* A block of SQL statements 
- Rollback The process of undoing specified SQL statements 
- Commit Writing unsaved SQL statements to the database tables 
- Savepoint A temporary placeholder in a transaction set to which you can issue a rollback (as opposed to rolling back an entire transaction)

>  **Tip: Which Statements Can You Roll Back?**
>
> Transaction processing is used to manage INSERT, UPDATE, and DELETE statements. You cannot roll back SELECT statements. (There would not be much point in doing so anyway.) You cannot roll back CREATE or DROP operations. These statements may be used in a transaction block, but if you perform a rollback they will not be undone.



## Controlling Transactions

Now that you know what transactions processing is, let’s look at what is involved in managing transactions.

> **Caution: Implementation Differences**
>
> The exact syntax used to implement transaction processing differs from one DBMS to another. Refer to your DBMS documentation before proceeding.

The key to managing transactions involves breaking your SQL statements into logical chunks and explicitly stating when data should be rolled back and when it should not.

Some DBMSs require that you explicitly mark the start and end of transaction blocks. In SQL Server, for example, you can do the following:

```python
BEGIN TRANSACTION ... COMMIT TRANSACTION
```

In this example, any SQL between the BEGIN TRANSACTION and COMMIT TRANSACTION statements must be executed entirely or not at all.

The equivalent code in MariaDB and MySQL is:

```python
START TRANSACTION 
...
```

Other DBMSs use variations of the above. You’ll notice that most implementations don’t have an explicit end of transaction. Rather, the transaction exists until something terminates it, usually a COMMITT to save changes or a ROLLBACK to undo then, as will be explained next.

### Using Rollback

The SQL ROLLBACK command is used to roll back (undo) SQL statements, as seen in this next statement:

```python
DELETE FROM Orders; 
ROLLBACK;
```

In this example, a DELETE operation is performed and then undone using a ROLLBACK statement. Although not the most useful example, it does demonstrate that, within a transaction block, DELETE operations (like INSERT and UPDATE operations) are never final.

### Using Commit

Usually SQL statements are executed and written directly to the database tables. This is known as an implicit commit—the commit (write or save) operation happens automatically.

Within a transaction block, however, commits might not occur implicitly. This, too, is DBMS specific. Some DBMSs treat a transaction end as an implicit commit; others do not.

To force an explicit commit, the COMMIT statement is used. The following is a SQL Server example:

```python
BEGIN TRANSACTION 
DELETE OrderItems WHERE order_num = 12345 
DELETE Orders WHERE order_num = 12345 
COMMIT TRANSACTION
```

In this SQL Server example, order number 12345 is deleted entirely from the system. Because this involves updating two database tables, Orders and OrderItems, a transaction block is used to ensure that the order is not partially deleted. The final COMMIT statement writes the change only if no error occurred. If the first DELETE worked, but the second failed, the DELETE would not be committed.

To accomplish the same thing in Oracle, you can do the following:

```python
SET TRANSACTION 
DELETE OrderItems WHERE order_num = 12345; 
DELETE Orders WHERE order_num = 12345; 
COMMIT;
```

### Using Savepoints

Simple ROLLBACK and COMMIT statements enable you to write or undo an entire transaction. Although this works for simple transactions, more complex transactions might require partial commits or rollbacks.

For example, the process of adding an order described previously is a single transaction. If an error occurs, you only want to roll back to the point before the Orders row was added. You do not want to roll back the addition to the Customers table (if there was one).

To support the rollback of partial transactions, you must be able to put placeholders at strategic locations in the transaction block. Then, if a rollback is required, you can roll back to one of the placeholders.

In SQL, these placeholders are called savepoints. To create one in MariaDB, MySQL, and Oracle, the SAVEPOINT statement is used, as follows:

```python
SAVEPOINT delete1;
```

In SQL Server you do the following:

```python
SAVE TRANSACTION delete1;
```

Each savepoint takes a unique name that identifies it so that, when you roll back, the DBMS knows where you are rolling back to. To roll back to this savepoint, do the following in SQL Server:

```python
ROLLBACK TRANSACTION delete1;
```

In MariaDB, MySQL, and Oracle you can do the following:

```python
ROLLBACK TO delete1;
```

The following is a complete SQL Server example:

```python
BEGIN TRANSACTION 
INSERT INTO Customers(cust_id, cust_name) 
VALUES('1000000010', 'Toys Emporium'); 
SAVE TRANSACTION StartOrder; 
INSERT INTO Orders(order_num, order_date, cust_id) VALUES(20100,'2001/12/1','1000000010'); 
IF @@ERROR <> 0 ROLLBACK TRANSACTION StartOrder; 
INSERT INTO OrderItems(order_num, order_item, prod_id, quantity, item_price)
VALUES(20100, 1, 'BR01', 100, 5.49); 
IF @@ERROR <> 0 ROLLBACK TRANSACTION StartOrder; 
INSERT INTO OrderItems(order_num, order_item, prod_id, quantity, item_price) VALUES(20100, 2, 'BR03', 100, 10.99); 
IF @@ERROR <> 0 ROLLBACK TRANSACTION StartOrder; 
COMMIT TRANSACTION
```

Here are a set of four INSERT statements enclosed within a transaction block. A savepoint is defined after the first INSERT so that, if any of the subsequent INSERT operations fail, the transaction is only rolled back that far. In SQL Server, a variable named @@ERROR can be inspected to see if an operation succeeded. (Other DBMSs use different functions or variables to return this information.) If @@ERROR returns a value other than 0, an error occurred, and the transaction rolls back to the savepoint. If the entire transaction is processed, a COMMIT is issued to save the data.

> **Tip: The More Savepoints the Better**
>
> You can have as many savepoints as you’d like within your SQL code, and the more the better. Why? Because the more savepoints you have the more flexibility you have in managing rollbacks exactly as you need them.

## Summary

In this lesson, you learned that transactions are blocks of SQL statements that must be executed as a batch. You learned that COMMIT and ROLLBACK statements are used to explicitly manage when data is written and when it is undone. You also learned that savepoints provide a greater level of control over rollback operations. Transaction processing is a really important topic, and one that is far beyond the scope of one lesson. In addition, as you have seen here, transaction processing is implemented differently in each DBMS. As such, you should refer to your DBMS documentation for further details.

