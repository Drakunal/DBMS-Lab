### **Lab 5: Enhanced University Database**



### **1. Creating the Tables**

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

#### **Programs Table**
```sql
CREATE TABLE Programs (
    ProgramID INT PRIMARY KEY,
    ProgramName VARCHAR(100),
    FeesPerYear DECIMAL(10, 2),
    DurationYears INT
);
```

---

### **2. Inserting Sample Data**

#### **Parents**
```sql
INSERT INTO Parents (ParentID, FirstName, LastName, ContactNumber, Email)
VALUES
(1, 'John', 'Doe', '9876543210', 'john.doe@example.com'),
(2, 'Jane', 'Smith', '8765432109', 'jane.smith@example.com'),
(3, 'Mike', 'Johnson', '7654321098', 'mike.johnson@example.com'),
(4, 'Lucy', 'Brown', '6543210987', 'lucy.brown@example.com'),
(5, 'Emily', 'Clark', '9123456780', 'emily.clark@example.com'),
(6, 'Robert', 'Lee', '9876012345', 'robert.lee@example.com');
```

#### **Programs**
```sql
INSERT INTO Programs (ProgramID, ProgramName, FeesPerYear, DurationYears)
VALUES
(1, 'B.Sc Computer Science', 50000.00, 3),
(2, 'M.Sc Computer Science', 75000.00, 2),
(3, 'B.A English Literature', 40000.00, 3),
(4, 'B.Sc Mathematics', 45000.00, 3),
(5, 'M.A History', 60000.00, 2);
```

#### **Students**
```sql
INSERT INTO Students (StudentID, FirstName, LastName, DateOfBirth, Gender, ProgramID, ParentID)
VALUES
(1, 'Alice', 'Johnson', '2003-05-12', 'Female', 1, 3),
(2, 'Bob', 'Smith', '2000-11-25', 'Male', 2, 2),
(3, 'Charlie', 'Brown', '2002-03-18', 'Male', 3, 4),
(4, 'Diana', 'Doe', '2001-07-09', 'Female', 4, 1),
(5, 'Evelyn', 'Clark', '2004-08-21', 'Female', 1, 5),
(6, 'Frank', 'Lee', '1999-12-01', 'Male', 5, 6);
```

---

### **3. Queries and Concepts**

#### **3.1 Wildcards**

**Query 1: Find parents with email addresses ending in "example.com".**
```sql
SELECT * 
FROM Parents
WHERE Email LIKE '%example.com';
```

**Query 2: Find students whose first name starts with 'A'.**
```sql
SELECT * 
FROM Students
WHERE FirstName LIKE 'A%';
```

**Query 3: Find programs where the program name contains "History".**
```sql
SELECT * 
FROM Programs
WHERE ProgramName LIKE '%History%';
```

---

#### **3.2 Joins**

**Query 4: List all students along with their parent’s contact information.**
```sql
SELECT 
    CONCAT(S.FirstName, ' ', S.LastName) AS StudentName,
    CONCAT(P.FirstName, ' ', P.LastName) AS ParentName,
    P.ContactNumber
FROM Students S
JOIN Parents P ON S.ParentID = P.ParentID;
```

**Query 5: Show all programs and the number of students enrolled in each.**
```sql
SELECT 
    P.ProgramName,
    COUNT(S.StudentID) AS NumberOfStudents
FROM Programs P
LEFT JOIN Students S ON P.ProgramID = S.ProgramID
GROUP BY P.ProgramName;
```

---

#### **3.3 Aggregate Functions**

**Query 6: Find the total revenue from fees for all programs.**
```sql
SELECT 
    SUM(FeesPerYear * DurationYears) AS TotalRevenue
FROM Programs;
```

**Query 7: Find the average age of students (assuming today’s date is '2024-12-05').**
```sql
SELECT 
    AVG(YEAR('2024-12-05') - YEAR(DateOfBirth)) AS AverageAge
FROM Students;
```

**Query 8: Print the current age of all students.**
```sql
SELECT 
    CONCAT(FirstName, ' ', LastName) AS StudentName,
    YEAR(CURDATE()) - YEAR(DateOfBirth) AS CurrentAge
FROM Students;
```

---

#### **3.4 GROUP BY and HAVING**

**Query 9: Find parents with more than one child enrolled.**
```sql
SELECT 
    CONCAT(P.FirstName, ' ', P.LastName) AS ParentName,
    COUNT(S.StudentID) AS NumberOfChildren
FROM Parents P
JOIN Students S ON P.ParentID = S.ParentID
GROUP BY P.ParentID
HAVING COUNT(S.StudentID) > 1;
```

**Query 10: List programs with fees greater than the average fee.**
```sql
SELECT 
    ProgramName,
    FeesPerYear
FROM Programs
WHERE FeesPerYear > (SELECT AVG(FeesPerYear) FROM Programs);
```

---

### **4. Practice Questions**

1. Write a query to list all students along with their program names.
2. Find programs where the duration is greater than 2 years or the fees exceed 60,000.
3. List parents whose contact number starts with '987'.
4. Show the number of male and female students enrolled in each program.
5. Display all students whose names contain the letter 'e' in either their first or last name.

