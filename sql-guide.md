# 	SQL Guide

[TOC]

### create user and grant all permissions

```bash
$ sudo mysql -u root -p

mysql> CREATE USER 'newuser'@'localhost' IDENTIFIED BY 'password';
mysql> GRANT ALL PRIVILEGES ON * . * TO 'newuser'@'localhost';
mysql> FLUSH PRIVILEGES;
```



### basics

**Comment**

```mysql
-- this is a comment
# this is a comment
/*
multiline comment
*/
```



**Data types**

```mysql
# STRING DATA TYPES
CHAR(size) # upto 255, pads with spaces
VARCHAR(size) # upto 255, no padding, use less storage, slower

# NUMERICAL DATA TYPES
INT # can hold negative val
INT UNSIGNED # starts from 0
FLOAT(m, d) # m = total digits, d = digits after decimal point
DOUBLE(m, d)

# DATA N TIME
YEAR # 2 (2001-69, 1970-99) or 4 digits (1901-2155)
DATE # YYYY-MM-DD
TIME # HH:MI:SS
DATETIME # YYYY-MM-DD HH:MI:SS
TIMESTAMP # YYYY-MM-DD HH:MI:SS UTC
```



**Col Constraints**

```mysql
NOT NULL
UNIQUE
DEFAULT
PRIMARY KEY
AUTO_INCREMENT

```



**Extras**

```mysql
NOW()
```





### Create database

```mysql
CREATE DATABASE dbname;
```



### Using database

```mysql
USE dbname;
```



### Delete database

```mysql
DROP DATABASE IF EXISTS dbname;
DROP DATABASE dbname;
```



### Working with tables

**See a table structure**

```mysql
DESCRIBE employees;
```



**Creating table**

```mysql
# create table
CREATE TABLE table_name (
    column_name1 datatype [column constraints],
    column_name2 datatype [column constraints],
    ...
    [table constraints],
    [table constraints]
);

CREATE TABLE mytable (
	id INT PRIMARY KEY AUTO_INCREMENT,
    name VARCHAR(255) NOT NULL,
    gender CHAR(1) NOT NULL,
    phone VARCHAR(255),
    age INT NOT NULL,
    date_created TIMESTAMP NOT NULL DEFAULT NOW()
);

# DEFINING TABLE CONSTRAINTS
CREATE TABLE mentorships (
    mentor_id INT NOT NULL,
    mentee_id INT NOT NULL,
    status VARCHAR(255) NOT NULL,
    project VARCHAR(255) NOT NULL,
    
    PRIMARY KEY (mentor_id, mentee_id, project),
    CONSTRAINT fk1 FOREIGN KEY(mentor_id) REFERENCES
    co_employees(id) ON DELETE CASCADE ON UPDATE RESTRICT,
    CONSTRAINT fk2 FOREIGN KEY(mentee_id) REFERENCES
    co_employees(id) ON DELETE CASCADE ON UPDATE RESTRICT,
    CONSTRAINT mm_constraint UNIQUE(mentor_id, mentee_id)
);

```



**Altering table**

```mysql
# rename table name
RENAME TABLE old_name TO new_name;

# add and remove cols
ALTER TABLE employees
    DROP COLUMN age,
    ADD COLUMN salary FLOAT NOT NULL AFTER contact_number,
    ADD COLUMN years_in_company INT NOT NULL AFTER salary;

# alter foreign key constraints
ALTER TABLE mentorships
	DROP FOREIGN KEY fk2;
ALTER TABLE mentorships
	ADD CONSTRAINT fk2 FOREIGN KEY(mentee_id) REFERENCES employees(id) ON DELETE CASCADE ON UPDATE CASCADE,
	DROP INDEX mm_constraint;
```



**Delete table**

```mysql
DROP TABLE IF EXISTS table_name;
```



### Working with data

**View data**

```mysql
SELECT * FROM table_name;
```



**Insert data**

```mysql
INSERT INTO table_name (column1, column2, column3, ...)
VALUES (value1, value2, value3, ...);

INSERT INTO employees (em_name, gender, contact_number, salary, years_in_company) VALUES
('James Lee', 'M', '516-514-6568', 3500, 11),
('Peter Pasternak', 'M', '845-644-7919', 6010, 10);
```



**Update data**

```mysql
UPDATE table_name
SET column1 = value1, column2 = value2, ...
WHERE condition;

UPDATE employees
SET contact_number = '516-514-1729'
WHERE id = 1;
```



**Deleting data**

```mysql
DELETE FROM table_name
WHERE condition;
```



### Query data

```mysql
SELECT column_names_or_other_information
[AS alias]
[ORDER BY column(s)] [DESC]
FROM table_name
[WHERE condition];

# select everything
SELECT * FROM table_name;

# select specific fields
SELECT em_name, gender from employees;

# AS alias
SELECT em_name AS 'Employee Name', gender AS Gender FROM employees;

# limit results
SELECT em_name AS 'Employee Name', gender AS Gender FROM employees LIMIT 3;

# remove duplicates
SELECT DISTINCT(gender) FROM employees;

# WHERE clause
SELECT * FROM employees WHERE id != 1;

# get result between a range
SELECT * FROM employees WHERE id BETWEEN 1 AND 3;
SELECT * FROM employees WHERE id NOT BETWEEN 1 AND 3;

# matching pattern
SELECT * FROM employees WHERE em_name LIKE '%er';

/* In contrast to the % symbol that represents an unknown number of characters,
the _ symbol is used to represent exactly ONE character.
Suppose we want to select the rows of all employees that have ‘e’ as the fifth
letter in their names, we write: */
SELECT * FROM employees WHERE em_name LIKE '____e%';

# IN query
SELECT * FROM employees WHERE id IN (1, 2, 5);
SELECT * FROM employees WHERE id NOT IN (1, 3, 5);

# combine conditions with AND OR
SELECT * FROM employees WHERE years_in_company > 5 OR salary > 5000;
SELECT * FROM employees WHERE 
(years_in_company > 5 OR salary > 5000) AND gender = 'F';
```



**Sub queries**

Subqueries are commonly used to filter the results of one table based on the
results of a query on another table.

```mysql
SELECT * FROM employees WHERE id IN 
(SELECT mentor_id FROM mentorships WHERE project = 'SQF LIMITED');
```



**Sorting**

```mysql
# ascending
SELECT * FROM employees ORDER BY gender, em_name;
# descending
SELECT * FROM employees ORDER BY gender DESC, em_name;
```



**functions**

reference: [link](https://dev.mysql.com/doc/refman/8.0/en/built-in-function-reference.html)

```mysql
# string funcs
CONCAT('Hello', ' World')
SUBSTRING('Programming', 2, 6); # from 2, 6 letters -> rogram

# time funcs
NOW()
CURDATE() # 2018-08-28
CURTIME()

# aggregate funcs: performs calculation on a set of values and returns the result of the calculation as a single value.
COUNT()
SELECT COUNT(*) FROM employees; # null ignored
SELECT COUNT(DISTINCT gender) FROM employees; # rm duplicates

SELECT AVG(salary) FROM employees;
SELECT ROUND(AVG(salary), 2) FROM employees;

SELECT MAX(salary) FROM employees;
SELECT MIN(salary) FROM employees;
SELECT MAX(salary), MIN(years_in_company) FROM employees;

SELECT SUM(salary) FROM employees;

```



**Grouping data**

MySQL allows us to group data when performing calculations. For instance, we can ask MySQL to group the data based on gender and perform the calculation on the two groups separately

```mysql
SELECT gender, MAX(salary) FROM employees GROUP BY gender;

# Suppose we want to display rows from the previous table only when the maximum salary is above 10000
SELECT gender, MAX(salary) FROM employees GROUP BY gender HAVING
MAX(salary) > 10000;

```



**Joins: Select and combine data from one or more table**

Types of joins:

- JOIN / INNER JOIN: take values from both the tables, omit rows that don't match
- LEFT JOIN: take all rows from left table, if no value in right table, fill it with null
- RIGHT JOIN: take all rows from right table, if no value in left table, fill it with null



T1: Table one & table two

|  A   |  B   |      |  C   |   B   |  D   |
| :--: | :--: | ---- | :--: | :---: | :--: |
|  1   | 259  |      |  2   | 218.1 | ABC  |
|  2   | 125  |      |  3   | 511.5 | DEF  |
|  3   | 731  |      |  4   | 219.9 | GHI  |

```mysql
# syntax
SELECT [table_names.]columns_names_or_other_information
FROM
left_table
JOIN / INNER JOIN / LEFT JOIN / RIGHT JOIN
right_table
ON
left_table.column_name = right_table.column_name;

# JOIN/INNER JOIN (tables: T1)
# selects rows where a common value exists for both the tables
SELECT A, C, one.B AS 'one B', two.B AS 'two B'
FROM
one # left table
INNER JOIN
two # right table
ON
A = C;

# LEFT JOIN
# selects all rows from left table
SELECT A, C, one.B AS 'one B', two.B AS 'two B'
FROM
one # left table
LEFT JOIN
two # right table
ON
A = C;

# RIGHT JOIN
# selects all rows from right table
SELECT A, C, one.B AS 'one B', two.B AS 'two B'
FROM
one # left table
RIGHT JOIN
two # right table
ON
A = C;
```



**Union & Union All**

The UNION keyword is used to combine the results of two or more SELECT statements. Each SELECT statement must have the same number of columns.

The column names from the first SELECT statement will be used as the column names for the results returned.

```mysql
# syntax
SELECT_statement_one
UNION
SELECT_statement_two;
```



### Views

An SQL view is a virtual table. In contrast to actual tables, views do not contain data. Instead, they contain SELECT statements. The data to display is retrieved using those SELECT statements.

```mysql
# syntax
CREATE VIEW name_of_view AS
SELECT statement;

# creating a view
CREATE VIEW myView AS
SELECT employees.id, mentorships.mentor_id, employees.em_name AS
'Mentor', mentorships.project AS 'Project Name'
FROM
mentorships
JOIN
employees
ON
employees.id = mentorships.mentor_id;

# using a view
SELECT * FROM myView;
SELECT mentor_id, `Project Name` FROM myView; # backticks imp.

# altering a view
ALTER VIEW name_of_view AS
SELECT statement;

# alter e.g.
ALTER VIEW myView AS
SELECT employees.id, mentorships.mentor_id, employees.em_name AS
'Mentor', mentorships.project AS 'Project' # change
FROM
mentorships
JOIN
employees
ON
employees.id = mentorships.mentor_id;

# deleting view
DROP VIEW [IF EXISTS] name_of_view; # syntax
DROP VIEW IF EXISTS myView;
```



### Triggers

A trigger is a series of actions that is activated when a defined event occurs for a specific table. This event can either be an INSERT , UPDATE or DELETE . Triggers can be invoked before or after the event.

> **Delimiter :** A delimiter is a character or sequence of characters that specifies the end of a SQL statement. previously, we have always used ; to specify the end of our SQL statements. We can change that if we want.

E.g : Suppose one of the employees has just resigned from the company and we want to delete this employee from the employees table. However, before we do that, we would like to transfer the data into another table called ex_employees as a form of back up. We can do this using a trigger.

```mysql
# SYNTAX
DELIMITER $$

CREATE TRIGGER name_of_trigger BEFORE/AFTER
UPDATE/DELETE/INSERT ON name_of_table FOR EACH ROW

BEGIN
	-- ACTIONS TO TAKE
END $$

DELIMITER ; # this changes dilimiter back to semi colon

# e.g.
DELIMITER $$

CREATE TRIGGER update_ex_employees BEFORE DELETE ON employees FOR EACH ROW
BEGIN	
	INSERT INTO ex_employees (em_id, em_name, gender) VALUES (OLD.id, OLD.em_name, OLD.gender);
END $$

DELIMITER ; 

# deleting a trigger
DROP TRIGGER IF EXISTS update_ex_employees;

```



important points:

- For triggers activated by a DELETE event, we use the OLD keyword to retrieve
  the deleted values (or values to be deleted).
- For triggers activated by an INSERT event, we use the NEW keyword to retrieve
  the inserted data (or data to be inserted).
- For triggers activated by an UPDATE event, we use the OLD keyword to retrieve
  the original data, and the NEW keyword to retrieve the updated data.



### Variables

```mysql
# declaring variables
SET @em_id = 1;
SET @em_name = 'Jamie';
SET @price = 12;
SET @price = @price + 3;
SET @result = SQRT(9);

# using variable
SELECT * FROM mentorships WHERE mentor_id = @em_id;
SELECT * FROM mentorships WHERE mentee_id = @em_id;
SELECT * FROM employees WHERE id = @em_id;
```



### Stored Routines

A stored routine is a set of SQL statements that are grouped, named and stored together in the server. Do not worry if this does not make much sense to you at the moment. We’ll discuss it in greater depth later.

There are two types of stored routines :

- stored procedures 

- stored functions.



**Stored Procedures**

```mysql
# syntax
DELIMITER $$
CREATE PROCEDURE name_of_procedure([parameters, if any])
BEGIN
-- SQL Statements
END $$
DELIMITER ;

# create procedure
DELIMITER $$
CREATE PROCEDURE select_info()
BEGIN
	SELECT * FROM employees;
	SELECT * FROM mentorships;
END $$
DELIMITER ;

# procedure with params
DELIMITER $$
CREATE PROCEDURE employee_info(IN p_em_id INT)
BEGIN
	SELECT * FROM mentorships WHERE mentor_id = p_em_id;
	SELECT * FROM mentorships WHERE mentee_id = p_em_id;
	SELECT * FROM employees WHERE id = p_em_id;
END $$
DELIMITER ;

# getting value from procedures using OUT
DELIMITER $$
CREATE PROCEDURE employee_name_gender(IN p_em_id INT, OUT p_name VARCHAR(255), OUT p_gender CHAR(1))
BEGIN
	SELECT em_name, gender INTO p_name, p_gender FROM employees WHERE id = p_em_id;
END $$
DELIMITER ;

# using INOUT
DELIMITER $$
CREATE PROCEDURE get_mentor(INOUT p_em_id INT, IN p_project VARCHAR(255))
BEGIN
	SELECT mentor_id INTO p_em_id FROM mentorships WHERE mentee_id = p_em_id AND project = p_project;
END $$
DELIMITER ;

# using procedure
CALL select_info();
CALL employee_info(1);

CALL employee_name_gender(1, @v_name, @v_gender); # no need to declare vars
SELECT * FROM employees WHERE gender = @v_gender; # using the vars

SET @v_id = 1;
CALL get_mentor(@v_id, 'Wayne Fibre');
SELECT @v_id;

# deleting procedure
DROP PROCEDURE IF EXISTS name_of_procedure;
```



**Stored Functions**

A stored function must return a value using the RETURN keyword.

Stored functions are executed using a SELECT statement while stored procedures are executed using the CALL keyword.

```mysql
# syntax
DELIMITER $$
CREATE FUNCTION function_name([parameters, if any]) RETURNS
data_type characteristics_of_function
BEGIN
-- Details of function
RETURN result;
END $$

# create a function
DELIMITER $$
CREATE FUNCTION calculateBonus(p_salary DOUBLE, p_multiple DOUBLE) RETURNS DOUBLE DETERMINISTIC
BEGIN
	DECLARE bonus DOUBLE(8, 2); # declare local var, can also give initial val
	SET bonus = p_salary*p_multiple; # set value of local var
	RETURN bonus; # return value
END $$
DELIMITER ;

# function with other determinants
CREATE FUNCTION demo_function(p_demo DOUBLE) RETURNS DOUBLE
DETERMINISTIC MODIFIES SQL DATA

# using a func
SELECT id, em_name, salary, calculateBonus(salary, 1.5) AS bonus
FROM employees;

# deleting func
DROP FUNCTION IF EXISTS calculateBonus
```



important points:

- IN, OUT, INOUT are three types of parameters. IN means input variables, and OUT means output variables
- we use the INTO keyword to store the results returned by the SQL statement into the OUT parameters.
- When we pass in variables that have not been declared previously, MySQL will declare the variables for us.
- p_em_id serves as both an IN and OUT parameter. Hence, we declare it as an INOUT parameter.
- Characteristrics of a function: 
  - DETERMINISTIC is a keyword that tells MySQL that the function will always return the same result given the same input parameters.
  - NOT DETERMINISTIC 
  - NO SQL indicates that the function does not contain SQL statements
  - READS SQL DATA indicates that the function will only read data from the database, but will not modify the data
  - MODIFIES SQL DATA (indicates that the function may modify the data in the database)
  - CONTAINS SQL (indicates that the function contains SQL instructions, but does not contain statements that read or write data).



### IF ELSEIF ELSE

```mysql
DELIMITER $$
CREATE FUNCTION if_demo_A(x INT) RETURNS VARCHAR(255)
DETERMINISTIC
BEGIN
    IF x > 0 THEN RETURN 'x is positive';
    	ELSEIF x = 0 THEN RETURN 'x is zero';
    	ELSE RETURN 'x is negative';
    END IF;
END $$
DELIMITER ;

DELIMITER $$
CREATE FUNCTION if_demo_B(x INT) RETURNS VARCHAR(255)
DETERMINISTIC
BEGIN
    IF x > 0 THEN RETURN 'x is positive';
    	ELSEIF x = 0 THEN RETURN 'x is zero';
    END IF;
END $$
DELIMITER ;
```



### CASE statements

```mysql
# syntax
CASE case_variable
    WHEN value_1 THEN do task A;
    WHEN value_2 THEN do task B;
    ...
    ELSE do task Z;
END CASE;

# syntax two 
CASE
    WHEN condition 1 is met THEN do task A;
    WHEN condition 2 is met THEN do task B;
    ...
    ELSE do task Z;
END CASE;

# e.g. 
DELIMITER $$
CREATE FUNCTION case_demo_A(x INT) RETURNS VARCHAR(255)
DETERMINISTIC
BEGIN
	CASE x
		WHEN 1 THEN RETURN 'x is 1';
		WHEN 2 THEN RETURN 'x is 2';
		ELSE RETURN 'x is neither 1 nor 2';
	END CASE;
END $$
DELIMITER ;

# e.g. 2
DELIMITER $$
CREATE FUNCTION case_demo_B(x INT) RETURNS VARCHAR(255)
DETERMINISTIC
BEGIN
    CASE
    	WHEN x > 0 THEN RETURN 'x is positive';
    	WHEN x = 0 THEN RETURN 'x is zero';
    	ELSE RETURN 'x is negative';
    END CASE;
END $$
DELIMITER ;
	
# e.g. 3
DELIMITER $$
CREATE FUNCTION case_demo_B(x INT) RETURNS VARCHAR(255)
DETERMINISTIC
BEGIN
    CASE
        WHEN x > 0 THEN RETURN 'x is positive';
        WHEN x = 0 THEN RETURN 'x is zero';
        ELSE RETURN 'x is negative';
    END CASE;
END $$
DELIMITER ;
```



### WHILE statement

```mysql
# syntax
[name of while statement : ] WHILE condition is true DO
	-- some tasks
END WHILE;

# e.g.
DELIMITER $$
CREATE FUNCTION while_demo(x INT, y INT) RETURNS VARCHAR(255)
DETERMINISTIC
BEGIN
    DECLARE z VARCHAR(255);
    SET z = '';
    while_example: WHILE x<y DO
    	SET x = x + 1;
   		SET z = concat(z, x);
    END WHILE;
    
    RETURN z;
END $$
DELIMITER ;
```



### Cursors 

A cursor is a mechanism that allows us to step through the rows returned by a SQL statement.

Cursors help us go through rows one by one.

After declaring the cursor, we also need to declare a handler that defines what the cursor should do when it reaches the last row of the results returned by the SELECT statement. Normally, we want the cursor to set a variable to a certain value. For instance, we may want it to set a variable called v_done to 1

Then open the cursor

Once the cursor is open, we can use a loop to step through the rows. Within the loop, we need to retrieve the row that the cursor is currently pointing at and store the data into variables. The syntax for retrieving the row is

After the row is retrieved, the cursor will automatically move to the next row. The LOOP statement will repeatedly fetch each row into the variables and process those variables until it reaches the end of the results.

```mysql
# declare cursor syntax
DECLARE cursor_name CURSOR FOR
    SELECT statement

# declare cursor handler
DECLARE CONTINUE HANDLER FOR NOT FOUND SET var_name = value;

# open cursor
OPEN cursor_name;

# retreive row which cursor is pointing at
FETCH cursor_name INTO variable_names;

# close cursor
CLOSE cursor_name;

# e.g.
DELIMITER $$
CREATE FUNCTION get_employees () RETURNS VARCHAR(255)
DETERMINISTIC
BEGIN
	DECLARE v_employees VARCHAR(255) DEFAULT '';
	DECLARE v_name VARCHAR(255);
	DECLARE v_gender CHAR(1);
	DECLARE v_done INT DEFAULT 0;

	DECLARE cur CURSOR FOR
		SELECT em_name, gender FROM employees;
		
	DECLARE CONTINUE HANDLER FOR NOT FOUND SET v_done = 1;
	
	OPEN cur;
	
	employees_loop: LOOP
        FETCH cur INTO v_name, v_gender;
        
        IF v_done = 1 THEN LEAVE employees_loop;
        ELSE SET v_employees = concat(v_employees, ', ', v_name, ': ', v_gender);
        END IF;
    END LOOP;
    
    RETURN substring(v_employees, 3);
	
END $$
DELIMITER ;

```





### Points

- A foreign key is a column (or a collection of columns) in one table that links
  to the primary key in another table.