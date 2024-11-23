Here are some common **SQL queries** that can be used to retrieve data from a database. These queries are categorized based on common tasks such as selecting specific columns, filtering data, joining tables, and aggregating results.

### 1. **Select All Data from a Table**
To get all the data from a table, use the `SELECT` statement:
```sql
SELECT * FROM table_name;
```
- `*` selects all columns from the table `table_name`.

### 2. **Select Specific Columns from a Table**
To retrieve specific columns from a table:
```sql
SELECT column1, column2 FROM table_name;
```
- Replace `column1` and `column2` with the actual column names you need.

### 3. **Filter Data with `WHERE`**
To filter data based on conditions:
```sql
SELECT * FROM table_name WHERE condition;
```
- Example:
```sql
SELECT * FROM employees WHERE salary > 50000;
```
- This will return all employees with a salary greater than 50,000.

### 4. **Using `AND`, `OR` to Combine Conditions**
You can combine multiple conditions with `AND` or `OR`:
```sql
SELECT * FROM employees WHERE salary > 50000 AND department = 'IT';
```
- This returns all employees from the 'IT' department with a salary greater than 50,000.

### 5. **Sorting Results with `ORDER BY`**
To sort data in ascending (`ASC`) or descending (`DESC`) order:
```sql
SELECT * FROM employees ORDER BY salary DESC;
```
- This will sort the employees by their salary in descending order.

### 6. **Limiting the Number of Results with `LIMIT`**
To limit the number of rows returned:
```sql
SELECT * FROM employees LIMIT 5;
```
- This will return only the first 5 rows of the `employees` table.

### 7. **Using `DISTINCT` to Remove Duplicates**
To get unique values from a column:
```sql
SELECT DISTINCT department FROM employees;
```
- This will return a list of unique departments in the `employees` table.

### 8. **Aggregate Functions (`COUNT`, `SUM`, `AVG`, `MAX`, `MIN`)**
To perform calculations on columns, such as counting rows, summing values, averaging, finding maximum or minimum values:
```sql
SELECT COUNT(*) FROM employees WHERE salary > 50000;
```
- This will return the number of employees with a salary greater than 50,000.

Example of using other aggregate functions:
```sql
SELECT AVG(salary) FROM employees;
```
- This will return the average salary of all employees.

```sql
SELECT MAX(salary) FROM employees;
```
- This will return the maximum salary in the `employees` table.

### 9. **Group Data Using `GROUP BY`**
To group data by a specific column, for example, to calculate sums or averages per group:
```sql
SELECT department, AVG(salary) FROM employees GROUP BY department;
```
- This will return the average salary for each department.

### 10. **Filtering Groups Using `HAVING`**
`HAVING` is used to filter results after grouping:
```sql
SELECT department, AVG(salary) FROM employees GROUP BY department HAVING AVG(salary) > 50000;
```
- This will return the average salary for each department where the average salary is greater than 50,000.

### 11. **Joining Tables (`JOIN`)**
To retrieve data from multiple related tables, use `JOIN`. The most common types are `INNER JOIN`, `LEFT JOIN`, `RIGHT JOIN`, and `FULL OUTER JOIN`.

- **INNER JOIN**: Returns records that have matching values in both tables.
```sql
SELECT employees.name, departments.department_name
FROM employees
INNER JOIN departments ON employees.department_id = departments.department_id;
```
- **LEFT JOIN**: Returns all records from the left table and matched records from the right table.
```sql
SELECT employees.name, departments.department_name
FROM employees
LEFT JOIN departments ON employees.department_id = departments.department_id;
```
- **RIGHT JOIN**: Returns all records from the right table and matched records from the left table.
```sql
SELECT employees.name, departments.department_name
FROM employees
RIGHT JOIN departments ON employees.department_id = departments.department_id;
```
- **FULL OUTER JOIN**: Returns all records when there is a match in either left or right table.
```sql
SELECT employees.name, departments.department_name
FROM employees
FULL OUTER JOIN departments ON employees.department_id = departments.department_id;
```

### 12. **Using `IN` for Multiple Values**
To check if a column value is in a list of specified values:
```sql
SELECT * FROM employees WHERE department IN ('IT', 'HR');
```
- This will return all employees who belong to either the 'IT' or 'HR' department.

### 13. **Using `LIKE` for Pattern Matching**
To filter data based on pattern matching:
```sql
SELECT * FROM employees WHERE name LIKE 'John%';
```
- This will return all employees whose name starts with "John".

### 14. **Subqueries**
You can use a subquery to get data that meets certain criteria:
```sql
SELECT * FROM employees WHERE department_id = (SELECT department_id FROM departments WHERE department_name = 'HR');
```
- This will return all employees who work in the 'HR' department, using a subquery to find the `department_id`.

### 15. **Joining with Subqueries**
Subqueries can also be used within `JOIN` clauses to retrieve data:
```sql
SELECT e.name, e.salary, d.department_name
FROM employees e
JOIN (SELECT department_id, department_name FROM departments WHERE department_name = 'IT') d
ON e.department_id = d.department_id;
```
- This will return the name, salary, and department name of employees working in the 'IT' department.

### 16. **Using `EXISTS` for Subqueries**
`EXISTS` is used to check whether a subquery returns any results:
```sql
SELECT * FROM employees e WHERE EXISTS (SELECT 1 FROM departments d WHERE e.department_id = d.department_id AND d.department_name = 'IT');
```
- This will return all employees who belong to the 'IT' department, using `EXISTS` to check if there is a matching department.

### 17. **Using `CASE` for Conditional Logic**
The `CASE` statement is used to perform conditional logic inside your queries:
```sql
SELECT name, salary,
    CASE
        WHEN salary > 70000 THEN 'High'
        WHEN salary BETWEEN 50000 AND 70000 THEN 'Medium'
        ELSE 'Low'
    END AS salary_range
FROM employees;
```
- This will categorize employees based on their salary into 'High', 'Medium', or 'Low'.

### 18. **Using `DISTINCT` with Aggregates**
You can use `DISTINCT` to find unique values even with aggregate functions:
```sql
SELECT COUNT(DISTINCT department_id) FROM employees;
```
- This will return the number of unique departments in the `employees` table.

---

### Conclusion:
These are some of the most common SQL queries for retrieving data from a database. SQL provides a rich set of functionality for querying and manipulating data, including filtering, aggregating, joining tables, and more. You can combine these techniques to build powerful queries for a wide range of data retrieval needs.
