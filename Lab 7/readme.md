### **Lab 7: Transactions, COMMIT, ROLLBACK, and SAVEPOINT**

#### **Objective**  
- Understand the concept of transactions in SQL.  
- Practice using `COMMIT`, `ROLLBACK`, and `SAVEPOINT` for maintaining database consistency during operations.  

---

### **Scenario: University Database**

This lab continues with the **Students**, **Programs**, and **Grades** tables, introducing transactions to handle complex operations safely.

---

### **Step 1: Table Creation**

#### **Students Table**
```sql
CREATE TABLE Students (
    StudentID INT PRIMARY KEY,
    FirstName VARCHAR(50),
    LastName VARCHAR(50),
    DateOfBirth DATE,
    ProgramID INT,
    AdmissionYear INT
);
```

#### **Programs Table**
```sql
CREATE TABLE Programs (
    ProgramID INT PRIMARY KEY,
    ProgramName VARCHAR(100),
    DurationYears INT
);
```

#### **Grades Table**
```sql
CREATE TABLE Grades (
    GradeID INT PRIMARY KEY,
    StudentID INT,
    Subject VARCHAR(50),
    Marks INT
);
```

---

### **Step 2: Data Insertion**

#### **Insert Data into Programs**
```sql
INSERT INTO Programs (ProgramID, ProgramName, DurationYears)
VALUES 
    (1, 'B.Sc. Computer Science', 3),
    (2, 'M.Sc. Computer Science', 2),
    (3, 'B.Sc. Mathematics', 3),
    (4, 'M.Sc. Physics', 2);
```

#### **Insert Data into Students**
```sql
INSERT INTO Students (StudentID, FirstName, LastName, DateOfBirth, ProgramID, AdmissionYear)
VALUES 
    (1, 'Alice', 'Smith', '2002-04-15', 1, 2020),
    (2, 'Bob', 'Johnson', '2003-05-22', 2, 2021),
    (3, 'Charlie', 'Brown', '2001-09-10', 3, 2019),
    (4, 'Diana', 'White', '2004-11-01', 4, 2022);
```

#### **Insert Data into Grades**
```sql
INSERT INTO Grades (GradeID, StudentID, Subject, Marks)
VALUES 
    (1, 1, 'Mathematics', 85),
    (2, 1, 'Physics', 78),
    (3, 2, 'Mathematics', 92),
    (4, 3, 'Chemistry', 74),
    (5, 4, 'Physics', 89);
```

---

### **Step 3: Queries with Transactions**

#### **1. Basic Transaction with COMMIT and ROLLBACK**

##### **Scenario:** Add a new student and a grade entry, then decide to roll back the changes.  

```sql
-- Start the transaction
BEGIN;

-- Add a new student
INSERT INTO Students (StudentID, FirstName, LastName, DateOfBirth, ProgramID, AdmissionYear)
VALUES (5, 'Eve', 'Miller', '2002-07-18', 1, 2023);

-- Add a grade for the new student
INSERT INTO Grades (GradeID, StudentID, Subject, Marks)
VALUES (6, 5, 'Mathematics', 88);

-- Rollback the transaction
ROLLBACK;
```

---

#### **2. Using SAVEPOINT**

##### **Scenario:** Add multiple grades for an existing student but decide to roll back to a specific point.  

```sql
-- Start the transaction
BEGIN;

-- Add a grade
INSERT INTO Grades (GradeID, StudentID, Subject, Marks)
VALUES (7, 1, 'Chemistry', 82);

-- Set a SAVEPOINT
SAVEPOINT BeforePhysics;

-- Add another grade
INSERT INTO Grades (GradeID, StudentID, Subject, Marks)
VALUES (8, 1, 'Physics', 90);

-- Realize a mistake and roll back to the savepoint
ROLLBACK TO BeforePhysics;

-- Commit the rest of the transaction
COMMIT;
```

---

#### **3. Correcting Errors with ROLLBACK**

##### **Scenario:** Update all marks for a student, but revert the changes after realizing they were incorrect.  

```sql
-- Start the transaction
BEGIN;

-- Update marks for StudentID 2
UPDATE Grades 
SET Marks = Marks + 5
WHERE StudentID = 2;

-- Realize the update was incorrect
ROLLBACK;
```

---

#### **4. Transaction on Multiple Tables**

##### **Scenario:** Enroll a new student in a program and insert initial grades in a single transaction.  

```sql
-- Start the transaction
BEGIN;

-- Add a new student
INSERT INTO Students (StudentID, FirstName, LastName, DateOfBirth, ProgramID, AdmissionYear)
VALUES (6, 'Frank', 'Green', '2003-03-12', 3, 2023);

-- Add grades for the new student
INSERT INTO Grades (GradeID, StudentID, Subject, Marks)
VALUES 
    (9, 6, 'Mathematics', 80),
    (10, 6, 'Physics', 85);

-- Commit the transaction
COMMIT;
```

---

#### **5. Conditional Logic in Transactions**

##### **Scenario:** Update all students' marks for a subject, but only commit if no marks exceed 100 after the update.  

```sql
-- Start the transaction
BEGIN;

-- Add a new student
INSERT INTO Students (StudentID, FirstName, LastName, DateOfBirth, ProgramID, AdmissionYear)
VALUES (7, 'Frank', 'Green', '2003-03-12', 3, 2023);

-- Create a savepoint
SAVEPOINT InsertGrades;

-- Add grades for the new student
INSERT INTO Grades (GradeID, StudentID, Subject, Marks)
VALUES 
    (8, 7, 'Mathematics', 80), 
    (11, 7, 'Physics', 85),
    (12, 7, 'Chemistry', 78);

-- If something goes wrong, rollback to the savepoint
ROLLBACK TO InsertGrades;

-- Otherwise, finalize the transaction
COMMIT;


```

---

### **Step 4: Practice Questions**

1. Add a new student and their grades in a single transaction, but revert only the grades if an error occurs.  
2. Update the admission year for all students in the "M.Sc. Physics" program, but rollback if the year becomes inconsistent.  
3. Use `SAVEPOINT` to partially roll back changes while inserting grades for multiple students.  
4. Increase marks for all students enrolled after 2021, but use `ROLLBACK` if the changes impact records incorrectly.  

---

### **Key Learnings**
- Transactions ensure **consistency** and **atomicity** in the database.  
- Using `SAVEPOINT` allows partial rollbacks within a transaction.  
- `COMMIT` finalizes changes, while `ROLLBACK` discards them.  
