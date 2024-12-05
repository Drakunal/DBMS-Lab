### **Lab 4: Wildcard Use Case**

Below is the **university database** lab updated to include more wildcard usage (like `_`, `%`, and combinations) along with other SQL functionalities.

---

### **1. Table Creation**

#### **Parents Table**
```sql
CREATE TABLE Parents (
    ParentID INT PRIMARY KEY,
    FirstName VARCHAR(50),
    LastName VARCHAR(50),
    ContactNumber VARCHAR(15),
    Email VARCHAR(100)
);
```

#### **Programs Table**
```sql
CREATE TABLE Programs (
    ProgramID INT PRIMARY KEY,
    ProgramName VARCHAR(100),
    DurationYears INT,
    FeesPerYear DECIMAL(10, 2)
);
```

#### **Students Table**
```sql
CREATE TABLE Students (
    StudentID INT PRIMARY KEY,
    FirstName VARCHAR(50),
    LastName VARCHAR(50),
    DateOfBirth DATE,
    Gender ENUM('Male', 'Female', 'Other'),
    ProgramID INT,
    ParentID INT,
    FOREIGN KEY (ProgramID) REFERENCES Programs(ProgramID),
    FOREIGN KEY (ParentID) REFERENCES Parents(ParentID)
);
```

---

### **2. Insertion Statements**

#### **Parents**
```sql
INSERT INTO Parents (ParentID, FirstName, LastName, ContactNumber, Email)
VALUES
(1, 'John', 'Doe', '9876543210', 'john.doe@example.com'),
(2, 'Jane', 'Smith', '8765432109', 'jane.smith@univ.com'),
(3, 'Michael', 'Brown', '7654321098', 'michael.brown@college.edu'),
(4, 'Sarah', 'Davis', '6543210987', 'sarah_davis@edu.com'),
(5, 'Emily', 'Clark', '5432109876', 'e.clark@example.net');
```

#### **Programs**
```sql
INSERT INTO Programs (ProgramID, ProgramName, DurationYears, FeesPerYear)
VALUES
(1, 'B.Sc. Computer Science', 3, 50000.00),
(2, 'M.Sc. Computer Science', 2, 75000.00),
(3, 'B.A. English', 3, 30000.00),
(4, 'M.A. English', 2, 50000.00),
(5, 'Ph.D. Mathematics', 5, 100000.00);
```

#### **Students**
```sql
INSERT INTO Students (StudentID, FirstName, LastName, DateOfBirth, Gender, ProgramID, ParentID)
VALUES
(1, 'Alice', 'Doe', '2000-05-15', 'Female', 1, 1),
(2, 'Bob', 'Smith', '1999-08-22', 'Male', 2, 2),
(3, 'Charlie', 'Brown', '2001-01-10', 'Other', 3, 3),
(4, 'Diana', 'Davis', '1998-12-05', 'Female', 4, 4),
(5, 'Ethan', 'Clark', '2002-06-25', 'Male', 5, 5);
```

---

### **3. Queries Using Wildcards**

#### **3.1 Basic Wildcards**

###### **Example 1: Emails containing 'edu' anywhere in the string**
```sql
SELECT *
FROM Parents
WHERE Email LIKE '%edu%';
```

###### **Example 2: Students whose first name starts with 'A' and ends with 'e'**
```sql
SELECT *
FROM Students
WHERE FirstName LIKE 'A%e';
```

###### **Example 3: Parent names where the second letter is 'a'**
```sql
SELECT *
FROM Parents
WHERE FirstName LIKE '_a%';
```

#### **3.2 Advanced Wildcards**

###### **Example 4: Emails that end in `.com` but have exactly two characters before the domain**
```sql
SELECT *
FROM Parents
WHERE Email LIKE '__%.com';
```

###### **Example 5: Programs where the word 'Science' appears exactly once**
```sql
SELECT *
FROM Programs
WHERE ProgramName LIKE '%Science%' AND ProgramName NOT LIKE '%Science%Science%';
```

###### **Example 6: Students whose last names have exactly five characters**
```sql
SELECT *
FROM Students
WHERE LastName LIKE '_____';
```

---

### **4. Queries Using Joins**

#### **4.1 Join Parents and Students**
```sql
SELECT 
    S.StudentID,
    CONCAT(S.FirstName, ' ', S.LastName) AS StudentName,
    CONCAT(P.FirstName, ' ', P.LastName) AS ParentName,
    P.ContactNumber
FROM Students S
JOIN Parents P ON S.ParentID = P.ParentID;
```

#### **4.2 Join Students and Programs**
```sql
SELECT 
    CONCAT(S.FirstName, ' ', S.LastName) AS StudentName,
    P.ProgramName,
    P.DurationYears
FROM Students S
JOIN Programs P ON S.ProgramID = P.ProgramID;
```

---

### **5. Queries Using Aggregate Functions**

#### **5.1 Find Total Students in Each Program**
```sql
SELECT 
    P.ProgramName,
    COUNT(S.StudentID) AS TotalStudents
FROM Programs P
LEFT JOIN Students S ON P.ProgramID = S.ProgramID
GROUP BY P.ProgramName;
```

#### **5.2 Find the Program with the Highest Fees**
```sql
SELECT 
    ProgramName,
    MAX(FeesPerYear) AS HighestFee
FROM Programs;
```

#### **5.3 Calculate Average Fee Across All Programs**
```sql
SELECT 
    AVG(FeesPerYear) AS AverageFee
FROM Programs;
```

#### **5.4 Find Parent with the Most Students Enrolled**
```sql
SELECT 
    CONCAT(P.FirstName, ' ', P.LastName) AS ParentName,
    COUNT(S.StudentID) AS StudentCount
FROM Parents P
JOIN Students S ON P.ParentID = S.ParentID
GROUP BY P.ParentID
ORDER BY StudentCount DESC
LIMIT 1;
```

---

### **6. Practice Questions**

1. Find all parents whose contact number ends with `876`.
2. List all programs where the duration is less than three years.
3. Retrieve details of students enrolled in "M.Sc. Computer Science."
4. Identify parents with multiple children in the university.
5. Display students whose names contain exactly two vowels.
6. Calculate the total fees collected from each program.

---
