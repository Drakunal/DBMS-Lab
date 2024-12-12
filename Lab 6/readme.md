### **Lab 6: Subqueries, SET Operations, and CASE**

In this lab, we will focus on **subqueries**, **SET operations** (`UNION`, `INTERSECT`, `EXCEPT`), and the **CASE** statement to handle conditional logic in SQL. These are essential tools for more complex and structured queries.

---

### **Table Definitions**
#### **1. Students Table**
```sql
CREATE TABLE Students (
    StudentID INT PRIMARY KEY,
    FirstName VARCHAR(50),
    LastName VARCHAR(50),
    DateOfBirth DATE,
    ProgramID INT,
    AdmissionYear INT,
    Gender ENUM('Male', 'Female', 'Other'),
    FOREIGN KEY (ProgramID) REFERENCES Programs(ProgramID)
);
```

#### **2. Parents Table**
```sql
CREATE TABLE Parents (
    ParentID INT PRIMARY KEY,
    StudentID INT,
    ParentName VARCHAR(100),
    ContactNumber VARCHAR(15),
    Email VARCHAR(100),
    Relationship ENUM('Father', 'Mother', 'Guardian'),
    FOREIGN KEY (StudentID) REFERENCES Students(StudentID)
);
```

#### **3. Programs Table**
```sql
CREATE TABLE Programs (
    ProgramID INT PRIMARY KEY,
    ProgramName VARCHAR(100),
    DurationYears INT,
    Department VARCHAR(50)
);
```

---

### **Data Insertion**
#### **Programs**
```sql
INSERT INTO Programs (ProgramID, ProgramName, DurationYears, Department)
VALUES
    (1, 'B.Sc. Computer Science', 3, 'Science'),
    (2, 'M.Sc. Computer Science', 2, 'Science'),
    (3, 'B.A. English', 3, 'Arts'),
    (4, 'M.A. History', 2, 'Humanities');
```

#### **Students**
```sql
INSERT INTO Students (StudentID, FirstName, LastName, DateOfBirth, ProgramID, AdmissionYear, Gender)
VALUES
    (1, 'John', 'Doe', '2003-04-10', 1, 2021, 'Male'),
    (2, 'Jane', 'Smith', '2002-08-15', 2, 2020, 'Female'),
    (3, 'Alice', 'Brown', '2004-01-22', 1, 2022, 'Female'),
    (4, 'Robert', 'Johnson', '2001-07-09', 3, 2019, 'Male'),
    (5, 'Emily', 'Davis', '2005-03-18', 4, 2023, 'Female'),
    (6, 'Michael', 'Clark', '2003-12-30', 1, 2021, 'Male'),
    (7, 'Sophia', 'Walker', '2004-09-14', 2, 2022, 'Female'),
    (8, 'Ethan', 'Harris', '2001-11-25', 3, 2020, 'Male'),
    (9, 'Chloe', 'White', '2002-06-05', 4, 2019, 'Female'),
    (10, 'Oliver', 'Martin', '2003-02-10', 1, 2020, 'Male');
```

#### **Parents**
```sql
INSERT INTO Parents (ParentID, StudentID, ParentName, ContactNumber, Email, Relationship)
VALUES
    (1, 1, 'Richard Doe', '1234567890', 'richard.doe@example.com', 'Father'),
    (2, 2, 'Laura Smith', '1234567891', 'laura.smith@example.com', 'Mother'),
    (3, 3, 'Peter Brown', '1234567892', 'peter.brown@example.com', 'Father'),
    (4, 4, 'Emma Johnson', '1234567893', 'emma.johnson@example.com', 'Mother'),
    (5, 5, 'Evelyn Davis', '1234567894', 'evelyn.davis@example.com', 'Mother'),
    (6, 6, 'William Clark', '1234567895', 'william.clark@example.com', 'Father'),
    (7, 7, 'Sophia Walker', '1234567896', 'sophia.walker@example.com', 'Mother'),
    (8, 8, 'Lucas Harris', '1234567897', 'lucas.harris@example.com', 'Guardian'),
    (9, 9, 'Grace White', '1234567898', 'grace.white@example.com', 'Mother'),
    (10, 10, 'Liam Martin', '1234567899', 'liam.martin@example.com', 'Father');
```

---

### **Queries**

#### **1. Subqueries**
##### **a) Find Students in the Science Department**
```sql
SELECT CONCAT(FirstName, ' ', LastName) AS StudentName
FROM Students
WHERE ProgramID IN (
    SELECT ProgramID
    FROM Programs
    WHERE Department = 'Science'
);
```

##### **b) Find Parents of Students Enrolled After 2020**
```sql
SELECT ParentName, ContactNumber
FROM Parents
WHERE StudentID IN (
    SELECT StudentID
    FROM Students
    WHERE AdmissionYear > 2020
);
```

---

#### **2. SET Operations**
##### **a) Students in Both Science and Arts Departments**
```sql
SELECT FirstName, LastName
FROM Students
WHERE ProgramID IN (
    SELECT ProgramID FROM Programs WHERE Department = 'Science'
)
INTERSECT
SELECT FirstName, LastName
FROM Students
WHERE ProgramID IN (
    SELECT ProgramID FROM Programs WHERE Department = 'Arts'
);
```

##### **b) Students in Science or Humanities Departments**
```sql
SELECT FirstName, LastName
FROM Students
WHERE ProgramID IN (
    SELECT ProgramID FROM Programs WHERE Department = 'Science'
)
UNION
SELECT FirstName, LastName
FROM Students
WHERE ProgramID IN (
    SELECT ProgramID FROM Programs WHERE Department = 'Humanities'
);
```

##### **c) Students Only in Science Department**
```sql
SELECT FirstName, LastName
FROM Students
WHERE ProgramID IN (
    SELECT ProgramID FROM Programs WHERE Department = 'Science'
)
EXCEPT
SELECT FirstName, LastName
FROM Students
WHERE ProgramID NOT IN (
    SELECT ProgramID FROM Programs WHERE Department = 'Science'
);
```

---

#### **3. CASE Statement**
##### **a) Categorize Students by Admission Year**
```sql
SELECT CONCAT(FirstName, ' ', LastName) AS StudentName,
       CASE
           WHEN AdmissionYear < 2020 THEN 'Early Batch'
           WHEN AdmissionYear BETWEEN 2020 AND 2022 THEN 'Recent Batch'
           ELSE 'New Batch'
       END AS BatchCategory
FROM Students;
```

##### **b) Identify Parent Contact Roles**
```sql
SELECT ParentName, Relationship,
       CASE
           WHEN Relationship = 'Father' THEN 'Primary Contact'
           WHEN Relationship = 'Mother' THEN 'Secondary Contact'
           ELSE 'Support Contact'
       END AS ContactRole
FROM Parents;
```

##### **c) Students’ Current Age**
```sql
SELECT CONCAT(FirstName, ' ', LastName) AS StudentName,
       TIMESTAMPDIFF(YEAR, DateOfBirth, CURDATE()) AS CurrentAge,
       CASE
           WHEN TIMESTAMPDIFF(YEAR, DateOfBirth, CURDATE()) < 20 THEN 'Teen'
           WHEN TIMESTAMPDIFF(YEAR, DateOfBirth, CURDATE()) BETWEEN 20 AND 23 THEN 'Young Adult'
           ELSE 'Adult'
       END AS AgeGroup
FROM Students;
```

---

### **Practice Questions**
1. Write a query to find students whose parents have a contact number starting with '123'.  
2. Retrieve a list of programs with more than one student enrolled using a subquery.  
3. Identify students enrolled in either Science or Arts departments using a `UNION` operation.  
4. Classify programs as "Short-Term" if their duration is <=2 years or "Long-Term" otherwise using `CASE`.  
5. Find students who are only enrolled in programs under the "Arts" department using `EXCEPT`.  
