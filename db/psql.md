

# SQL
<!-- writing : mohamadi_arch -->

# ACID
1. Atomicity
2. Consistency
3. Isolation
4. Durability

## MVCC
multiple version concurrency control



## Qestions?
1. how to select all cols just without one col





---------------------------------------------------------------------------------------------------
### Basic SELECT Statement

```sql
SELECT column_name, column_name
[ FROM table_source ]
[ WHERE search_condition ]
[ GROUP BY group_by_expression ]
[ HAVING search_condition ]
[ ORDER BY column_name DESC ]
[LIMIT 10]
;
-- singlie line comment
/*
multiple line comment
*/
```
Double quotes are for names of tables or fields. (Upper case letters only in double quotes apply)
The single quotes are for string constants.

```sql
select * from "Employee" where "employee_name"='elina';
```
#### alias

```sql
select employee_id as id from employee;        -- alias for column name[you can use alias in where clause but not in select clause]
select * from employee as e where e.last_name='elina'; -- alias for table name
-- as G ===> lower case g       as 'G' ===> upper case G    ===>as 'using space' ===> as 'using-dash' 
```

-------------------------------------------------------------------------------------
# Naming conventions

Singular names for tables
Singular names for columns
Schema name for tables prefix (E.g.: SchemeName.TableName)
Pascal casing (a.k.a. upper camel case)

----------------------------------------------------------------------------------------
# Managing Database

## data types
```bash
# CHAR better for the data values in a column are of the same length.It's 50% faster than VARCHAR. [static memory allocation]
# VARCHAR better for the data values in a column are of variable length. [dynamic memory allocation]
# VARCHAR is best suited for storing short to medium-length strings, while TEXT is better suited for storing large amounts of textual data. TEXT:fixed max size of 65535 characters(you cannot limit the max size)
Numeric             -- INTEGER=INT, BIGINT, REAL, DOUBLE PRECISION, DECIMAL, NUMERIC(precision, scale), SERIAL [for auto increment]
Character           --   CHARACTER(n)=CHAR, VARCHAR(n), TEXT, CLOB       
Temporal             -- DATE, TIME, TIMESTAMP, INTERVAL
List          -- JSON, ENUM, ARRAY, HSTORE,
UUID             --for storing UUID (Universally Unique Identifiers) 
Special          -- such as network address and geometric data
```
## Constraint
```bash
NOT NULL              --Ensures that a column cannot have NULL value.
UNIQUE                      -- Ensures that all values in a column are different. 
DEFAULT                     -- Sets a default value for a column.
PRIMARY Key                 -- Not NULL + UNIQUE 
REFERENCES                 -- for foreign key
CHECK                       -- validation
EXCLUSION                    --ensures that if any two rows are compared on the specified column(s) or expression(s) using the specified operator(s), not all of these comparisons will return TRUE.]
############## refer to several columns.
PRIMARY KEY(col1, col2, ....)       UNIQUE(col1, col2, ....)          CHECK(col1 > 0)
```


## User and Schema

PRIVILEGES: 1.SELECT 3.INSERT 4.UPDATE 4.DELETE 5.TRUNCATE 6.USAGE(schema) 7.CONNECT(db) 8.EXECUTE(function and SP) 9.ALL
USAGE privilege: default privilage for new users, see objects like tables in public schema


```bash
CREATE USER ali WITH PASSWORD '123';                # create new user [in output message you get 'CREATE ROLE'] bcz role = user + login
CREATE ROLE dba;                                     # create a role
CREATE ROLE myrole WITH LOGIN PASSWORD '123';        # exactly like create user bcz role=user+login
CREATE USER ali LOGIN SUPERUSER PASSWORD '123';    # create new user as superuser
# when you create new user, that user can not query any table, create any user but can create new table and query on that
# owner of table can grant privileges to other users about his table
GRANT ALL PRIVILEGES ON DATABASE customers TO ali;    # grant all permissions to user ali   - ON DATABASE - IN SCHEMA - ALL TABLES IN SCHEMA
GRANT SELECT ON TABLE public.employees TO ali;       # grant select permission to user ali
GRANT ali TO myrole;                                # grant role
REVOKE CONNECT ON DATABASE customers FROM PUBLIC;      # -PUBLIC(NOT SUPERUERS) -ROLE -USER 
REVOKE INSERT ON customer FROM ali;
ALTER USER ali CREATEDB;                           # CREATEDB privilage has special syntax
drop user ali;                                    # drop user (if uesr created table we get error)
drop role myrole;                                 # drop role
create schema fincance;                           # create new schema
create schema authorization ali;                       # create schema belong to user ali (sql selects with same table names will search inside this schema) ==> search_path
create table fincance.customer(id int);                  # create table in fincance scheama
select * from pg_tables where tablename='customer';      # check all tables in diff schemas with this name
drop schema fincance;                            # drop schema
drop schema fincance cascade;                    # drop schema with all dependencies (force)
```
### Manage Table

```sql
-- CREATE DATABASE
CREATE DATABASE db_name;
DROP DATABASE db_name;
CREATE DATABASE db_name OWNER user_name;                     -- owner is optional  [WITH OWNER = 'user_name']
COMMENT ON DATABASE db_name IS 'description';               -- comment for db
ALTER DATABASE "old_dbname" RENAME TO "new_dbname"           -- rename db
-- CREATE Table
CREATE TABLE table_name (
    -- you can specify a name for each CONSTRAINT [optional]
    column int IDENTITY(1,1) PRIMARY KEY, -- primary key and "IDENTITY(1,1)" for auto increment
    column data_type NOT NULL,            -- normal column
    column data_type NOT NULL FOREIGN KEY REFERENCES table_name(column) -- foreign key
);
SELECT * INTO new_table_name FROM table1;                                         -- create new table from existing table
ALTER TABLE table_name old_table_name RENAME new_table_name;       -- rename table
DROP TABLE table_name;                                              -- delete table
TRUNCATE TABLE table_name;                                          -- delete all data in table  also [TRUNCATE table_name]
-- You can use multiple actions in one statement 'ALTER TABLE table_name'
-- RENAME TABLE
ALTER TABLE table_name RENAME TO new_table_name;
-- ADD Column
ALTER TABLE table_name ADD col1 datatype;
-- DROP COLUMN
ALTER TABLE table_name DROP COLUMN [ IF EXISTS ] col1 datatype;       -- IF EXISTS is optional for prevent error
-- ALTER COLUMN
ALTER TABLE table_name ALTER COLUMN [ IF EXISTS ] col1 TYPE datatype;     -- IF EXISTS is optional for prevent error
-- RENAME COLUMN
ALTER TABLE table_name RENAME COLUMN [ IF EXISTS ] col1 TO new_col_name;
-- SET or DROP CONSTRAINT
ALTER TABLE table_name ALTER COLUMN col1 SET NOT NULL;    -- DROP NOT NULL
-- TABLE CONSTRAINT like PRIMARY KEY
ALTER TABLE table_name ADD CONSTRAINT constraint_name UNIQUE (col1, col2);   
ALTER TABLE yourtable DROP CONSTRAINT constraint_name;  -- you can see all constraint_name in \d table_name
ALTER TABLE emp ALTER COLUMN col1 DROP NOT NULL;  -- not null  can not be written in constraint
ALTER TABLE emp ALTER COLUMN col1 SET NOT NULL;      -- not null can not be written in constraint 
```

### INSERT INTO Table
```sql
INSERT INTO table_name (column, column) VALUES (value, value)        -- specify columns
INSERT INTO table_name VALUES (value, value)                    -- insert to all columns 
INSERT INTO table_name VALUES (value, value), (value, value)                    -- insert multiple rows
INSERT INTO table_name (column, column) SELECT column, column FROM table_name_2;    -- insert rows from another table [INSERT INTO SELECT]
UPDATE table_name SET column = value, column = value WHERE condition;    -- update a row
UPDATE table_name SET column = value + 20 ;                    -- update all rows
DELETE FROM table_name WHERE condition;                         -- delete rows
DELETE FROM table_name;                                             -- delete all rows
TRUNCATE TABLE table_name;           -- delete all rows
DELETE FROM table_name WHERE col in (values, ...) RETURNING *;       -- delete rows and return deleted rows for output
```

## VIEW
```sql
CREATE TABLE table_name AS SELECT * FROM table_name_2;    -- create TABLE from a query in physical storage [data will not update anymore]
CREATE VIEW table_name AS SELECT * FROM table_name_2;    -- create VIEW from a query (not physical storage) [runs select behind the scense (slow)]
CREATE MATERIALIZED VIEW view_name AS SELECT * FROM table_name_2 WITH DATA;    -- date stores physicaly so it is fast
REFRESH MATERIALIZED VIEW view_name;                                              -- refresh for updating data
-- for naming use v_table_name and mv_table_name for materialized
ALTER VIEW, ALTER MATERIALIZED VIEW
DROP VIEW, DROP MATERIALIZED VIEW
CREATE OR REPLACE VIEW table_name AS new_query;                                   -- replace view [ not work with tables and materialized views]
```

## tanble inheritance
child table inherits all columns from parent table

```bash
CREATE TABLE child_table (col1, col2) INHERITS (parent_table);
SELECT * FROM parent_table;                                       # display all columns from parent table and child records
SELECT * FROM ONLY parent_table;                                       # display columns of only parent
UPDATE parent SET col1 = 'val1';                                     # update parent table and child tables also
UPDATE ONLY parent SET col1 = 'val1';                                     # update only parent table
DROP TABLE parent;                                               # we can not delete parent table if it has child
DROP TABLE parent cascade;                                               # delete parent table and its child forcefully
```
### IMPORT and EXPORT
```sql
IMPORT FROM CSV FILE 'C:\path\file.csv' INTO table_name;        -- data in csv file import by order of columns
EXPORT TO CSV FILE 'C:\path\file.csv' FROM table_name;
```

---------------------------------------------------------------------------------------------

### WHERE Conditions
Logical Operators: AND, OR, NOT, IN , EXISTS, ANY, ALL, BETWEEN, IS NULL
matching operators: LIKE, ILIKE
Arithmetic Operators : + - / * % ^
Comparison Operators:  = >= <= > < != <>
Mathematical Functions: ABS(-45)=45, CEILING(4.6)=5, FLOOR, RAND, ROUND(4.2522,2)=4.25


```sql
SELECT column_name FROM table_name WHERE condition1 AND condition2;    -- OR, AND for multiple conditions [can not have multiple where]  [Operator precedence AND > OR]
select * from payment where amount = 10 OR amount = 11;             -- amount = 10 OR 11 is not ture
selcet * from payment where amount between 10 AND 20;               --BETWEEN ...AND .... ==>  amount >= 10 AND amount <= 20
selcet * from payment where date between '2018-01-01' AND '2018-12-31';              -- auto convert to '2018-01-01 00:00'
select * from customer where customer_id in (1,2,3,4,5,6,7,8,9,10);    -- IN (list)
-- EXISTS, ANY, ALL are logical operators [ture/false]
-- EXISTS
SELECT * FROM table1 WHERE EXISTS (SELECT column_name FROM table2)  --existence of any record in a subquery
-- ANY
SELECT * FROM table1 WHERE age >= ANY (SELECT age FROM table2)
-- ALL
SELECT * FROM table1 WHERE age <= ALL (SELECT age FROM table2)
```
### ORDER BY

```sql
SELECT * FROM games ORDER BY Type DESC, Title DESC       -- you should specify DESC for each column
SELECT Type, Title, Last_name  FROM games ORDER BY 1, 2    -- not good for readability
```

### GROUP BY
aggregation functions: SUM AVG MAX MIN COUNT ROUND ==> on one column
```sql
select sum(amount) from payment;
select sum(amount), avg(price) from payment;      -- can not use additional col: select sum(amount) ,price ==> but you can have some aggregation function  
-----------------------
select ROUND(sum(amount),2) from payment;         -- round(amount,2) = 255.12
-----------------------what is group by
select sum(amount) from payment;                                          -- sum of amounts for all customers just in one record in output
select sum(amount),customer_id from payment group by customer_id;         -- sum of amounts for each customer in a table [pk is payment_id]
select sum(amount),customer_id from payment group by customer_id order by sum(amount) desc;  -- sum(amount) is a column
-------------------------
select id,date from payment group by id,date;               -- group by id and date [no need to use aggregate functions in each group by]
-- each col must appear in the GROUP BY clause or be used in an aggregate function
------------------------ HAVING
select sum(amount),customer_id from payment group by customer_id having sum(amount) > 100;    -- set condition in aggregation function
```
#### GROUPING SETS
```sql
select sum(amount), customer_id from payment group by customer_id
union              -- also we should set NULL as date
select sum(amount), date from payment group by date
union               -- also we should set NULL as customer_id
select sum(amount), customer_id,date from payment group by customer_id,date
-------------equals ==
select sum(amount), customer_id,date from payment group by GROUPING SETS((customer_id), (date), (customer_id, date));
```

#### ROLLUP & CUBE
when we have multiple columns in GROUP BY
oreder in cube does not mattes but in rollup does
```sql
    select col1, col2, col3, sum(col4) from table GROUP BY ROLLUP (col1, col2, col3);      -- hierarchy of group sets
    select col1, col2, col3, sum(col4) from table GROUP BY CUBE (col1, col2, col3);         -- all posible combination of grouping sets 
----- ROLLUP equals to == create a role hierarchy of grouping sets [like date: year month week]
GROUP BY GROUPING SETS(
    (col1, col2, col3),
    (col1, col2),
    (col1),
    ()     -- means overall toatal of everything like sum of sums   [sum, sum(sum), sum(sum(sum))]
)
----- CUBE equals to == create a cube of all posible combination of grouping sets
GROUP BY GROUPING SETS(
    (col1, col2, col3),
    (col1, col2),
    (col1, col3),
    (col2, col3),    --[sum of col1 and col2, sum col2 and col3]
    (col1),(col2),(col3),()  -- () means overall totall  sum(sum()) 
```

### CASE Statement
like if condition
```sql
CASE
    WHEN condition1 THEN 'string'     -- we can have multiple WHEN
    ELSE 'false'                   -- else is optional
END -- as new_column_name
select * , case when price > 50 then 'expensive' end as status from games;    -- if you do not use  ELSE for other output is NULL
select sum(case when price > 50 then 1 else 0 end) from film;                  -- case trick for counting special value
select sum(case when ...), sum(case when ...) from tabel)               -- case trick for pvot like in excel  [a=100, b=150, c=200 ,...] like group by
-- pivoting with case and sum
SELECT id, SUM(CASE WHEN col1=5 THEN 1 ELSE 0 END) AS col_name FROM table GROUP BY id;   -- sum with if in case 
```
## FUNCTIONS

#### DATE FUNCTIONS
```sql
select * , DATE(payment_date) from payment;    -- convert timestamp to date: 2020-01-01
select * , EXRACT(YEAR FROM(payment_date) from payment;    -- YEAR, MONTH, DAY, SECOND, HOUR, MINUTE, DOW, WEEK, QUARTER as a number
select * TO_CHAR(payment_date, 'YYYY-DD') from payment;    -- convert timestamp to persnoal format [YY/MM, DAY, MONTH, ...] as a char
select CURRENT_DATE, CURRENT_TIME, CURRENT_TIMESTAMP;
select CURRENT_TIME - payment_date from payment;        -- calc diff ==> it is  interval
```

###### Types of time
```sql
DATE                  --  YYYY-MM-DD : '2019-01-01'
TIME  without timezone                  -- HH:MM:SS '10:15:30.635'
TIME with timezone    -- HH:MM:SS+Z '10:15:30.635+02'
TIMESTAMP without timezone    -- YYYY-MM-DD HH:MM:SS '2019-01-01 10:15:30.635'      [DATE + TIME]
TIMESTAMP with timezone    -- YYYY-MM-DD HH:MM:SS+Z   '2019-01-01 10:15:30.635+02' [DATE + TIME]
intervals                   -- differnce of two days
```

#### STRING FUNCTIONS

```sql
select * , UPPER(first_name) from customer;     -- upper, lower, length, left(ali,2)=al , right(ali,2)= li, 
-- substring(string from 1start for 3length)   substring(position, length)
-- position('@' IN  col_name) ==> index
-- concat(a,b) also || operator means concat,
-- Replace(col, sub_oldtext, sub_newtext)   ==> uselful for remove space
select * , TRIM(both from first_name, 'A') from customer;      --delete A from start and end of a  string [ltrim, rtrim, btrim]
```
### NULL FUNCTIONS
```sql
-- COALESCE() alternate: NVL(oracle), ISNULL(SQL server), IFNULL(MySQL)
SELECT COALESCE(NULL, NULL, col2) from table;              --return the first non-null value in a list [all argumets should have same data type]
SELECT COALESCE(CAST(timecol as VARCHAR), 'not found') from table;   -- trick for returning default value for null
```

### LIKE, ILIKE
LIKE is case sensitive
ILIKE is case INsensitive
we can use wildcards with LIKE and ILIKE

```sql
WHERE column LIKE 'a%'	-- % means any number of characters even zero
WHERE column LIKE '_ali%' -- undeline(_) means a single character
WHERE column LIKE '___' -- find all values that have exactly 3 characters
```
#### ADVANCED  FUNCTIONS
```sql
SELECT CAST(col1 as VARCHAR) from table;                    -- convert data type
```


#### DISTINCT
useful for age rating for movies
```sql
SELECT DISTINCT first_name FROM table_name    -- remove duplicates in this col [first_name]
SELECT DISTINCT on (first_name , last_name) ,first_name , last_name, gender FROM table_name    -- distinct on two col, but should add those name agin
SELECT last_name FROM customer GROUP BY last_name HAVING COUNT(*) > 1;    -- find duplicates (anti Distinct)
```

#### TABLE QUERY
```sql
```


### JOINS
- (INNER) JOIN:          Returns records that have matching values in both tables
- LEFT (OUTER) JOIN:     Return all records from the left table + the matched records ==> (set null for uncommon)
- FULL (OUTER) JOIN:     Return all records (set null for uncommon in both tables)
- RIGHT (OUTER) JOIN:    Similar to FULL (OUTER) JOIN
- NATRUAL JOIN:          Automatically join on same column name ( no need to specify ON T1.id=T2.id)
- CROSS JOIN:            Cartesian product of tables [col1: 1,2 col2: A,B,C  col1*col2= A1,B1,C1,A2,...,C3 ] -- all posible combination even dup

```sql
SELECT column_name(s)           -- A.*, B.* for select all columns
FROM tablea A JOIN tableb B    -- as A
    ON A.column_name = B.column_name;   -- if you set alias, you can not use tablea, tableb anymore
----------------- multiple join
select * from T1 join T2 on T1.id = T2.id and T1.name=T2.name [and ....]     -- multiple conditions 
select * from T1 join T2 on T1.id = T2.id INNER JOIN T3 on T2.address = T3.address   -- multiple tables [ no and]
SELECT * FROM tablea A INNER JOIN tableb B ON A.col1 = B.col1 AND B.col2 = 'value';    -- like where clause but more performance efficient 
------------------ self join
SELECT * FROM employee T1 left join employee T2 on T1.employee_id = T2.manager_id;     -- self-join
-- self join can be used for comparing rows in same table
------------------ cross join
SELECT T1.col1, T2.col2 from T1 CROSS JOIN T2;                    -- all posible combination between col1 and col2
```

### UNIONS
1. [match columns by order]
2. [must have the same numbere of columns]
3. [data type must match]
4. [UNION removes duplicates (UNION ALL for duplicates)]

```sql
SELECT col1,col2 FROM T1   -- trick: select col1 , 'table_name' ==> find which data comes from which table
UNION                       -- UNION ALL for duplicates
SELECT col1,col2 FROM T2    -- add multiple unions
---------------------------------
SELECT col1,col2, NULL as col3 FROM T1               -- for setting data types
UNION
SELECT col3,col2, NULL as col1 FROM T2
-- Trick: you can set multiple cols in one column by union
```

### Variable
```sql
-- define variable
WITH ctee (var1, var2) as (
   values (5, 'foo')
)
SELECT *
FROM table1, ctee
WHERE a = var1
   OR b = var2;
```

### Show values with select
```sql
SELECT a, b FROM (VALUES (0, 0), (1, 1)) AS t(a, b);       -- insert two column and two rows
VALUES (1, 'one'), (2, 'two'), (3, 'three');              -- without select, with VALUES you can create multiple rows
-----
WITH my_values(a, b, c) AS (
VALUES (1, 2, 3)
)
SELECT * FROM my_values;
-----
```

### CTE
Common Table Expressions
like sub-query, most CTE problems can solve with sub-query too
CTEs can be recursive but sub-queries can not
```sql
WITH cte_name AS (SELECT * FROM table1) SELECT * FROM cte_name;                     -- SELECT, INSERT, UPDATE ,...
WITH cte_name AS (SELECT sum(*) as sum FROM table1) SELECT avg(sum) FROM cte_name;   -- real-world example
WITH cte_name AS () , WITH cte_name2 AS () SELECT * FROM cte_name, cte_name2;   -- multiple CTE
WITH cte_name (f,l) AS (SELECT first_name , last_name FROM customer) SELECT * FROM cte_name;  -- change first_name col to f and ...
-- Recursive CTE
-- RECURSIVE CTE restrictions: select distict, group by, aggregation functions like sum, limit, left join, right join, pivot in tsql,  sub-queries
-- inner-join is allowed
WITH RECURSIVE t AS (    -- you can set column name here or in anchor member. in here: t(n) n is name of column
    SELECT 1 as n         -- anchor member or base member or non-recursive term   [selecte 1,1 creates two column]
  UNION ALL
    SELECT n+1 FROM t WHERE n < 100    -- recursive member [make sure for ending]
)
SELECT sum(n) FROM t;
--- Fiboncatchi  not work
-- the row set only contains the rows for the current recursion level, so you can not use LAG or LEAD window function
WITH RECURSIVE fibonacci(n, fib) AS (
    SELECT 1, 0
    UNION ALL
    SELECT n + 1, 
           CASE 
               WHEN n = 1 THEN 1
               ELSE fib + lag(fib) over (order by n)
           END
    FROM fibonacci
    WHERE n < 10
)
SELECT fib FROM fibonacci;
---hierarchy
WITH RECURSIVE boss_hierarchy AS (
    SELECT emp_id, boss_id, name
    FROM employees
    WHERE emp_id = 10

    UNION ALL

    SELECT e.emp_id, e.boss_id, e.name
    FROM employees e
    INNER JOIN boss_hierarchy bh ON e.emp_id = bh.boss_id
)
SELECT * FROM boss_hierarchy;
```

### SUB-QUERY

```sql
-- sub-query should return one column
-------------------------------------------------Normal sub-query

--------------------- WHERE (subquery)   
WHERE col = (SELECT AVG(col) FROM table) -- equal sign use for returning one single value
WHERE col IN (SELECT id FROM table)      -- IN use for multiple returning multiple values
----------------------FROM (subquery)
SELECT column_name(s) FROM (subquery) as alias           -- you should specify alias for sub-query in FROM
SELECT sum(x) FROM (select avg(*) as x from table) as alias    -- example for sub-query in FROM
----------------------SELECT (subquery)
SELECT *, (SELECT AVG(col) FROM table) FROM table   -- as a STATIC value, so it should return one single value 

---------------------------------------------------Correlated sub-query 
-- set alias for tables
-- correlated sub-query does not work independently  [you can not run it separately because it it dependent on outer query]
-- correlated sub-query can not be used in FROM
-- correlated sub-query is not good for performance [use window functions]
SELECT * FROM employee t where sales > (SELECT AVG(sales) FROM employee t2 WHERE t2.region = t.region)
```

### WINDOW FUNCTIONS
Perform calculations over a set of rows that are somehow related to the current row.
PARTITION BY: split up the rows into windows
ORDER BY: order the rows within the window
```sql
SELECT *, AGG(col1) OVER(PARTITION BY col2, ...) from table                 --  like correlated sub-query
SELECT *, count(col1) OVER() from table                                     --  total count without any partition
SELECT *, count(col1) OVER(order by pk) from table                    --  count step by step (1,2,...)
SELECT *, row_number() OVER(order by pk) from table                    -- Generate Row Serial Numbers
-- rank() has gap but dense_rank() not
SELECT *, dense_rank() OVER(order by col desc) from table           -- dense_rank() and rank() for ranking base on values [ we need to order values]
SELECT *, first_value(col) OVER(order by col) from table                   -- first value of rank(), order by desc for last value
SELECT *, LEAD(col) OVER(order by col1 desc) from table                   -- next value of rank() like step by step   [ LAG() for previous value]
SELECT *, LAG(id) OVER(order by id) from table                           -- get previous row. lag(id,2) 2 is offset
SELECT * FROM (SELECT *, rank() OVER(order by col desc) from table) as t WHERE t.rank <= 3      -- we can not use rank() in where clause [use sub-q]
```


### User Defined Functions

```sql
-- \sf func_col_name for see this script and \df for list of functions
-- user defined functions does not support transactions but Stored Procedures does
CREATE FUNCTION func_col_name(a INT, b INT)     -- CREATE OR REPLACE FUNCTION
RETURNS INT
LANGUAGE plpgsql          -- sql | c | python
AS
$$                               -- start the body
DECLARE                           -- declare variables
c INT;
BEGIN                              -- function definition
    SELECT a+b+3 INTO c;            -- write the returned value in variable
    RETURN c;
END;
$$                               -- end the body 
-- useage
SELECT func_col_name(1,2);       -- like table
SELECT first_name, last_name, func_col_name(first_name,last_name) from customer;  -- call function for each row of table
```

### TRANSACTION , ROLLBACK
like bank transaction
```sql
BEGIN; -- starts one unit of work
UPDATE table_name SET column = value - 100 WHERE id =1;
UPDATE table_name SET column = value + 100 WHERE id =2;
COMMIT;          -- before commit, only current session can see changes with select
----------- ROLLBACK and controll transactions
BEGIN;
OPERATION1;
OPERATION2;
ROLLBACK;       -- undo all previous operations [ends transaction so you need to start transaction again with BEGIN;]
OPERATION2;
OPERATION2;
SAVEPOINT op2;   -- savepoint like label
OPERATION;
ROLLBACK TO SAVEPOINT op2;   -- [does not end transaction]
RELEASE SAVEPOINT op2;   -- delete savepoint
COMMIT;
```

#### Stored Procedures

```sql
-- user defined functions does not support transactions but Stored Procedures does
CREATE PROCEDURE proc_name(a INT, b INT)     -- CREATE OR REPLACE PROCEDURE
LANGUAGE plpgsql          -- sql | c | python
AS
$$                               -- start the body
DECLARE                           -- declare variables
c INT;
BEGIN                              -- function definition
SELECT id from
SELECT id into c from table where first_name= a and last_name= b;  -- write the returned value in variable
UPDATE table_name SET column = value - 100 WHERE id =c;
COMMIT;                             -- commit the transactions
END;                               -- we do not have return in SP
$$                               -- end the body
-- useage
CALL proc_name(a,b)           -- no return
```


### INDEX
Index is used to speed up query and performance in read operations but not write operations
Data in db is stored without any order, actually they stored in pages (rows in block) whereever free space is available, this is not good for reading
the data (full table scan)
Indexes help to make data reads faster but slower for writes, also use additional storage
better to use on columns that used as filters in large databases (but not freequently update or insert)

1. BTree Index
default index
good for high-cardinality(unique) columns
there is an default index on pk in postgresql


1. Bitmap Index
good for dataware houses
good for large amount of data + low-cardinality (exp: one col can have only three values like enum)  [many repeating data]
very storage efficient and optimized for read but not good for those data that should be updated soon

```sql
CREATE INDEX index_name ON table_name [USING index_type] (col1, col2);      -- USING index_type is optional
DROP INDEX index_name;                                                    -- takes a lot of time
```









