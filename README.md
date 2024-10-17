# MySQL---CTEs-in-MySQL

# DAY 27 - Challenge 27

# COMMON TABLE EXPRESSIONS (CTEs) IN MySQL

As part of a 75-day data analysis challenge, this work on MySQL covers CTEs

USE Challenge;

CREATE TABLE staff (
    staff_id INT PRIMARY KEY,
    first_name VARCHAR(50),
    last_name VARCHAR(50),
    department_id INT,
    manager_id INT,
    hire_date DATE,
    salary DECIMAL(10, 2)
);

INSERT INTO staff (staff_id, first_name, last_name, department_id, manager_id, hire_date, salary) VALUES
(1, 'Alice', 'Johnson', 1, NULL, '2020-05-01', 80000),
(2, 'Bob', 'Smith', 1, 1, '2021-03-15', 55000),
(3, 'Carol', 'Williams', 2, NULL, '2019-09-10', 75000),
(4, 'David', 'Brown', 2, 3, '2022-06-01', 60000),
(5, 'Eve', 'Davis', 3, NULL, '2020-01-20', 90000),
(6, 'Frank', 'Miller', 3, 5, '2023-04-18', 65000),
(7, 'Grace', 'Wilson', 3, 5, '2023-08-05', 70000),
(8, 'Hank', 'Moore', 1, 1, '2022-10-12', 51000);

CREATE TABLE departments2 (
    department_id INT PRIMARY KEY,
    department_name VARCHAR(50)
);

INSERT INTO departments2 (department_id, department_name) VALUES
(1, 'Engineering'),
(2, 'HR'),
(3, 'Sales');

SELECT * FROM staff;
SELECT * FROM departments2;


![Day 27](https://github.com/user-attachments/assets/a6a510e3-3c7c-40df-b424-23f1a4ff7da9)              ![Day 27 II](https://github.com/user-attachments/assets/608bca5c-b375-4f33-9a6d-ddd39440c207)



# Use a Non-Recursive CTE to filter high earners in the Engineering department.

WITH HighEarners AS (
SELECT staff_id, first_name, department_id, salary
FROM staff
WHERE salary > 50000
)
SELECT first_name, salary
FROM HighEarners
WHERE department_id = 1;

# Use a Recursive CTE to find the reporting hierarchy starting from the CEO (Alice).

WITH RECURSIVE OrgHierarchy AS (
    SELECT staff_id, first_name, manager_id
    FROM staff
    WHERE manager_id IS NULL  -- CEO
    UNION ALL
    SELECT s.staff_id, s.first_name, s.manager_id
    FROM staff s
    INNER JOIN OrgHierarchy o ON s.manager_id = o.staff_id
)
SELECT * FROM OrgHierarchy;


![Day 27 III](https://github.com/user-attachments/assets/90dc6952-b05e-4c8c-9625-2e56d8508062)



# Calculate total salary by department using a CTE.

WITH DepartmentSalary AS (
SELECT department_id, SUM(salary) AS TotalSalary
FROM staff
GROUP BY department_id
)
SELECT department_id, TotalSalary
FROM DepartmentSalary;

# Combine multiple CTEs to find high earners hired in the last year.

WITH HighEarners AS (
    SELECT staff_id, first_name, salary
    FROM staff
    WHERE salary > 50000
), RecentHires AS (
    SELECT staff_id, first_name, hire_date
    FROM staff
    WHERE hire_date >= '2023-01-01'
)
SELECT e.first_name, e.salary, h.hire_date
FROM HighEarners e
JOIN RecentHires h ON e.staff_id = h.staff_id;


![Day 27 IV](https://github.com/user-attachments/assets/aa99445b-0aca-4f54-88c5-64e7ccb09fcc)
