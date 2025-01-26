
# Library Management System 📚

The Library Management System is a SQL-based project designed to streamline and manage library operations efficiently. The system organizes and tracks essential data related to library branches, books, employees, book issuance, and returns.

## Key Features:-
- *Branch Management:* Information about library branches and their locations.
- *Book Catalog:* Details of books, including ISBN numbers, rental - amounts, and availability status.
- *Employee Records:* Information about library staff and their roles.
- *Issue Tracking:* Records of book issuances to members.
- *Return Monitoring:* Tracks the return status of issued books.

This project provides a structured database schema that ensures - seamless handling of library operations while maintaining data integrity.

![ERD](https://github.com/bhupinder308/SQL-Project-Library-Management/blob/main/ER%20Diagram.png)

##Schema Creation

Create Branch table
```sql
create table branch (   

	branch_id varchar(10)primary key,
	manager_id varchar(10),
	branch_address varchar(55),
	contact_no 	 varchar(55)
);
```

Create employee table
```sql
create table employees(
	emp_id	varchar(10) primary key,
	emp_name	varchar(25),
	position	varchar(12),
	salary	float,
	branch_id  varchar(10)
);
```

Create table books
```sql
create table books(
	isbn	varchar(20) primary key,
	book_title	varchar(60),
	category	varchar(20),
	rental_price float,	
	status	varchar(4),
	author	varchar(25),
	publisher varchar(30)
);
```


Create issued table
```sql
 create table issued_status(
	 issued_id	varchar(6) primary key,
	 issued_member_id	varchar(6),
	 issued_book_name	varchar(60),
	 issued_date	date,
	 issued_book_isbn	varchar(20),
	 issued_emp_id  varchar(6)

 );
```

Create table members
```sql
create table members(

	 member_id	varchar(6) primary key,
	 member_name	varchar(25),
	 member_address	varchar(55),
	 reg_date date

);
```


Create table return 
```sql
create table return_status(
	return_id	varchar(7) primary key,
	issued_id	varchar(7),
	return_book_name	varchar(70),
	return_date	date ,
	return_book_isbn varchar(27)

);
```

## Data Modeling

```sql
alter table issued_status add constraint issued_status_FK_books 
	foreign key(issued_book_isbn) references books(isbn) on delete cascade on update cascade;  

alter table issued_status add constraint issued_status_FK_members
foreign key (issued_member_id) references members(member_id) on delete cascade on update cascade;  


alter table issued_status add constraint issued_status_FK_employees
foreign key(issued_emp_id) references  employees(emp_id)  on delete cascade on update cascade;  

alter table employees add constraint employees_FK_branch
foreign key(branch_id)  references branch(branch_id)   on delete cascade on update cascade;   

alter table return_status add constraint return_status_FK_issued_status
foreign key(issued_id) references issued_status(issued_id) on delete cascade on update cascade;
```

Task 1. 
Create a New Book Record
"978-1-60129-456-2', 'To Kill a Mockingbird', 'Classic', 6.00, 'yes', 'Harper Lee', 'J.B. Lippincott & Co.'

```sql
nsert into books values('978-1-60129-456-2', 'To Kill a Mockingbird', 'Classic', 6.00, 'yes', 'Harper Lee', 'J.B. Lippincott & Co.');

select * from books;
```

Task 2 
Update an Existing Member's Address

```sql
select * from members;
-- Here we will update the address of member_id= "C101"( Name - "Alice Johnson" ) from "123 Main St" to "125 Main St"

update members set member_address='125 Main St' where member_id='C101'; -- Address updated to "125 Main St"
```

Task 3
Delete a Record from the Issued Status Table.
Objective: Delete the record with issued_id = 'IS121' from the issued_status table

```sql
delete from issued_status where issued_id = 'IS121';

select * from issued_status where issued_id = 'IS121';-- Record successfully deleted.
```

Task 4
Retrieve All Books Issued by a Specific Employee
Objective: Select all books issued by the employee with emp_id = 'E101'.

```sql
select issued_emp_id,issued_book_name from issued_status where issued_emp_id='E101';
-- Insight- Only 2 books were issued by 'E101', books name are "Moby Dick" and "To Kill a Mockingbird"
```

Task 5
List emp_id Who Have Issued More Than One Book
Objective: Use GROUP BY to find emp_id who have issued more than one book.

```sql
select issued_emp_id, count(*) as no_of_books_issued from issued_status 
	group by issued_emp_id having count(*)>1 order by count(*) desc;

-- Insight - There are 9 employees who have issued more then 1 book.
```


Task 6: Create Summary Tables**: Used CTAS to generate new tables based on query results - each book and total book_issued_cnt

```sql
create table book_cnts as 
select 
b.book_title,b.isbn, count(i.issued_date) as book_issued_count
from books b join 
issued_status i on b.isbn = i.issued_book_isbn group by b.book_title,b.isbn order by count(i.issued_date) desc;

select  * from book_cnts;  -- The table we have created via CTAS can be accessed. 
```

Task 7. Retrieve All Books in a Specific Category:
Objective:- Find the books from category " Classic"

```sql
select * from books where category='Classic';

-- Insight- There are 9 books in Classic category.
```

Task 8: Find Total Rental Income by Category:

```sql
select b.category,sum( b.rental_price) as total_rental
	from 
books b join issued_status i on b.isbn = i.issued_book_isbn group by b.category order by sum( b.rental_price) desc;

-- Insight:- Max rental income was generated from "Classic" category books
```

Task 9 
List Members Who Registered in the Last 180 Days

- *Considering todays date is August 23,2024*
*In this project instead of current date , we have used date as August 23,2024 as this project was completed 3 days of time interval and to get the same result we have used current date as  August 23,2024*

```sql
select * from members where reg_date> (to_date('2024-08-23','YYYY-MM-DD') - INTERVAL '6 month');
-- Insight- There are only 2 customers who registered in last 180 days(6 months)
```

Task 10: 
List Employees with their branch ID and  and their branch details

```sql 
select 
	e.emp_id,e.emp_name,b.branch_id,b.manager_id, b.branch_address, b.contact_no, e1.emp_name as manager_name, 
 e1.position
	from 
employees e join branch b on e.branch_id = b.branch_id
join employees e1 
on b.manager_id=e1.emp_id
```

Task 11
Create a Table of Books with Rental Price Above 7 USD

```sql

create table books_price_above_7 as 
select * from books where rental_price> 7;

-- Insight- There are only 7 books whose price is above 7 USD
```


Task 12: Retrieve the List of Books Not Yet Returned

```sql
select i.*, rs.return_id, rs.return_date from issued_status i
left join return_status rs on i.issued_id = rs.issued_id where rs.return_date is null;

-- Insight - There are 20 books that were issued yet not returned.
```

## Adding new records in the issued_status column 

```sql

INSERT INTO issued_status(issued_id, issued_member_id, issued_book_name, issued_date, issued_book_isbn, issued_emp_id)
VALUES
('IS151', 'C118', 'The Catcher in the Rye',( to_date('2024-08-23','YYYY-MM-DD') - INTERVAL '24 days'),  '978-0-553-29698-2', 'E108'),
('IS152', 'C119', 'The Catcher in the Rye', (to_date('2024-08-23','YYYY-MM-DD') - INTERVAL '13 days'),  '978-0-553-29698-2', 'E109'),
('IS153', 'C106', 'Pride and Prejudice', (to_date('2024-08-23','YYYY-MM-DD') - INTERVAL '7 days'),  '978-0-14-143951-8', 'E107'),
('IS154', 'C105', 'The Road', (to_date('2024-08-23','YYYY-MM-DD') - INTERVAL '32 days'),  '978-0-375-50167-0', 'E101');



select * from issued_status;
```

