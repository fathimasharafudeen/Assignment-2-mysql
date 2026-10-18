
-- MySQL Assignment 2 - Querying Data
-- Database: employee (tables: departments, location, employees)
-- Run employee_data.sql first to insert the data.
-- ============================================================

USE employee;

-- ------------------------------------------------------------
-- PART 1: BASIC QUERYING
-- ------------------------------------------------------------

-- 1. Distinct values: distinct salaries
SELECT DISTINCT salary
FROM employees;

-- 2. Alias (AS)
SELECT age AS Employee_Age,
       salary AS Employee_Salary
FROM employees;

-- 3a. WHERE: salary > 50000 and hired before 2016-01-01
SELECT *
FROM employees
WHERE salary > 50000
  AND hire_date < '2016-01-01';

-- 3b. Find the employee with a missing designation
SELECT *
FROM employees
WHERE designation IS NULL;

-- 3b. Fill the missing designation with 'Data Scientist'
UPDATE employees
SET designation = 'Data Scientist'
WHERE designation IS NULL;
-- (If Error 1175 appears, use: WHERE employee_id = 5004;)

-- Verify the update
SELECT employee_id, employee_name, designation
FROM employees
WHERE employee_id = 5004;

-- ------------------------------------------------------------
-- PART 2: SORTING AND GROUPING DATA
-- ------------------------------------------------------------

-- 1. ORDER BY: department ID ascending, salary descending
SELECT *
FROM employees
ORDER BY department_id ASC, salary DESC;

-- 2. LIMIT: first 5 employees hired in 2018
SELECT *
FROM employees
WHERE YEAR(hire_date) = 2018
ORDER BY hire_date
LIMIT 5;

-- 3a. Aggregate: sum of all salaries in the Finance department
SELECT SUM(e.salary) AS total_finance_salary
FROM employees e
JOIN departments d ON e.department_id = d.department_id
WHERE d.department_name = 'Finance';

-- 3b. Aggregate: minimum age among all employees
SELECT MIN(age) AS minimum_age
FROM employees;

-- 4a. GROUP BY: maximum salary for each location
SELECT l.location,
       MAX(e.salary) AS max_salary
FROM employees e
JOIN location l ON e.location_id = l.location_id
GROUP BY l.location;

-- 4b. GROUP BY: average salary for each designation containing 'Analyst'
SELECT designation,
       AVG(salary) AS avg_salary
FROM employees
WHERE designation LIKE '%Analyst%'
GROUP BY designation;

-- 5a. HAVING: departments with fewer than 3 employees
SELECT d.department_name,
       COUNT(e.employee_id) AS emp_count
FROM employees e
JOIN departments d ON e.department_id = d.department_id
GROUP BY d.department_name
HAVING COUNT(e.employee_id) < 3;

-- 5b. HAVING: locations where female employees' average age is below 30
SELECT l.location,
       AVG(e.age) AS avg_age
FROM employees e
JOIN location l ON e.location_id = l.location_id
WHERE e.gender = 'F'
GROUP BY l.location
HAVING AVG(e.age) < 30;

-- ------------------------------------------------------------
-- PART 3: JOINS
-- ------------------------------------------------------------

-- 1. INNER JOIN: employee name, designation, department name
SELECT e.employee_name,
       e.designation,
       d.department_name
FROM employees e
INNER JOIN departments d
        ON e.department_id = d.department_id;

-- 2. LEFT JOIN: all departments with total employees (including 0)
SELECT d.department_name,
       COUNT(e.employee_id) AS total_employees
FROM departments d
LEFT JOIN employees e
       ON d.department_id = e.department_id
GROUP BY d.department_id, d.department_name;

-- 3. RIGHT JOIN: all locations with employee names (NULL if none)
SELECT l.location,
       e.employee_name
FROM employees e
RIGHT JOIN location l
        ON e.location_id = l.location_id;
