# Library performane analysis
# Objectives 
- Create database
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
