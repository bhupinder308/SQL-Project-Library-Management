
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

