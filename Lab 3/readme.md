
### **Lab 3: Advanced SQL Operations**

This lab builds on the foundational SQL knowledge from Labs 1 and 2, focusing on more advanced operations to enhance data manipulation and analysis skills. The key topics covered are:

- **Joins (Advanced Examples)**
- **Self Joins**
- **Subqueries**
- **Set Operations**
- **Case Statements**
- **Nested Aggregations**
- **Sorting with `ORDER BY` and Filtering with `LIMIT`**

---

### **Code**

---

#### **1. Advanced Joins**

###### **Example 1: Finding Members and Their Borrowed Books**  
Get details of members and the books they borrowed:  
```sql
SELECT M.FirstName, B.Title, BR.BorrowDate
FROM Members M
JOIN BorrowingRecords BR ON M.MemberID = BR.MemberID
JOIN Books B ON BR.BookID = B.BookID;
```

###### **Example 2: Inner Join to Filter Only Borrowed Books**  
List books that have been borrowed at least once:  
```sql
SELECT DISTINCT B.Title, B.AuthorName
FROM Books B
JOIN BorrowingRecords BR ON B.BookID = BR.BookID;
```

###### **Example 3: Left Join to Identify Non-Borrowed Books**  
Identify books in the library that have never been borrowed:  
```sql
SELECT B.Title, B.AuthorName
FROM Books B
LEFT JOIN BorrowingRecords BR ON B.BookID = BR.BookID
WHERE BR.BookID IS NULL;
```

---

#### **2. Self Joins**

###### **Example 1: Finding Members Who Borrowed the Same Book**  
Find pairs of members who borrowed the same book on the same date:  
```sql
SELECT A.MemberName AS Member1, B.MemberName AS Member2, A.BorrowDate, A.Title
FROM (
    SELECT M.MemberName, BR.BorrowDate, B.Title, BR.BookID
    FROM BorrowingRecords BR
    JOIN Members M ON BR.MemberID = M.MemberID
    JOIN Books B ON BR.BookID = B.BookID
) A
JOIN (
    SELECT M.MemberName, BR.BorrowDate, B.Title, BR.BookID
    FROM BorrowingRecords BR
    JOIN Members M ON BR.MemberID = M.MemberID
    JOIN Books B ON BR.BookID = B.BookID
) B
ON A.BookID = B.BookID AND A.BorrowDate = B.BorrowDate AND A.MemberName != B.MemberName;
```

---

#### **3. Subqueries**

###### **Example 1: Finding the Most Borrowed Book**  
Find the book with the highest borrow count:  
```sql
SELECT Title
FROM Books
WHERE BookID = (
    SELECT BookID
    FROM BorrowingRecords
    GROUP BY BookID
    ORDER BY COUNT(*) DESC
    LIMIT 1
);
```

###### **Example 2: List Members Who Borrowed More Than 5 Books**  
Find members who borrowed more than 5 books:  
```sql
SELECT MemberName
FROM Members
WHERE MemberID IN (
    SELECT MemberID
    FROM BorrowingRecords
    GROUP BY MemberID
    HAVING COUNT(BookID) > 5
);
```

---

#### **4. Set Operations**

###### **Example 1: Combining Borrow Data Across Periods**  
List all unique members who borrowed books in January or February:  
```sql
SELECT MemberID
FROM BorrowingRecords
WHERE BorrowDate BETWEEN '2024-01-01' AND '2024-01-31'
UNION
SELECT MemberID
FROM BorrowingRecords
WHERE BorrowDate BETWEEN '2024-02-01' AND '2024-02-28';
```

###### **Example 2: Members Borrowing in Both Periods**  
Find members who borrowed books in both January and February:  
```sql
SELECT MemberID
FROM BorrowingRecords
WHERE BorrowDate BETWEEN '2024-01-01' AND '2024-01-31'
INTERSECT
SELECT MemberID
FROM BorrowingRecords
WHERE BorrowDate BETWEEN '2024-02-01' AND '2024-02-28';
```

---

#### **5. Case Statements**

###### **Example 1: Categorizing Borrow Frequency**  
Categorize members based on the number of books they borrowed:  
```sql
SELECT M.MemberName,
       CASE 
           WHEN COUNT(BR.BookID) > 10 THEN 'Frequent Borrower'
           WHEN COUNT(BR.BookID) BETWEEN 5 AND 10 THEN 'Moderate Borrower'
           ELSE 'Occasional Borrower'
       END AS BorrowCategory
FROM Members M
LEFT JOIN BorrowingRecords BR ON M.MemberID = BR.MemberID
GROUP BY M.MemberName;
```

---

#### **6. Nested Aggregations**

###### **Example 1: Average Borrow Count per Campus**  
Find the average number of books borrowed by members grouped by campus:  
```sql
SELECT M.Campus, AVG(BorrowCounts.TotalBorrowed) AS AvgBorrowed
FROM Members M
JOIN (
    SELECT MemberID, COUNT(*) AS TotalBorrowed
    FROM BorrowingRecords
    GROUP BY MemberID
) BorrowCounts ON M.MemberID = BorrowCounts.MemberID
GROUP BY M.Campus;
```

---

#### **7. Sorting with ORDER BY and Filtering with LIMIT**

###### **Example 1: Top 5 Most Borrowed Books**  
Find the top 5 most borrowed books:  
```sql
SELECT B.Title, COUNT(BR.BookID) AS BorrowCount
FROM Books B
JOIN BorrowingRecords BR ON B.BookID = BR.BookID
GROUP BY B.Title
ORDER BY BorrowCount DESC
LIMIT 5;
```

---

### **Practice Questions**

1. List all members and the books they borrowed, including members who haven’t borrowed anything.  
2. Identify books that have never been borrowed.  
3. Find the top 3 most borrowed books.  
4. Determine the average borrow count for each genre.  
5. List members who borrowed books from both January and February.  
6. Create a column categorizing books as "Bestseller" if borrowed more than 50 times and "Regular" otherwise.
