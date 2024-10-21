---

# Lab 1: Library Management System using SQL

### Objective
This lab will cover the basics of SQL, focusing on creating tables with primary and foreign keys, inserting records into these tables, and performing simple `SELECT` queries with and without aggregate functions.

---

### 1. **Create Database**

```sql
CREATE DATABASE LibraryManagement;
USE LibraryManagement;
```

---

### 2. **Create Tables**

#### a. **Books Table**

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

#### b. **Members Table**

```sql
CREATE TABLE Members (
    MemberID INT PRIMARY KEY,
    FirstName VARCHAR(50),
    LastName VARCHAR(50),
    JoinDate DATE,
    MembershipType ENUM('Regular', 'Premium')
);
```

#### c. **BorrowingRecords Table**

```sql
CREATE TABLE BorrowingRecords (
    MemberID INT,
    BookID INT,
    BorrowDate DATE,
    ReturnDate DATE,
    NumberOfCopies INT,
    PRIMARY KEY (MemberID, BookID, BorrowDate),
    FOREIGN KEY (MemberID) REFERENCES Members(MemberID),
    FOREIGN KEY (BookID) REFERENCES Books(BookID)
);
```

---

### 3. **Insert Data into Tables**

#### a. **Insert Data into `Books`**

```sql
INSERT INTO Books (BookID, Title, Author, Genre, PublishedYear, AvailableCopies)
VALUES 
(1, '1984', 'George Orwell', 'Dystopian', 1949, 5),
(2, 'To Kill a Mockingbird', 'Harper Lee', 'Classic', 1960, 3),
(3, 'The Great Gatsby', 'F. Scott Fitzgerald', 'Classic', 1925, 4),
(4, 'Brave New World', 'Aldous Huxley', 'Science Fiction', 1932, 2),
(5, 'Moby Dick', 'Herman Melville', 'Adventure', 1851, 3),
(6, 'Pride and Prejudice', 'Jane Austen', 'Romance', 1813, 5),
(7, 'The Catcher in the Rye', 'J.D. Salinger', 'Fiction', 1951, 6),
(8, 'The Hobbit', 'J.R.R. Tolkien', 'Fantasy', 1937, 7),
(9, 'War and Peace', 'Leo Tolstoy', 'Historical', 1869, 4),
(10, 'Frankenstein', 'Mary Shelley', 'Horror', 1818, 2);
```

#### b. **Insert Data into `Members`**

```sql
INSERT INTO Members (MemberID, FirstName, LastName, JoinDate, MembershipType)
VALUES 
(101, 'John', 'Doe', '2022-01-10', 'Regular'),
(102, 'Jane', 'Smith', '2021-04-15', 'Premium'),
(103, 'Alice', 'Johnson', '2023-05-22', 'Regular'),
(104, 'Bob', 'Brown', '2022-12-01', 'Regular'),
(105, 'Carol', 'Wilson', '2021-06-17', 'Premium'),
(106, 'Dave', 'Clark', '2023-07-30', 'Regular'),
(107, 'Eve', 'Miller', '2022-03-21', 'Premium'),
(108, 'Frank', 'Davis', '2022-09-11', 'Regular'),
(109, 'Grace', 'Moore', '2023-02-28', 'Premium'),
(110, 'Hank', 'Taylor', '2021-11-05', 'Regular');
```

#### c. **Insert Data into `BorrowingRecords`**

```sql
INSERT INTO BorrowingRecords (MemberID, BookID, BorrowDate, ReturnDate, NumberOfCopies)
VALUES 
(101, 1, '2023-08-10', NULL, 1),  -- John Doe borrowed 1 copy of '1984'
(102, 3, '2023-08-12', '2023-09-01', 2),  -- Jane Smith borrowed 2 copies of 'The Great Gatsby'
(103, 4, '2023-09-05', NULL, 1),  -- Alice Johnson borrowed 1 copy of 'Brave New World'
(104, 5, '2023-09-07', NULL, 1),  -- Bob Brown borrowed 'Moby Dick'
(105, 2, '2023-09-15', NULL, 1);  -- Carol Wilson borrowed 'To Kill a Mockingbird'
```

---

### 4. **Run SELECT Queries**

#### a. **Retrieve All Books**:

```sql
SELECT * FROM Books;
```
---
#### b. **Retrieve Books Borrowed by a Specific Member**:

```sql
SELECT * FROM BorrowingRecords WHERE MemberID = 101;  -- John Doe's borrowing history
```
---
#### c. **Retrieve Members Who Borrowed More Than 1 Copy of a Book**:

```sql
SELECT * FROM BorrowingRecords WHERE NumberOfCopies > 1;
```
---
#### d. **Retrieve Books Borrowed After a Certain Date**:

```sql
SELECT * FROM BorrowingRecords WHERE BorrowDate > '2023-09-01';
```
---
#### e. **Count the Total Number of Books in the Library**:

```sql
SELECT COUNT(*) AS TotalBooks FROM Books;
```
---

#### f. **Find the Average Number of Available Copies per Book**:

```sql
SELECT AVG(AvailableCopies) AS AverageCopies FROM Books;
```
---

#### g. **Find the Maximum and Minimum Published Year of Books**:

```sql
SELECT MAX(PublishedYear) AS LatestPublication, MIN(PublishedYear) AS OldestPublication FROM Books;
```
---

#### h. **Count the Total Number of Premium Members**:

```sql
SELECT COUNT(*) AS TotalPremiumMembers FROM Members WHERE MembershipType = 'Premium';
```
---

#### i. **Sum of Copies Borrowed by Each Member**:

```sql
SELECT MemberID, SUM(NumberOfCopies) AS TotalBorrowed FROM BorrowingRecords
GROUP BY MemberID;
```
---

#### j. **Average Number of Books Borrowed per Transaction**:

```sql
SELECT AVG(NumberOfCopies) AS AverageBorrowedPerTransaction FROM BorrowingRecords;
```
---

### 5. **Practice Questions**

1. **Add More Data**:
   - Insert 2 more books into the `Books` table.
   - Insert 2 more members into the `Members` table.
   - Insert 2 more records into the `BorrowingRecords` table.

2. **Write Queries**:
   - Retrieve all the books written by **George Orwell**.
   - Find members who joined the library before **2022**.
   - Fetch books that have more than **4 available copies**.
   - Find records where members returned the books after borrowing them (i.e., `ReturnDate` is not `NULL`).

3. **Bonus Challenge**:
   - Write a query to count how many books each member has borrowed after a specific date.

---

### **Assumptions and Rules**

1. **Library Database Structure**:
   - We store information about **books**, **members**, and **borrowing records** in three tables: `Books`, `Members`, and `BorrowingRecords`.
   - The `BorrowingRecords` table captures borrowing transactions and links books to members via foreign keys (`BookID` and `MemberID`).

2. **Books Table**:
   - Each book is uniquely identified by `BookID` (Primary Key).
   - Fields include `Title`, `Author`, `Genre`, `PublishedYear`, and `AvailableCopies` (number of copies available in the library).

3. **Members Table**:
   - Each member is identified by a unique `MemberID` (Primary Key).
   - Fields include `FirstName`, `LastName`, `JoinDate`, and `MembershipType` (`ENUM` type, either 'Regular' or 'Premium').

4. **BorrowingRecords Table**:
   - Records each borrowing transaction, with `MemberID`, `BookID`, and `BorrowDate` forming a **composite primary key**.
   - `NumberOfCopies` indicates how many copies of a book a member borrowed.
   - The `ReturnDate` can be `NULL`, indicating that the book has not been returned yet.

5. **Primary and Foreign Keys**:
   - **Primary keys**: `BookID` (Books), `MemberID` (Members), `MemberID, BookID, BorrowDate` (BorrowingRecords).
   - **Foreign keys** ensure that each borrowing record is tied to an existing member and book.

6. **Updating Available Copies**:
   - When a member borrows a book, the `AvailableCopies` in the `Books` table is **not automatically updated**.
   - This can be handled using a **trigger** in future labs to decrease the number of available copies when a borrowing transaction is recorded.

7. **Basic Queries**:
   - Focuses on **basic CRUD operations** (Create, Read, Update, Delete) and **aggregate functions** (`COUNT()`, `SUM()`, `AVG()`).
   - Example queries include counting books, finding members who borrowed more than 1 copy, and calculating the average number of books borrowed per transaction.

8. **No Advanced SQL Concepts Yet**:
   - **Joins**, **triggers**, and **complex transactions** are not covered in this lab, as it’s an introductory exercise.
   - Advanced features will be introduced in future labs.
   
---
-by Kunal Dey
