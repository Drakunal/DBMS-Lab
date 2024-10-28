---

### **Lab 2: Data Manipulation and Retrieval**

#### **Objective**
Learn to modify, retrieve, and organize data with SQL commands like `UPDATE`, `DELETE`, `ALTER`, `JOIN`, `GROUP BY`, and `ORDER BY` through hands-on examples.

#### **Note**
- Use the previous lab database for this

---

### **Database Schema Recap**

The schema includes:
- **Books**: Manages book details.
- **Members**: Contains member information.
- **BorrowingRecords**: Tracks books borrowed by members.

---

### **1. Table Alterations**

#### Example: Adding Constraints and Columns
Only two minimal alterations are performed here.

1. **Add a Check Constraint**: Ensures `AvailableCopies` is always greater than or equal to zero.
2. **Add a Column with Default Value**: Adds `Rating` to `Books` table with a default value.

```sql
-- Alter to add a constraint on AvailableCopies to ensure it is non-negative
ALTER TABLE Books
ADD CONSTRAINT chk_AvailableCopies CHECK (AvailableCopies >= 0);

-- Add a Rating column with a default value of 3.5
ALTER TABLE Books
ADD COLUMN Rating DECIMAL(3, 2) DEFAULT 3.5;
```

---

### **2. UPDATE Operations**

#### Example 1: Updating Specific Columns
Update specific columns based on conditions.

```sql
-- Increase available copies for a specific book
UPDATE Books
SET AvailableCopies = AvailableCopies + 5
WHERE BookID = 2;

-- Update membership type to 'Premium' for members who have borrowed more than 10 books
UPDATE Members
SET MembershipType = 'Premium'
WHERE MemberID IN (
    SELECT MemberID FROM BorrowingRecords GROUP BY MemberID HAVING COUNT(BookID) > 10
);
```

---

### **3. DELETE Operations**

#### Example 1: Removing Records with Conditions
Use `DELETE` to remove records based on criteria.

```sql
-- Delete borrowing records where books were borrowed over 5 years ago
DELETE FROM BorrowingRecords
WHERE BorrowDate < DATE_SUB(CURDATE(), INTERVAL 5 YEAR);

-- Delete books with zero available copies
DELETE FROM Books
WHERE AvailableCopies = 0;
```

---

### **4. JOIN Operations**

#### Example 1: INNER JOIN Between `Members` and `BorrowingRecords`

```sql
-- Show which books each member borrowed along with the date
SELECT Members.FirstName, Members.LastName, Books.Title, BorrowingRecords.BorrowDate
FROM Members
JOIN BorrowingRecords ON Members.MemberID = BorrowingRecords.MemberID
JOIN Books ON BorrowingRecords.BookID = Books.BookID;
```

#### Example 2: LEFT JOIN with `Members` and `BorrowingRecords`
Lists all members and their borrowing records, showing `NULL` if they haven’t borrowed any books.

```sql
-- Show all members, even if they haven’t borrowed any books
SELECT Members.FirstName, Members.LastName, BorrowingRecords.BookID, BorrowingRecords.BorrowDate
FROM Members
LEFT JOIN BorrowingRecords ON Members.MemberID = BorrowingRecords.MemberID;
```

#### Example 3: GROUP BY with JOIN for Summarized Data
Retrieve aggregated information, such as the total number of books borrowed by each member.

```sql
-- Show the total number of books borrowed by each member
SELECT Members.FirstName, Members.LastName, COUNT(BorrowingRecords.BookID) AS BooksBorrowed
FROM Members
JOIN BorrowingRecords ON Members.MemberID = BorrowingRecords.MemberID
GROUP BY Members.MemberID;
```

---

### **5. GROUP BY, ORDER BY, and LIMIT**

These clauses are used for organizing data, especially when combined with aggregate functions.

#### Example 1: Using GROUP BY and ORDER BY
Group books by `Genre` and order them by `PublishedYear` within each genre.

```sql
-- Count the number of books in each genre, ordered by publication year
SELECT Genre, COUNT(*) AS TotalBooks
FROM Books
GROUP BY Genre
ORDER BY PublishedYear DESC;
```

#### Example 2: Using LIMIT
Limit the results to show only a few rows.

```sql
-- Show the top 3 most borrowed books
SELECT Books.Title, COUNT(BorrowingRecords.BookID) AS BorrowCount
FROM Books
JOIN BorrowingRecords ON Books.BookID = BorrowingRecords.BookID
GROUP BY Books.Title
ORDER BY BorrowCount DESC
LIMIT 3;
```

#### Example 3: Combining GROUP BY, ORDER BY, and HAVING
Use `HAVING` with `GROUP BY` to filter groups, showing only genres with more than 3 books.

```sql
-- List genres with more than 3 books and order by average rating
SELECT Genre, AVG(Rating) AS AvgRating
FROM Books
GROUP BY Genre
HAVING COUNT(*) > 3
ORDER BY AvgRating DESC;
```

---

### **6. Practice Questions**

1. **Update** the `AvailableCopies` in `Books` to reduce by 1 for every borrowed book.
2. **Add** a `PhoneNumber` column in `Members` with a default value of ‘Unknown’.
3. **Delete** records of books that were borrowed more than 4 years ago.
4. Find the **top 5 members** with the most borrowed books, showing their names and borrow count.
5. List the **average rating of books** by genre in descending order.
6. **Display** all books borrowed in the last 6 months, with titles and borrow dates.
7. Find the **total number of books** in each genre where more than 10 copies are available.

---

by Kunal Dey
