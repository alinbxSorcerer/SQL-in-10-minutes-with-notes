# Lesson 1. Understanding SQL

In this lesson, you’ll learn exactly what SQL is and what it will do for you.

> **Summary**
>
> database, container; table, file; column field datatype primary key; row record, 



## Database Basics

The fact that you are reading a book on SQL **indicates** that you, somehow, need to interact with databases. SQL is a language used to do just this, **so before looking at SQL itself,** it is important that you understand some basic concepts about databases and database technologies.

Whether you are aware of it or not, you use databases all the time. 

- Each time you select a name from your email address book, you are using a database. 
- If you conduct a search on an Internet search site, you are using a database. 
- When you log into your network at work, you are validating your name and password against a database. 
- Even when you use your ATM card at a cash machine, you are using databases for PIN number verification and balance checking.

**But even though** we all use databases all the time, **there remains** much confusion over what exactly a database is. This is especially true because different people use the same database terms to mean different things. Therefore, **a good place to start our study** is with a list and explanation of the most important database terms.

> **Tip: Reviewing Basic Concepts**
>
> What follows is a very brief overview of some basic database concepts. It is intended to either **jolt** your memory if you already have some database experience, or to provide you with the absolute basics, if you are new to databases. Understanding databases is an important part of mastering SQL, and you might want to find a good book on database fundamentals to **brush up** on the subject if needed.

### Databases

The term database is used in many different ways, but for our purposes (and indeed, from SQL’s perspective) a database is a collection of data stored in some organized fashion. The simplest way to think of it is to imagine a database as a filing cabinet. The filing cabinet is simply a physical location to store data, regardless of what that data is or how it is organized.

> **Database**
>
> A container (usually a file or set of files) to store organized data.
>
> **Caution: Misuse Causes Confusion**
>
> People often use the term database to refer to the database software **they are running.** This is incorrect, and it is **a source of much confusion.** Database software is actually called the Database Management System (or DBMS). The database is the **container** created and manipulated via the DBMS, and exactly what the database is and what form it takes varies from one database to the next.

### Tables

When you store information in your filing cabinet, you don’t just toss it in a drawer. Rather, you create files within the filing cabinet, and then you file related data in specific files.

In the database world, **that file is called a table.** A table is a structured file that can store data of a specific type. A table might contain a list of customers, a product catalog, or any other list of information.

> **Table** is a file
>
> A structured list of data of a specific type.

The key here is that the data stored in the table is **one type of data or one list.** You would never store a list of customers and a list of orders in the same database table. Doing so would make subsequent retrieval and access difficult. Rather, you’d create two tables, one for each list.

**Every table in a database has a name that identifies it.** That name is always unique— meaning no other table in that database can have the same name.

> **Note: Table Names**
>
> What makes a table name unique is actually a combination of several things including the database name and table name. Some databases also use the name of the database owner as part of the unique name. This means that while you cannot use the same table name twice in the same database, you definitely can reuse table names in different databases.

Tables have characteristics and properties that define how data is stored in them. These include information about what data may be stored, how it is broken up, how individual pieces of information are named, and much more. **This set of information that describes a table is known as a schema,** and schemas are used to describe specific tables within a database, as well as entire databases (and the relationship between tables in them, if any).

> **Schema**
>
> Information about database and table layout and properties.



### Columns and Datatypes Field

Tables are **made up** of columns. A column contains a particular piece of information within a table.

> **Column**
>
> A single field in a table. All tables are made up of one or more columns.

The best way to understand this is to **envision** database tables as grids, somewhat like spreadsheets. Each column in the grid contains a particular piece of information. In a customer table, for example, one column contains the customer number, another contains the customer name, and the address, city, state, and ZIP code are all stored in their own columns.

> **Tip: Breaking Up Data**
>
> It is extremely important to **break data into multiple columns correctly**. For example, city, state, and ZIP code should always be separate columns. By breaking these out, it becomes possible to sort or filter data by specific columns (for example, to find all customers in a particular state or in a particular city). If city and state are combined into one column, it would be extremely difficult to **sort or filter** by state.
>
> When you break up data, the level of granularity is up to you and your specific requirements. For example, addresses are typically stored with the house number and street name together. This is fine, unless you might one day need to sort data by street name, in which case splitting house number and street name would be preferable.

Each column in a database has an associated datatype. A datatype defines what type of data the column can contain. For example, if the column were to contain a number(perhaps the number of items in an order), the datatype would be a numeric datatype. If the column were to contain dates, text, notes, currency amounts, and so on, the appropriate datatype would be used to specify this. 

> **Datatype**
>
>  A type of allowed data. Every table column has an **associated datatype** that restricts (or allows) specific data in that column. 

Datatypes restrict the type of data that can be stored in a column (for example, **preventing the entry of alphabetical characters into a numeric field**). Datatypes also help sort data correctly and play an important role in optimizing **disk usage**. As such, special attention must be given to picking the right datatype when tables are created.

> **Caution: Datatype Compatibility**
>
> Datatypes and their names are one of the primary sources of SQL **incompatibility.** While most basic datatypes are supported consistently, many more advanced datatypes are not. And worse, occasionally you’ll find that the same datatype is referred to by different names in different DBMSs. There is not much you can do about this, but it is important to keep in mind when you create **table schemas**.



### Rows record

Data in a table is stored in rows; each record saved is stored in its own row. Again, envisioning a table as a spreadsheet style grid, the vertical columns in the grid are the table columns, and the horizontal rows are the table rows.

For example, a customers table might store one customer per row. The number of rows in the table is the number of records in it.

> **Rows**
>
> A record in a table.
>
> **Note: Records or Rows?**
>
> You may hear users refer to database records when referring to rows. For the most part the two terms are used interchangeably, but row is technically the correct term.



### Primary Keys

Every row in a table should have some column (or set of columns) that uniquely identifies it. A table containing customers might use a customer number column for this purpose, **whereas** a table containing orders might use the order ID. An employee list table might use an employee ID or the employee Social Security number column.

> **Primary key**
>
> A column (or set of columns) whose values uniquely identify every row in a table.

This column (or set of columns) that **uniquely identifies** each row in a table is called a primary key. The primary key is used to refer to a specific row. Without a primary key, updating or deleting specific rows in a table becomes extremely difficult as there is **no guaranteed safe wa**y to refer to just the rows to be affected.

> **Tip: Always Define Primary Keys**
>
> Although primary keys are not actually required, most database designers ensure that every table they create has a primary key so that future data manipulation is possible and manageable.

Any column in a table can be established as the primary key, as long as it meets the following conditions:

- No two rows can have the same primary key value.
- Every row must have a primary key value. (Primary key columns may not allow NULL values.)
-  Values in primary key columns should never be modified or updated.
- Primary key values should **never be reused.** (If a row is deleted from the table, its primary key may not be assigned to any new rows in the future.)

> 这四条总结得棒.

Primary keys are usually defined on a single column within a table. But this is not required, and multiple columns may be used together **as a primary key.** When multiple columns are used, the rules listed above must apply to all columns, and the values of all columns together must be unique (individual columns need not have unique values).

There is another very important type of key called a foreign key, but I’ll get to that later on in Lesson 12, “Joining Tables.”



## What Is SQL?

SQL (pronounced as the letters S-Q-L or as sequel) is an abbreviation for Structured Query Language. SQL is a language designed specifically for **communicating with databases.**

Unlike other languages (spoken languages like English, or programming languages like Java, C, or PHP), SQL is made up of very few words. This is deliberate. SQL is designed to do one thing and do it well—provide you with a simple and efficient way to read and write data from a database.

**What are the advantages of SQL?**

- SQL is not a **proprietary** language used by specific database **vendors**. Almost every major DBMS supports SQL, so learning this one language will enable you to interact with just about every database you’ll run into.
- SQL is easy to learn. The statements are all made up of descriptive English words, and there aren’t that many of them.
- Despite its apparent simplicity, SQL is actually a very powerful language, and by **cleverly using its language elements you can perform very complex and sophisticated database operations.**



And with that, let’s learn SQL.

> **Note: SQL Extensions**
>
> Many DBMS vendors have extended their support for SQL by adding statements or instructions to the language. The purpose of these extensions is to provide additional functionality or simplified ways to perform specific operations. And while often extremely useful, these extensions tend to be very DBMS specific, and they are rarely supported by more than a single vendor.
>
> Standard SQL is governed by the ANSI standards committee, and is thus called ANSI SQL. All major DBMSs, even those with their own extensions, support ANSI SQL. Individual implementations have their own names (PL-SQL, Transact-SQL, and so forth).
>
> For the most part, the SQL taught in this book is ANSI SQL. On the odd occasion where DBMS specific SQL is used it is so noted.



## Try It Yourself

Like any language, the best way to learn SQL is to try it for yourself. To do this you’ll need a database and an application with which to test your SQL statements.

All of the lessons in this book use real SQL statements and real database tables. Appendix A, “The Example Tables,” explains what the example tables are, and provides details on how to obtain (or create) them so that you may follow along with the instructions in each lesson. Appendix B, “Working in Popular Applications,” describes the steps needed to execute your SQL in a variety of applications. Before **proceeding to** the next lesson, I’d strongly suggest that you turn to these two appendixes so that you’ll be ready to follow along.



## Summary

In this first lesson, you learned what SQL is and why it is useful. Because SQL is used to interact with databases, you also reviewed some basic database terminology.



**granularity** /ˈɡræn.jəˈlær.ə.ti

1. 助记, grain, hard small seed

2. 词源,

   1790, from Late Latin granulum "granule, a little grain," diminutive of Latin granum "grain, seed" (from PIE root [*gre-no-](https://www.etymonline.com/word/*gre-no-?ref=etymonline_crossreference) "grain") + [-ar](https://www.etymonline.com/word/-ar?ref=etymonline_crossreference). Replaced granulous (late 14c.). Related: Granularity. 

3. 释义,

   the quality of including a lot of small details.

   The [marketing](https://dictionary.cambridge.org/zhs/%E8%AF%8D%E5%85%B8/%E8%8B%B1%E8%AF%AD-%E6%B1%89%E8%AF%AD-%E7%AE%80%E4%BD%93/marketing) [analysis](https://dictionary.cambridge.org/zhs/%E8%AF%8D%E5%85%B8/%E8%8B%B1%E8%AF%AD-%E6%B1%89%E8%AF%AD-%E7%AE%80%E4%BD%93/analysis) [offers](https://dictionary.cambridge.org/zhs/%E8%AF%8D%E5%85%B8/%E8%8B%B1%E8%AF%AD-%E6%B1%89%E8%AF%AD-%E7%AE%80%E4%BD%93/offer) a high [level](https://dictionary.cambridge.org/zhs/%E8%AF%8D%E5%85%B8/%E8%8B%B1%E8%AF%AD-%E6%B1%89%E8%AF%AD-%E7%AE%80%E4%BD%93/level) of granularity. 





