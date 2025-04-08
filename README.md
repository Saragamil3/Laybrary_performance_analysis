# Library performane analysis
# Objectives 
- Create database using ms sql server
- Insert data from csv files into database
- Write SQL queries to analyze library performance
# database schema
 ![database schema ](https://github.com/Saragamil3/Laybrary_performance_analysis/blob/main/Screenshot%202025-04-08%20090728.png)
# create database 
- Created tables for branches, employees, members, books, issued status, and return status

```sql
CREATE DATABASE LIBRARY;

USE LIBRARY ;

CREATE TABLE dbo.members (
member_id VARCHAR(10) PRIMARY KEY, 
member_name VARCHAR(50) NOT NULL,
member_address  VARCHAR(50),
reg_date DATE 
);


CREATE TABLE dbo.books(
isbn VARCHAR(50) PRIMARY KEY, 
book_title VARCHAR(50) NOT NULL, 
category VARCHAR(20) ,
rental_price FLOAT ,
book_status VARCHAR(20),
author VARCHAR(50) ,
publisher VARCHAR(50)
);


CREATE TABLE dbo.branch(
branch_id VARCHAR(20) PRIMARY KEY,
manager_id VARCHAR(20),
branch_address VARCHAR(50),
contact_no VARCHAR(20)
);

CREATE TABLE dbo.employees(
emp_id VARCHAR(10) PRIMARY KEY, 
emp_name VARCHAR(50) NOT NULL ,
position VARCHAR(50),
salary DECIMAL(10,2),
branch_id VARCHAR(20) FOREIGN KEY REFERENCES branch(branch_id)
) ;

CREATE TABLE dbo.issued_status (
issued_id VARCHAR(10) PRIMARY KEY,
issued_member_id VARCHAR(10) FOREIGN KEY REFERENCES members(member_id),
issued_book_name VARCHAR(50),
issued_date DATE ,
issued_book_isbn VARCHAR(50) FOREIGN KEY REFERENCES books(isbn),
issued_emp_id    VARCHAR(10) FOREIGN KEY REFERENCES employees(emp_id)
);
```
- insert data from csv files into each table in the database
```sql
--insert data from csv files into each table in the database
BULK INSERT dbo.books
from 'D:\Ai\DA Projects\library project\books.csv'  
WITH (
    FORMAT = 'CSV',  -- Only available in SQL Server 2017+
    FIRSTROW = 2,  -- Skip header row
    FIELDTERMINATOR = ',',  -- CSV uses comma as delimiter
    ROWTERMINATOR = '\n',  -- New line for each record
    TABLOCK
);
```
#Write SQL queries to analyze library performance
- Retrieve All Books in a Specific Categories
```sql
select 
   category,
   book_title
from 
    books 
where 
    category in ('Children', 'Classic')
```
- Number of issued books for each category
```sql
select 
   b.category,
   count(distinct Iss.issued_book_isbn) Number_of_issued_books
from dbo.issued_status Iss 
right join dbo.books b on b.isbn=Iss.issued_book_isbn
group by 
   b.category
order by
   Number_of_issued_books desc ;
```
-Total Rental Income by Category for issued books
```sql
select 
   category,
   sum(rental_price) Total_Rental_Income
from books 
where isbn in ( 
                 select 
			       distinct issued_book_isbn 
				 from
				    issued_status )
group by
    category
order by 
    Total_Rental_Income desc ; 
```
- List of issued and non issued books
```sql
-- issued books
select 
    b.book_title
from dbo.issued_status Iss  join dbo.books b
on b.isbn = Iss.issued_book_isbn 

-- not issued books
select 
    b.book_title
from books b
where b.isbn not in (select distinct issued_book_isbn from issued_status )
```
- List Employees with Their branch details
```sql
select 
   e.emp_name,
   b.branch_address,
   b.contact_no
from employees e  
join branch b on e.branch_id=b.branch_id ;
```
- Employees with the Most Book Issues Processed
```sql
select 
     top 5 e.emp_name,
	 count(distinct Iss.issued_book_isbn) Num_of_issued_books
from employees e  
join issued_status Iss on e.emp_id=Iss.issued_emp_id
group by  e.emp_name
order by Num_of_issued_books desc ;
```
