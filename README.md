# Library Management System using SQL Project --P2

## Project Overview

**Project Title**: Library Management System  
**Level**: Intermediate  
**Database**: `library_project_2`

This project demonstrates the implementation of a Library Management System using SQL. It includes creating and managing tables, performing CRUD operations, and executing advanced SQL queries. The goal is to showcase skills in database design, manipulation, and querying.


## Objectives

1. **Set up the Library Management System Database**: Create and populate the database with tables for branch, employees, members, books, issued status, and return status.
2. **CRUD Operations**: Perform Create, Read, Update, and Delete operations on the data.
3. **CTAS (Create Table As Select)**: Utilize CTAS to create new tables based on query results.
4. **Advanced SQL Queries**: Develop complex queries to analyze and retrieve specific data.

## Project Structure

## 1. Database Setup:

- **Database Creation**: Created a database named `library_project_2`.
- **Table Creation**: Created tables for branch_tb, emp_tb, member_tb, book_tb, issued_tb, and return_tb. Each table includes relevant columns and relationships.

```sql
CREATE DATABASE library_project_2;
USE library_project_2;

```

## 2. Create Tables:
```sql
-- Create Table "Branch":

CREATE TABLE branch_tb (
	branch_id VARCHAR(5) PRIMARY KEY,
	manager_id VARCHAR(5),
	branch_address VARCHAR(15),
	contact_no VARCHAR(15)
);

-- Create Table "Employees":

CREATE TABLE emp_tb (
	emp_id VARCHAR(5) PRIMARY KEY,
	emp_name VARCHAR(20),
	position VARCHAR(10),
	salary INT,
	branch_id VARCHAR(5) -- FK
);

-- Create Table "Books":

CREATE TABLE book_tb (
	isbn VARCHAR(20) PRIMARY KEY,
	book_title VARCHAR(55),
	category VARCHAR(20),
	rental_price FLOAT,
	status VARCHAR(5),
	author VARCHAR(25),
	publisher VARCHAR(26)
);

-- Create Table "Members":

CREATE TABLE member_tb (
	member_id VARCHAR(5) PRIMARY KEY,
	member_name VARCHAR(15),
	member_address VARCHAR(15),
	reg_date DATE
);

-- Create Table "Issued_status":

CREATE TABLE issued_tb (
	issued_id VARCHAR(6) PRIMARY KEY,
	issued_member_id VARCHAR(6), -- FK
	issued_book_name VARCHAR(55), 
	issued_date DATE,
	issued_book_isbn VARCHAR(20),  -- FK
	issued_emp_id VARCHAR(6)   -- FK
);

-- Create Table "Return_status":

CREATE TABLE return_tb (
	return_id VARCHAR(6) PRIMARY KEY,
	issued_id VARCHAR(6),  -- FK
	return_book_name VARCHAR(80),
	return_date DATE,
	return_book_isbn VARCHAR(20) -- FK
);

```
### 2.Adding FOREIGN KEY:

```sql

-- FOREIGN KEY ADDING in issued_tb:

ALTER TABLE issued_tb
ADD CONSTRAINT fk_members
FOREIGN KEY (issued_member_id)
REFERENCES member_tb(member_id),

ADD CONSTRAINT fk_books
FOREIGN KEY (issued_book_isbn)
REFERENCES book_tb(isbn),

ADD CONSTRAINT fk_emps
FOREIGN KEY (issued_emp_id)
REFERENCES emp_tb(emp_id);

-- FOREIGN KEY ADDING in return_tb:

ALTER TABLE return_tb
ADD CONSTRAINT fk_issued_status
FOREIGN KEY (issued_id)
REFERENCES issued_tb(issued_id);

-- FOREIGN KEY ADDING in emp_tb:

ALTER TABLE emp_tb
ADD CONSTRAINT fk_branch_id
FOREIGN KEY (branch_id)
REFERENCES branch_tb(branch_id);

```

### 3. CRUD Operations:

- **Create**: Inserted sample records into the `books` table.
- **Read**: Retrieved and displayed data from various tables.
- **Update**: Updated records in the `employees` table.
- **Delete**: Removed records from the `members` table as needed.

**Task 1. Create a New Book Record**
-- "978-1-60129-456-2', 'To Kill a Mockingbird', 'Classic', 6.00, 'yes', 'Harper Lee', 'J.B. Lippincott & Co.')"

```sql
SELECT * FROM book_tb;

INSERT INTO book_tb (isbn, book_title, category, rental_price, status, author, publisher)
VALUES
('978-1-60129-456-2', 'To Kill a Mockingbird', 'Classic', 6.00, 'yes', 'Harper Lee', 'J.B. Lippincott & Co.');

SELECT * FROM book_tb;
```
**Task 2: Update an Existing Member's Address**

```sql
SELECT * FROM member_tb;

UPDATE member_tb
SET member_address = '125 Main St'
WHERE member_id = 'C101';

SELECT * FROM member_tb;
```

**Task 3: Delete a Record from the Issued Status Table**
-- Objective: Delete the record with issued_id = 'IS121' from the issued_status table.

```sql
SELECT * FROM issued_tb;

DELETE FROM issued_tb
WHERE issued_id = 'IS121';

SELECT * FROM issued_tb;
```

**Task 4: Retrieve All Books Issued by a Specific Employee**
-- Objective: Select all books issued by the employee with emp_id = 'E101'.

```sql
SELECT * FROM issued_tb;

SELECT * FROM issued_tb
WHERE issued_emp_id = 'E101';
```


**Task 5: List Members Who Have Issued More Than One Book**
-- Objective: Use GROUP BY to find members who have issued more than one book.

```sql
SELECT * FROM issued_tb;

SELECT 
issued_member_id,
count(*) AS No_of_book_issued 
FROM issued_tb
GROUP BY issued_member_id
HAVING No_of_book_issued  > 1;
```

### 4. CTAS (Create Table As Select):

**Task 6: Create Summary Tables**: Used CTAS to generate new tables based on query results - each book and total book_issued_cnt**

```sql
SELECT * FROM book_tb;
SELECT * FROM issued_tb;

CREATE TABLE book_issued_cnt AS
SELECT 
	b.isbn,
	b.book_title,
	COUNT(i.issued_id) AS Total_issued_count
FROM book_tb AS b
JOIN issued_tb AS i
ON b.isbn = i.issued_book_isbn
GROUP BY b.isbn,b.book_title;

SELECT * FROM  book_issued_cnt;
```

### 5. Data Analysis & Findings:

The following SQL queries were used to address specific questions:

**Task 7: Retrieve All Books in a Specific Category**:

```sql
SELECT * FROM book_tb
WHERE category = 'History';
```

**Task 8: Find Total Rental Income by Category**:

```sql
SELECT * FROM book_tb;

SELECT 
	b.category,
    SUM(b.rental_price) AS Total_Rental_Income,
    COUNT(*) AS No_of_Time_book_issued
FROM book_tb AS b
JOIN issued_tb AS i
ON b.isbn = i.issued_book_isbn
GROUP BY b.category;
```

**Task 9: List Members Who Registered in the Last 180 Days**:

```sql
SELECT * FROM member_tb;

SELECT * FROM member_tb
WHERE reg_date >= CURDATE() - INTERVAL 180 DAY;
```

**Task 10: List Employees with Their Branch Manager's Name and their branch details**:

```sql
SELECT * FROM branch_tb;
SELECT * FROM emp_tb;

SELECT 
	e.emp_id,
    e.emp_name,
    b.manager_id,
    e1.emp_name AS manager_name,
    e1.position
FROM emp_tb AS e
JOIN branch_tb AS b
ON e.branch_id = b.branch_id
JOIN emp_tb AS e1
ON e1.emp_id = b.manager_id;
```

**Task 11:Create a table of books where the Rental Price is greater than 6**:

```sql
SELECT * FROM book_tb;

CREATE TABLE High_rental_price_books
SELECT 
	* 
FROM book_tb
WHERE rental_price > 6
ORDER BY rental_price ASC;

SELECT * FROM High_rental_price_books;
```

**Task 12: Retrieve the List of Books Not Yet Returned**:

```sql
SELECT * FROM issued_tb;
SELECT * FROM return_tb;

SELECT 
	iss.issued_id,
    iss.issued_book_name,
    iss.issued_date,
    re.return_date
FROM issued_tb AS iss
LEFT JOIN return_tb AS re
ON iss.issued_id = re.issued_id
WHERE re.return_id IS NULL;
```

## Advanced SQL Operations

**Task 13: Identify Members with Overdue Books**:
Write a query to identify members who have overdue books (assume a 30-day return period). Display the member's_id, member's name, book title, issue date, and days overdue.

```sql
SELECT * FROM issued_tb;
SELECT * FROM return_tb;
SELECT * FROM member_tb;

SELECT 
	ist.issued_member_id,
    m.member_name,
    ist.issued_book_name,
	ist.issued_date,
    re.return_date,
    CURDATE() - ist.issued_date AS Over_dues_days
FROM issued_tb AS ist
LEFT JOIN return_tb AS re
ON ist.issued_id = re.issued_id
JOIN member_tb AS m
ON m.member_id = ist.issued_member_id
WHERE re.return_date IS NULL
AND  CURDATE() - ist.issued_date >365
ORDER BY ist.issued_member_id;
```

**Task 14: Update Book Status on Return**  
Write a query to update the status of books in the books table to "Yes" when they are returned (based on entries in the return_status table).


```sql

-- STORE PROCEDURE

DELIMITER $$

CREATE PROCEDURE add_return_records(
    IN p_return_id VARCHAR(10),
    IN p_issued_id VARCHAR(10),
    IN p_book_quality VARCHAR(10)
)
BEGIN

    DECLARE v_isbn VARCHAR(50);
    DECLARE v_book_name VARCHAR(80);

    -- Insert return record
    INSERT INTO return_tb
    (
        return_id,
        issued_id,
        return_date,
        book_quality
    )
    VALUES
    (
        p_return_id,
        p_issued_id,
        CURDATE(),
        p_book_quality
    );

    -- Get ISBN and Book Name
    SELECT
        issued_book_isbn,
        issued_book_name
    INTO
        v_isbn,
        v_book_name
    FROM issued_tb
    WHERE issued_id = p_issued_id;

    -- Update book status
    UPDATE book_tb
    SET status = 'yes'
    WHERE isbn = v_isbn;

    -- Display message
    SELECT CONCAT('Thank you for returning the book: ', v_book_name) AS Message;

END$$

DELIMITER ;


-- Testing FUNCTION add_return_records

SELECT * FROM book_tb
WHERE isbn = '978-0-307-58837-1';

SELECT * FROM issued_tb
WHERE issued_book_isbn = '978-0-307-58837-1';

SELECT * FROM return_tb
WHERE issued_id = 'IS135';

-- calling function 
CALL add_return_records('RS138', 'IS135', 'Good');

-- calling function 
CALL add_return_records('RS148', 'IS140', 'Good');
```

**Task 15: Find Employees with the Most Book Issues Processed**  
Write a query to find the top 3 employees who have processed the most book issues. 
Display the employee name, number of books processed, and their branch.

```sql

SELECT * FROM issued_tb;
SELECT * FROM emp_tb;
SELECT * FROM branch_tb;

SELECT 
	e.emp_name,
    COUNT(iss.issued_id) AS No_of_book_processed,
    b.branch_id
FROM emp_tb as e
JOIN issued_tb AS iss
ON e.emp_id = iss.issued_emp_id
JOIN branch_tb AS b
ON e.branch_id = b.branch_id
GROUP BY e.emp_name, b.branch_id;
```

**Task 16: CTAS: Create a Table of Active Members**  
Use the CREATE TABLE AS (CTAS) statement to create a new table active_members containing members who have issued at least one book in the last 2 months.

```sql

CREATE TABLE active_members AS
SELECT *
FROM member_tb
WHERE member_id IN (
    SELECT DISTINCT issued_member_id
    FROM issued_tb
    WHERE issued_date >= CURRENT_DATE - INTERVAL 2 MONTH
);

SELECT * 
FROM active_members;

```

## Reports

- **Database Schema**: Detailed table structures and relationships.
- **Data Analysis**: Insights into book categories, employee salaries, member registration trends, and issued books.
- **Summary Reports**: Aggregated data on high-demand books and employee performance.

## Conclusion

This project demonstrates the application of SQL skills in creating and managing a library management system. It includes database setup, data manipulation, and advanced querying, providing a solid foundation for data management and analysis.


## Author - SANKARAGOPI B

Final Year Engineering Student | Aspiring Data Analyst

This project showcases SQL skills essential for database management and analysis.

- **LinkedIn**: [Connect with me professionally](https://linkedin.com/in/sankaragopi-b-9b45312b2)


Thank you for your interest in this project!
