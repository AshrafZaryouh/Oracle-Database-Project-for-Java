# 💻 Oracle Database Project for Java  

### *Comprehensive Report with Explanations, Schema, and Tables*

---

## 1. 🧭 Introduction

![two_tier](https://github.com/user-attachments/assets/6841ee0a-eeea-49ef-b0d8-be8e5c20de1b)

This project demonstrates how to connect and integrate a **Java application** with an **Oracle Database** to perform essential CRUD operations (Create, Read, Update, Delete). It follows a modular, layered design that separates logic, data, and presentation.

### Objectives
- Design a relational schema for managing employees, departments, and projects.  
- Implement Java code to connect with Oracle DB using JDBC.  
- Demonstrate queries, transactions, and data flow.  
- Apply security, performance, and maintainability principles.

---

## 2. 🏗️ Project Architecture

![cncpt121](https://github.com/user-attachments/assets/ac4861d6-148e-4a55-ac57-de34f9ea6c9f)

The project follows a **3-tier architecture** for clarity and scalability.

### 2.1 Layers Overview

| Layer | Description | Technology Used |
|-------|--------------|-----------------|
| Presentation | Interface where users or APIs interact | JavaFX / Spring MVC / REST API |
| Business Logic | Validation, rules, and data processing | Core Java / Spring Boot |
| Data Access | Database operations and persistence | JDBC / Hibernate / MyBatis |

### 2.2 Architectural Flow

```
[ User Interface ]
        ↓
[ Java Service Layer ]
        ↓
[ DAO Layer (JDBC/ORM) ]
        ↓
[ Oracle Database ]
```

Each layer is independent but communicates through well-defined interfaces, ensuring **loose coupling** and **easy maintenance**.

---

## 3. 🧩 Database Design

![cncpt041](https://github.com/user-attachments/assets/7f5fcd72-9ebb-48ae-9063-ce4ddc9fed46)

The project uses a single schema named **HR_SYSTEM**, containing three interconnected tables:
1. **DEPARTMENTS**
2. **EMPLOYEES**
3. **PROJECTS**

### 3.1 ER (Entity-Relationship) Diagram

```
DEPARTMENTS (1) ───< EMPLOYEES (M)
EMPLOYEES (1) ───< PROJECTS (M)
```

This structure enforces referential integrity:
- One department can have many employees.
- One employee can manage multiple projects.

---

## 4. 🗂️ Schema and Tables

![cncpt045](https://github.com/user-attachments/assets/bb1339ad-710d-4675-8b30-6bc28a6585cb)

### 4.1 Table: DEPARTMENTS

**Purpose:**  
Stores information about company departments.

| Column | Type | Constraint | Description |
|--------|------|-------------|-------------|
| DEPT_ID | NUMBER(5) | PRIMARY KEY | Unique Department ID |
| DEPT_NAME | VARCHAR2(50) | NOT NULL | Department Name |
| LOCATION | VARCHAR2(50) |  | Department Location |

**SQL Script:**
```sql
CREATE TABLE DEPARTMENTS (
  DEPT_ID NUMBER(5) PRIMARY KEY,
  DEPT_NAME VARCHAR2(50) NOT NULL,
  LOCATION VARCHAR2(50)
);
```

---

### 4.2 Table: EMPLOYEES

**Purpose:**  
Holds all employee details and their department association.

| Column | Type | Constraint | Description |
|--------|------|-------------|-------------|
| EMP_ID | NUMBER(6) | PRIMARY KEY | Employee ID |
| NAME | VARCHAR2(100) | NOT NULL | Employee Name |
| EMAIL | VARCHAR2(100) | UNIQUE | Unique Email |
| SALARY | NUMBER(10,2) | CHECK (SALARY > 0) | Monthly Salary |
| DEPT_ID | NUMBER(5) | FOREIGN KEY | Links to DEPARTMENTS(DEPT_ID) |

**SQL Script:**
```sql
CREATE TABLE EMPLOYEES (
  EMP_ID NUMBER(6) PRIMARY KEY,
  NAME VARCHAR2(100) NOT NULL,
  EMAIL VARCHAR2(100) UNIQUE,
  SALARY NUMBER(10,2) CHECK (SALARY > 0),
  DEPT_ID NUMBER(5),
  FOREIGN KEY (DEPT_ID) REFERENCES DEPARTMENTS(DEPT_ID)
);
```

---

### 4.3 Table: PROJECTS

**Purpose:**  
Contains project information and the employee responsible.

| Column | Type | Constraint | Description |
|--------|------|-------------|-------------|
| PROJECT_ID | NUMBER(6) | PRIMARY KEY | Project ID |
| PROJECT_NAME | VARCHAR2(100) | NOT NULL | Project Name |
| START_DATE | DATE |  | Start Date |
| END_DATE | DATE |  | Completion Date |
| EMP_ID | NUMBER(6) | FOREIGN KEY | References EMPLOYEES(EMP_ID) |

**SQL Script:**
```sql
CREATE TABLE PROJECTS (
  PROJECT_ID NUMBER(6) PRIMARY KEY,
  PROJECT_NAME VARCHAR2(100) NOT NULL,
  START_DATE DATE,
  END_DATE DATE,
  EMP_ID NUMBER(6),
  FOREIGN KEY (EMP_ID) REFERENCES EMPLOYEES(EMP_ID)
);
```

---

## 5. ⚙️ Java Integration (JDBC Layer)

![dbarch](https://github.com/user-attachments/assets/3db7b32b-2bc0-4be7-a5bb-6acf47013d77)

Java interacts with Oracle through **JDBC (Java Database Connectivity)**, enabling direct SQL execution within applications.

### 5.1 Connection Example

```java
String url = "jdbc:oracle:thin:@localhost:1521:ORCL";
String username = "hr_system";
String password = "hr_pass";

Connection conn = DriverManager.getConnection(url, username, password);
```

### 5.2 DAO Example (Insert Employee)

```java
public void insertEmployee(int id, String name, String email, double salary, int deptId) {
    String sql = "INSERT INTO EMPLOYEES VALUES (?, ?, ?, ?, ?)";
    try (Connection conn = DriverManager.getConnection(URL, USER, PASS);
         PreparedStatement ps = conn.prepareStatement(sql)) {
        ps.setInt(1, id);
        ps.setString(2, name);
        ps.setString(3, email);
        ps.setDouble(4, salary);
        ps.setInt(5, deptId);
        ps.executeUpdate();
    } catch (SQLException e) {
        e.printStackTrace();
    }
}
```

**Explanation:**  
- Uses a **PreparedStatement** to avoid SQL injection.  
- Automatically closes resources using try-with-resources.  
- Keeps SQL logic separate in DAO layer for maintainability.

---

## 6. 🔄 Data Flow Diagram

<img width="1562" height="770" alt="ouaw-initial-data-replication-architecture" src="https://github.com/user-attachments/assets/318f4d94-e116-401d-a56b-ee9e2651609f" />

```
+-------------+      +-------------+      +---------------+
|   User UI   | ---> |   Java App  | ---> |   Oracle DB   |
| (Form/API)  |      | (DAO Layer) |      | (Tables/SQL)  |
+-------------+      +-------------+      +---------------+
```

**Explanation:**  
- Input flows from UI → Java logic → Database.  
- Results flow back for display or reporting.  
- Ensures a clean, predictable data lifecycle.

---

## 7. 🔐 Security and Performance

<img width="1280" height="510" alt="1713361392296" src="https://github.com/user-attachments/assets/d6c79f16-e63f-45e3-b5eb-62ad5724110d" />

| Concern | Description | Best Practice |
|----------|--------------|---------------|
| SQL Injection | Malicious SQL from user input | Always use `PreparedStatement` |
| Connection Overhead | Frequent connection creation | Use **Connection Pooling** (HikariCP, UCP) |
| Data Security | Sensitive credentials or data | Use SSL and environment configs |
| Query Performance | Slow queries | Apply indexes and use `EXPLAIN PLAN` |

---

## 8. 📊 Sample SQL Queries

![ui](https://github.com/user-attachments/assets/c36a9678-ad04-4122-b54d-04bbb6dfadb7)

### 8.1 Retrieve Employee with Department
```sql
SELECT e.EMP_ID, e.NAME, d.DEPT_NAME
FROM EMPLOYEES e
JOIN DEPARTMENTS d ON e.DEPT_ID = d.DEPT_ID;
```

**Explanation:**  
This query demonstrates a **JOIN** between tables to retrieve related data.

---

### 8.2 Retrieve Project Assignments
```sql
SELECT p.PROJECT_NAME, e.NAME AS ASSIGNED_TO
FROM PROJECTS p
JOIN EMPLOYEES e ON p.EMP_ID = e.EMP_ID;
```

**Explanation:**  
Shows how **foreign key relationships** connect projects and employees.

---

## 9. 🧰 Advanced Features (Optional)

| Feature | Purpose |
|----------|----------|
| Stored Procedures | Move complex logic into Oracle for performance |
| Triggers | Automate data consistency and audit trails |
| Java Stored Procedures | Run Java inside Oracle DB |
| REST APIs | Expose data via web endpoints using Spring Boot |
| Reporting | Generate PDF or Excel reports with tools like JasperReports |

---

## 10. 🧠 Conclusion

This Oracle–Java project provides a **complete example of enterprise data integration**, covering:
- Logical data modeling with relational schemas  
- Secure database access through JDBC  
- Structured, layered design for scalability  
- Extensible features for web and API development  

---

### ✅ Deliverables Summary

| Component | Description |
|------------|-------------|
| SQL Scripts | Create schema and tables |
| Java Source | DAO, models, service classes |
| Config Files | Database connection details |
| Documentation | Project report (this file) |
| Optional | REST endpoints or GUI interface |

---

