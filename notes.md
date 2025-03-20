# SQLite Installation and Usage Guide for Linux

## Installing SQLite and SQLite Browser

### Ubuntu/Debian-based Systems
```bash
# Install SQLite command-line tool
sudo apt update
sudo apt install sqlite3

# Install SQLite Browser (GUI)
sudo apt install sqlitebrowser
```

### Creating a New Database
```bash
# Create a new database file
sqlite3 mydatabase.db
```

### Creating a Table
Once in the SQLite prompt, you can create a table:
```sql
CREATE TABLE users (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    name TEXT NOT NULL,
    email TEXT UNIQUE,
    age INTEGER
);
```

### Basic SQLite Commands

#### List all tables
```sql
.tables
```

#### See table schema
```sql
.schema users
```

#### Change output format (column mode)
```sql
.mode column
.headers on
```

### Basic CRUD Operations

#### Insert Data
```sql
-- Insert a single row
INSERT INTO users (name, email, age) VALUES ('John Doe', 'john@example.com', 30);

-- Insert multiple rows
INSERT INTO users (name, email, age) VALUES 
    ('Jane Smith', 'jane@example.com', 28),
    ('Bob Johnson', 'bob@example.com', 35);
```

#### Query Data
```sql
-- Select all records
SELECT * FROM users;

-- Select with conditions
SELECT * FROM users WHERE age > 30;

-- Order results
SELECT * FROM users ORDER BY name;
```

#### Update Data
```sql
-- Update a record
UPDATE users SET age = 31 WHERE name = 'John Doe';
```

#### Delete Data
```sql
-- Delete a record
DELETE FROM users WHERE name = 'Jane Smith';
```

### Exiting SQLite
```sql
.quit
```

## Using SQLite Browser (GUI)

1. Launch SQLite Browser:
```bash
sqlitebrowser
```

2. From the GUI:
   - Click "New Database" to create a new database file
   - Click "Create Table" to design a new table
   - Use the "Browse Data" tab to view and edit data
   - Use the "Execute SQL" tab to run custom SQL queries

### Common GUI Operations
- **Creating a table**: Click on "Create Table" button, define column names, types, and constraints
- **Importing data**: Database → Import → Table from CSV file
- **Exporting data**: Database → Export → Table to CSV file
- **Executing SQL**: Switch to "Execute SQL" tab to run custom queries

## SQLite Data Types

SQLite uses dynamic typing with these storage classes:
- `INTEGER`: Whole numbers
- `REAL`: Floating-point numbers
- `TEXT`: Text strings
- `BLOB`: Binary data
- `NULL`: Null value

## SQLite Constraints

- `PRIMARY KEY`: Unique identifier for the row
- `AUTOINCREMENT`: Automatically increment the value
- `NOT NULL`: Column cannot contain NULL values
- `UNIQUE`: All values in the column must be unique
- `DEFAULT`: Default value for the column
- `CHECK`: Ensures values meet a specific condition

## Advanced SQLite Operations

### Foreign Keys

Foreign keys establish relationships between tables. You need to enable foreign key support first:

```sql
-- Enable foreign key support (must be done for each connection)
PRAGMA foreign_keys = ON;
```

#### Creating Tables with Foreign Keys

```sql
-- Create a departments table
CREATE TABLE departments (
    dept_id INTEGER PRIMARY KEY,
    dept_name TEXT NOT NULL
);

-- Create an employees table with a foreign key reference
CREATE TABLE employees (
    emp_id INTEGER PRIMARY KEY,
    name TEXT NOT NULL,
    salary REAL,
    dept_id INTEGER,
    FOREIGN KEY (dept_id) REFERENCES departments(dept_id)
);
```

#### Inserting Related Data

```sql
-- Insert departments first
INSERT INTO departments (dept_id, dept_name) VALUES 
    (1, 'HR'),
    (2, 'Engineering'),
    (3, 'Marketing');

-- Then insert employees with valid department references
INSERT INTO employees (name, salary, dept_id) VALUES 
    ('Alice', 75000, 2),
    ('Bob', 65000, 1),
    ('Charlie', 85000, 2),
    ('Diana', 70000, 3);
```

### JOIN Operations

JOINs combine rows from two or more tables based on a related column.

#### INNER JOIN
Returns records with matching values in both tables:

```sql
SELECT employees.name, employees.salary, departments.dept_name
FROM employees
INNER JOIN departments ON employees.dept_id = departments.dept_id;
```

#### LEFT JOIN
Returns all records from the left table and matched records from the right table:

```sql
SELECT employees.name, departments.dept_name
FROM employees
LEFT JOIN departments ON employees.dept_id = departments.dept_id;
```

#### Multiple Table Joins

```sql
-- Assuming we have a projects table with project_id and dept_id
SELECT employees.name, departments.dept_name, projects.project_name
FROM employees
JOIN departments ON employees.dept_id = departments.dept_id
JOIN projects ON departments.dept_id = projects.dept_id;
```

### Aggregate Functions

#### COUNT
```sql
-- Count total employees
SELECT COUNT(*) FROM employees;

-- Count employees by department
SELECT dept_id, COUNT(*) as employee_count 
FROM employees 
GROUP BY dept_id;
```

#### SUM, AVG, MIN, MAX
```sql
-- Sum of all salaries
SELECT SUM(salary) as total_payroll FROM employees;

-- Average salary
SELECT AVG(salary) as average_salary FROM employees;

-- Min and max salaries
SELECT MIN(salary) as lowest_salary, MAX(salary) as highest_salary FROM employees;

-- Average salary by department
SELECT dept_id, AVG(salary) as avg_dept_salary 
FROM employees 
GROUP BY dept_id;
```

### Filtering Aggregated Results (HAVING)

```sql
-- Find departments with average salary above 70000
SELECT dept_id, AVG(salary) as avg_salary 
FROM employees 
GROUP BY dept_id
HAVING avg_salary > 70000;
```

### Subqueries

```sql
-- Find employees with salary above average
SELECT name, salary 
FROM employees 
WHERE salary > (SELECT AVG(salary) FROM employees);

-- Find employees in the Engineering department
SELECT name 
FROM employees 
WHERE dept_id = (SELECT dept_id FROM departments WHERE dept_name = 'Engineering');
```

### Making changes to tables

```sql
-- Rename the users table to customers
ALTER TABLE users RENAME TO customers;
```

```sql
-- Add a new column to an existing table
ALTER TABLE users ADD COLUMN address TEXT;

-- Add a column with a default value
ALTER TABLE users ADD COLUMN registration_date TEXT DEFAULT CURRENT_TIMESTAMP;

-- Add a column with constraints
ALTER TABLE users ADD COLUMN username TEXT NOT NULL DEFAULT 'unknown';

-- Drop the 'age' column
ALTER TABLE users DROP COLUMN age;
```

### Deleting tables

```sql
-- Drop a table if it exists
DROP TABLE IF EXISTS users;

-- Drop a table (will cause an error if the table doesn't exist)
DROP TABLE employees;
```