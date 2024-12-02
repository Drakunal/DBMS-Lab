### **Lab 3: Advanced SQL Operations**

This lab builds on foundational SQL knowledge and introduces advanced SQL operations.  

#### Key Topics:
1. **Joins (Advanced Examples)**  
2. **Self Joins**  
3. **Subqueries**  
4. **Set Operations**  
5. **Case Statements (Advanced SQL)**  
6. **Nested Aggregations**  
7. **Sorting with `ORDER BY` and Filtering with `LIMIT`**  

---

### **Code Examples**

---

#### **1. Advanced Joins**

###### **Example 1: Finding Members and Their Borrowed Books**
Get details of members and the books they borrowed:
```sql
SELECT 
    CONCAT(M.FirstName, ' ', M.LastName) AS MemberName, 
    B.Title, 
    R.BorrowDate
FROM Members M
JOIN BorrowingRecords R ON M.MemberID = R.MemberID
JOIN Books B ON R.BookID = B.BookID;
```

###### **Example 2: Inner Join to Filter Only Borrowed Books**
List books that have been borrowed at least once:
```sql
SELECT DISTINCT B.Title, B.Author
FROM Books B
JOIN BorrowingRecords R ON B.BookID = R.BookID;
```

###### **Example 3: Left Join to Identify Non-Borrowed Books**
Identify books in the library that have never been borrowed:
```sql
SELECT B.Title, B.Author
FROM Books B
LEFT JOIN BorrowingRecords R ON B.BookID = R.BookID
WHERE R.BookID IS NULL;
```

---

#### **2. Self Joins**

###### **Example: Members Who Borrowed the Same Book**
Find pairs of members who borrowed the same book on the same date:
```sql
SELECT 
    CONCAT(M1.FirstName, ' ', M1.LastName) AS Member1, 
    CONCAT(M2.FirstName, ' ', M2.LastName) AS Member2, 
    R1.BorrowDate, 
    B.Title
FROM BorrowingRecords R1
JOIN BorrowingRecords R2 ON R1.BookID = R2.BookID AND R1.BorrowDate = R2.BorrowDate
JOIN Members M1 ON R1.MemberID = M1.MemberID
JOIN Members M2 ON R2.MemberID = M2.MemberID
JOIN Books B ON R1.BookID = B.BookID
WHERE R1.MemberID != R2.MemberID;
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

###### **Example 2: Members Who Borrowed More Than 5 Books**
Find members who borrowed more than 5 books:
```sql
SELECT CONCAT(FirstName, ' ', LastName) AS MemberName
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

###### **Example: Categorizing Borrow Frequency**
Categorize members based on the number of books they borrowed:
This query classifies library members into **Frequent Borrower**, **Moderate Borrower**, or **Occasional Borrower** based on the number of books they borrowed.

1. **`CONCAT(M.FirstName, ' ', M.LastName)`**: Combines the first and last names of members into `MemberName`.

2. **`CASE` Statement**: 
   - Counts how many books each member borrowed (`COUNT(R.BookID)`):
     - More than 10 → **Frequent Borrower**.
     - Between 5-10 → **Moderate Borrower**.
     - Less than 5 → **Occasional Borrower**.

3. **`LEFT JOIN`**: Ensures even members with no borrowing records are included (count as 0).

4. **`GROUP BY M.MemberID`**: Groups data by each member to calculate their borrowing count.

### Example:
- If "Alice" borrowed 7 books → **Moderate Borrower**.
- If "John" borrowed 12 books → **Frequent Borrower**.
- If "Bob" borrowed none → **Occasional Borrower**. 

Output: Member names with their borrowing category.
```sql
SELECT 
    CONCAT(M.FirstName, ' ', M.LastName) AS MemberName,
    CASE 
        WHEN COUNT(R.BookID) > 10 THEN 'Frequent Borrower'
        WHEN COUNT(R.BookID) BETWEEN 5 AND 10 THEN 'Moderate Borrower'
        ELSE 'Occasional Borrower'
    END AS BorrowCategory
FROM Members M
LEFT JOIN BorrowingRecords R ON M.MemberID = R.MemberID
GROUP BY M.MemberID;
```

---

#### **6. Nested Aggregations**

###### **Example: Average Borrow Count by Membership Type**
Find the average number of books borrowed by members grouped by membership type:
```sql
SELECT 
    M.MembershipType, 
    AVG(BorrowCounts.TotalBorrowed) AS AvgBorrowed
FROM Members M
JOIN (
    SELECT MemberID, COUNT(*) AS TotalBorrowed
    FROM BorrowingRecords
    GROUP BY MemberID
) BorrowCounts ON M.MemberID = BorrowCounts.MemberID
GROUP BY M.MembershipType;
```

---

#### **7. Sorting with ORDER BY and Filtering with LIMIT**

###### **Example: Top 5 Most Borrowed Books**
Find the top 5 most borrowed books:
```sql
SELECT B.Title, COUNT(R.BookID) AS BorrowCount
FROM Books B
JOIN BorrowingRecords R ON B.BookID = R.BookID
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
