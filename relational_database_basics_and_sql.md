# Task: Relational Database Basics and SQL

## Objective
Demonstrate fundamental relational database concepts by designing two normalized tables with a primary key / foreign key relationship, inserting sample records, and executing SQL queries utilizing `INNER JOIN` and `LEFT JOIN`.

---

## 1. Schema Definition (DDL)

```sql
-- 1. Create Departments Table (Parent Table)
CREATE TABLE departments (
    department_id SERIAL PRIMARY KEY,
    department_name VARCHAR(100) NOT NULL,
    location VARCHAR(100) NOT NULL
);

-- 2. Create Employees Table (Child Table with Foreign Key)
CREATE TABLE employees (
    employee_id SERIAL PRIMARY KEY,
    first_name VARCHAR(50) NOT NULL,
    last_name VARCHAR(50) NOT NULL,
    email VARCHAR(100) UNIQUE NOT NULL,
    salary NUMERIC(10, 2) NOT NULL,
    hire_date DATE NOT NULL DEFAULT CURRENT_DATE,
    department_id INT REFERENCES departments(department_id) ON DELETE SET NULL
);
