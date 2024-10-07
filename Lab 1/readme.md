---

# Lab 1: Library Management System using SQL

### Objective
This lab introduces the basics of SQL, including creating a database, tables with primary and foreign keys, inserting data, and running `SELECT` queries with and without the `WHERE` clause. We will use a **Library Management System** as the use case to apply these SQL operations.

---

### 1. **Create Database**

```sql
CREATE DATABASE LibraryManagement;
USE LibraryManagement;
```

This will create a new database named `LibraryManagement` and switch to it for further operations.

---

### 2. **Create Tables**

#### a. **Books Table**:
Stores information about the books in the library.

```sql
CREATE TABLE Books (
    BookID INT PRIMARY KEY,
    Title VARCHAR(100),
    Author VARCHAR(100),
    Genre VARCHAR(50),
    PublishedYear INT,
    AvailableCopies INT
);
```

#### b. **Members Table**:
Stores information about the members of the library.

```sql
CREATE TABLE Members (
    MemberID INT PRIMARY KEY,
    FirstName VARCHAR(50),
    LastName VARCHAR(50),
    JoinDate DATE,
    MembershipType ENUM('Regular', 'Premium')
);
```

#### c. **BorrowingRecords Table**:
Stores the borrowing history of the members, including the books they borrowed. This table references `Books` and `Members` through foreign keys.

```sql
CREATE TABLE BorrowingRecords (
    BorrowID INT PRIMARY KEY,
    MemberID INT,
    BookID INT,
    BorrowDate DATE,
    ReturnDate DATE,
    FOREIGN KEY (MemberID) REFERENCES Members(MemberID),
    FOREIGN KEY (BookID) REFERENCES Books(BookID)
);
```

---

### 3. **Insert Data into Tables**

#### a. **Insert Records into Books**:
```sql
INSERT INTO Books (BookID, Title, Author, Genre, PublishedYear, AvailableCopies)
VALUES 
(1, '1984', 'George Orwell', 'Dystopian', 1949, 5),
(2, 'To Kill a Mockingbird', 'Harper Lee', 'Classic', 1960, 2),
(3, 'The Great Gatsby', 'F. Scott Fitzgerald', 'Classic', 1925, 4),
(4, 'Brave New World', 'Aldous Huxley', 'Science Fiction', 1932, 3);
```

#### b. **Insert Records into Members**:
```sql
INSERT INTO Members (MemberID, FirstName, LastName, JoinDate, MembershipType)
VALUES 
(101, 'John', 'Doe', '2022-05-10', 'Regular'),
(102, 'Jane', 'Smith', '2021-07-20', 'Premium'),
(103, 'Alice', 'Johnson', '2023-02-12', 'Regular');
```

#### c. **Insert Records into BorrowingRecords**:
```sql
INSERT INTO BorrowingRecords (BorrowID, MemberID, BookID, BorrowDate, ReturnDate)
VALUES 
(1, 101, 1, '2023-09-01', NULL),  -- John Doe borrowed '1984'
(2, 102, 3, '2023-09-10', '2023-09-20');  -- Jane Smith borrowed 'The Great Gatsby'
```

---

### 4. **Run SELECT Queries**

#### a. **Select All Records from Books**:
```sql
SELECT * FROM Books;
```

This will retrieve all records from the `Books` table.

#### b. **Select Specific Records with WHERE Clause**:
- Retrieve books written by **George Orwell**:
  ```sql
  SELECT * FROM Books WHERE Author = 'George Orwell';
  ```

- Find books published before **1950**:
  ```sql
  SELECT * FROM Books WHERE PublishedYear < 1950;
  ```

- Retrieve members who joined after **2021**:
  ```sql
  SELECT * FROM Members WHERE JoinDate > '2021-12-31';
  ```

#### c. **Select Using Logical Operators**:
- Find all books in the **Classic** genre and published after **1930**:
  ```sql
  SELECT * FROM Books WHERE Genre = 'Classic' AND PublishedYear > 1930;
  ```

- Fetch books with less than **3 available copies**:
  ```sql
  SELECT Title, AvailableCopies FROM Books WHERE AvailableCopies < 3;
  ```

---

### 5. **Practice Exercises**
1. Add more records to the `Books` and `Members` tables.
2. Write queries to:
   - Retrieve books based on different criteria.
   - Fetch members who joined in a specific year.
   - Find books of a particular genre that have more than a certain number of available copies.

---

### Summary
In this lab, you have learned how to:
- Create a database and tables with primary and foreign keys.
- Insert records into the tables.
- Run `SELECT` queries with and without the `WHERE` clause to filter data based on different conditions.

This forms the foundation for more advanced SQL topics, which will be introduced in the following labs.

---
