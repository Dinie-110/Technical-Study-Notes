# SQL Study Guide: Beginner to Advanced

> A practical, GitHub-ready reference for learning Structured Query Language (SQL), from your first `SELECT` to query optimization, transactions, and interview preparation.

**Dialect:** Examples primarily use **PostgreSQL**. Standard SQL concepts apply broadly, but MySQL, SQL Server, SQLite, and Oracle differ in syntax. Dialect-specific behavior is identified where important.

## Table of Contents

1. [What Is SQL?](#1-what-is-sql)
2. [Set Up and Sample Database](#2-set-up-and-sample-database)
3. [SQL Syntax and Execution Order](#3-sql-syntax-and-execution-order)
4. [Reading Data with SELECT](#4-reading-data-with-select)
5. [Filtering with WHERE](#5-filtering-with-where)
6. [Sorting and Limiting Results](#6-sorting-and-limiting-results)
7. [Expressions, NULL, and CASE](#7-expressions-null-and-case)
8. [Aggregate Functions](#8-aggregate-functions)
9. [GROUP BY and HAVING](#9-group-by-and-having)
10. [JOINs and Relationships](#10-joins-and-relationships)
11. [Subqueries and EXISTS](#11-subqueries-and-exists)
12. [Common Table Expressions (CTEs)](#12-common-table-expressions-ctes)
13. [Set Operations](#13-set-operations)
14. [Inserting, Updating, and Deleting Data](#14-inserting-updating-and-deleting-data)
15. [Tables, Data Types, and Constraints](#15-tables-data-types-and-constraints)
16. [Database Design and Normalization](#16-database-design-and-normalization)
17. [Views and Materialized Views](#17-views-and-materialized-views)
18. [Window Functions](#18-window-functions)
19. [Date, Time, and Text Functions](#19-date-time-and-text-functions)
20. [Transactions and ACID](#20-transactions-and-acid)
21. [Indexes and Query Optimization](#21-indexes-and-query-optimization)
22. [Advanced SQL Patterns](#22-advanced-sql-patterns)
23. [Security and Safe SQL](#23-security-and-safe-sql)
24. [Stored Functions and Triggers](#24-stored-functions-and-triggers)
25. [SQL Interview Questions](#25-sql-interview-questions)
26. [Practice Exercises with Solutions](#26-practice-exercises-with-solutions)
27. [Mini Projects and Learning Roadmap](#27-mini-projects-and-learning-roadmap)
28. [Quick Reference Cheat Sheet](#28-quick-reference-cheat-sheet)

---

## 1. What Is SQL?

**SQL (Structured Query Language)** is used to define, read, modify, and manage data in relational databases. A database contains **tables**, tables contain **rows** (records), and rows contain **columns** (attributes).

| Category | Purpose | Common commands |
|---|---|---|
| DQL | Read/query data | `SELECT` |
| DML | Modify data | `INSERT`, `UPDATE`, `DELETE` |
| DDL | Define database objects | `CREATE`, `ALTER`, `DROP`, `TRUNCATE` |
| DCL | Manage privileges | `GRANT`, `REVOKE` |
| TCL | Control transactions | `BEGIN`, `COMMIT`, `ROLLBACK`, `SAVEPOINT` |

> The categories are conventional groupings, not a universal formal standard. Some commands' transactional behavior depends on the database.

**Key terms:** Primary key (PK) uniquely identifies a row; foreign key (FK) references a key in another (or the same) table; schema describes database objects; a relationship connects records across tables.

## 2. Set Up and Sample Database

Install PostgreSQL and use `psql`, pgAdmin, or another SQL client. Run this complete script in a **practice database** (not production). The sample is reused throughout this guide.

```sql
DROP TABLE IF EXISTS order_items, orders, products, employees, departments, customers CASCADE;

CREATE TABLE customers (
    customer_id INTEGER PRIMARY KEY,
    customer_name VARCHAR(100) NOT NULL,
    city VARCHAR(50),
    email VARCHAR(120) UNIQUE
);

CREATE TABLE departments (
    department_id INTEGER PRIMARY KEY,
    department_name VARCHAR(60) NOT NULL UNIQUE
);

CREATE TABLE employees (
    employee_id INTEGER PRIMARY KEY,
    employee_name VARCHAR(100) NOT NULL,
    department_id INTEGER REFERENCES departments(department_id),
    salary NUMERIC(10, 2) NOT NULL CHECK (salary >= 0),
    manager_id INTEGER REFERENCES employees(employee_id),
    hired_at DATE NOT NULL
);

CREATE TABLE products (
    product_id INTEGER PRIMARY KEY,
    product_name VARCHAR(100) NOT NULL,
    category VARCHAR(60) NOT NULL,
    price NUMERIC(10, 2) NOT NULL CHECK (price >= 0)
);

CREATE TABLE orders (
    order_id INTEGER PRIMARY KEY,
    customer_id INTEGER NOT NULL REFERENCES customers(customer_id),
    order_date DATE NOT NULL,
    status VARCHAR(20) NOT NULL CHECK (status IN ('pending', 'shipped', 'cancelled'))
);

CREATE TABLE order_items (
    order_id INTEGER NOT NULL REFERENCES orders(order_id),
    product_id INTEGER NOT NULL REFERENCES products(product_id),
    quantity INTEGER NOT NULL CHECK (quantity > 0),
    unit_price NUMERIC(10, 2) NOT NULL CHECK (unit_price >= 0),
    PRIMARY KEY (order_id, product_id)
);

INSERT INTO customers VALUES
(1, 'Aisha', 'Kuala Lumpur', 'aisha@example.com'),
(2, 'Ben', 'Penang', 'ben@example.com'),
(3, 'Chen', 'Kuala Lumpur', 'chen@example.com'),
(4, 'Dina', NULL, 'dina@example.com');

INSERT INTO departments VALUES
(10, 'Engineering'), (20, 'Sales'), (30, 'Operations');

INSERT INTO employees VALUES
(1, 'Maya', 10, 9000, NULL, '2022-01-10'),
(2, 'Ravi', 10, 6000, 1, '2023-03-15'),
(3, 'Lina', 20, 7000, NULL, '2021-07-01'),
(4, 'Omar', 20, 4500, 3, '2024-02-12'),
(5, 'Nora', NULL, 4000, NULL, '2025-06-01');

INSERT INTO products VALUES
(101, 'Laptop', 'Electronics', 3500),
(102, 'Mouse', 'Electronics', 80),
(103, 'Notebook', 'Stationery', 12),
(104, 'Desk Lamp', 'Home', 120);

INSERT INTO orders VALUES
(1001, 1, '2026-01-05', 'shipped'),
(1002, 1, '2026-01-10', 'pending'),
(1003, 2, '2026-02-02', 'shipped'),
(1004, 3, '2026-02-11', 'cancelled');

INSERT INTO order_items VALUES
(1001, 101, 1, 3500),
(1001, 102, 2, 80),
(1002, 103, 5, 12),
(1003, 102, 1, 80),
(1003, 104, 1, 120),
(1004, 103, 2, 12);
```

**Relationships:** `orders.customer_id → customers.customer_id`; `order_items.order_id → orders.order_id`; `order_items.product_id → products.product_id`; `employees.department_id → departments.department_id`; `employees.manager_id → employees.employee_id` (self-reference).

## 3. SQL Syntax and Execution Order

```sql
SELECT c.customer_name, COUNT(o.order_id) AS order_count
FROM customers AS c
LEFT JOIN orders AS o ON o.customer_id = c.customer_id
WHERE c.city = 'Kuala Lumpur'
GROUP BY c.customer_id, c.customer_name
HAVING COUNT(o.order_id) >= 1
ORDER BY order_count DESC
LIMIT 10;
```

SQL is declarative: you describe the result, and the database chooses an execution plan. A useful **logical processing model** is:

1. `FROM` / `JOIN` and `ON`
2. `WHERE`
3. `GROUP BY`
4. `HAVING`
5. `SELECT` expressions
6. `DISTINCT`
7. `ORDER BY`
8. `LIMIT` / `OFFSET`

This is a teaching model, not a promise about physical execution order. Most databases do not allow a `SELECT` alias in `WHERE`, because filtering is logically evaluated before that alias is produced.

## 4. Reading Data with SELECT

```sql
SELECT * FROM customers;                         -- All columns
SELECT customer_name, city FROM customers;       -- Specific columns
SELECT DISTINCT city FROM customers;             -- Unique cities (including one NULL)
SELECT customer_name AS name FROM customers;     -- Column alias
SELECT price, price * 1.06 AS price_with_tax FROM products;
```

**Best practice:** In applications, select only needed columns instead of `SELECT *` to avoid unnecessary data transfer and brittle dependencies.

## 5. Filtering with WHERE

```sql
SELECT * FROM products WHERE price > 100;
SELECT * FROM products WHERE category = 'Electronics' AND price < 1000;
SELECT * FROM products WHERE category = 'Home' OR category = 'Stationery';
SELECT * FROM products WHERE price BETWEEN 50 AND 150; -- Inclusive
SELECT * FROM customers WHERE city IN ('Penang', 'Kuala Lumpur');
SELECT * FROM customers WHERE customer_name LIKE 'A%'; -- Starts with A
SELECT * FROM customers WHERE customer_name ILIKE '%a%'; -- PostgreSQL: case-insensitive
SELECT * FROM customers WHERE city IS NULL;
SELECT * FROM customers WHERE city IS NOT NULL;
SELECT * FROM orders WHERE status <> 'cancelled';
```

`LIKE` uses `%` for any sequence of characters and `_` for one character. `AND` takes precedence over `OR`; use parentheses to express intent clearly.

## 6. Sorting and Limiting Results

```sql
SELECT * FROM products ORDER BY price DESC, product_name ASC;
SELECT * FROM products ORDER BY price DESC, product_id LIMIT 3;
SELECT * FROM products ORDER BY product_id LIMIT 2 OFFSET 2;
```

Always include a deterministic `ORDER BY` for pagination. For large datasets, **keyset pagination** often performs better than large `OFFSET` values:

```sql
SELECT * FROM products
WHERE product_id > 102
ORDER BY product_id
LIMIT 2;
```

## 7. Expressions, NULL, and CASE

`NULL` means unknown/missing, not zero or an empty string. Comparisons such as `city = NULL` do not return `TRUE`; use `IS NULL`. SQL uses three-valued logic: `TRUE`, `FALSE`, and `UNKNOWN`.

```sql
SELECT customer_name, COALESCE(city, 'Unknown') AS city
FROM customers;

SELECT product_name,
       CASE
           WHEN price >= 1000 THEN 'Premium'
           WHEN price >= 100 THEN 'Standard'
           ELSE 'Budget'
       END AS price_band
FROM products;

SELECT NULLIF(10, 10) AS returns_null;
SELECT 10.0 / NULLIF(0, 0) AS safe_division; -- NULL, not divide-by-zero
```

**Common pitfall:** `NOT IN` can behave unexpectedly if its subquery returns `NULL`; `NOT EXISTS` is often safer.

## 8. Aggregate Functions

Aggregates summarize multiple rows.

```sql
SELECT COUNT(*) AS total_products,
       COUNT(price) AS non_null_prices,
       SUM(price) AS sum_prices,
       AVG(price) AS avg_price,
       MIN(price) AS cheapest,
       MAX(price) AS most_expensive
FROM products;

SELECT COUNT(DISTINCT city) AS known_city_count FROM customers;
```

`COUNT(*)` counts rows; `COUNT(column)` counts only non-`NULL` values. `SUM`, `AVG`, `MIN`, and `MAX` generally ignore `NULL` inputs. `COUNT(DISTINCT city)` excludes `NULL`.

## 9. GROUP BY and HAVING

`WHERE` filters **individual rows before grouping**; `HAVING` filters **groups after aggregation**.

```sql
SELECT category, COUNT(*) AS product_count, ROUND(AVG(price), 2) AS avg_price
FROM products
WHERE price > 0
GROUP BY category
HAVING COUNT(*) >= 2
ORDER BY product_count DESC;
```

| Clause | Filters | Aggregate functions? |
|---|---|---|
| `WHERE` | Individual rows | Not directly in its predicate |
| `HAVING` | Grouped results | Yes |

Example: total spending per customer (excluding cancelled orders):

```sql
SELECT c.customer_id, c.customer_name,
       COALESCE(SUM(oi.quantity * oi.unit_price), 0) AS total_spent
FROM customers c
LEFT JOIN orders o
  ON o.customer_id = c.customer_id AND o.status <> 'cancelled'
LEFT JOIN order_items oi ON oi.order_id = o.order_id
GROUP BY c.customer_id, c.customer_name
ORDER BY total_spent DESC;
```

**Important:** A `LEFT JOIN` filter on the right table in `WHERE` can unintentionally eliminate unmatched rows. Put relevant right-side filters in `ON` if you need to preserve left-side rows.

## 10. JOINs and Relationships

A **foreign key** is a column (or set of columns) that refers to a candidate key, usually a primary key, in another table. For example, `orders.customer_id` is the FK; `customers.customer_id` is the referenced PK. A FK does **not** mean “data flowing from one table to another”; it enforces referential integrity.

### INNER JOIN: matched rows only

```sql
SELECT o.order_id, c.customer_name, o.order_date
FROM orders o
INNER JOIN customers c ON o.customer_id = c.customer_id;
```

### LEFT JOIN: all rows from the left

```sql
SELECT c.customer_name, o.order_id
FROM customers c
LEFT JOIN orders o ON c.customer_id = o.customer_id;
```

Dina appears even though she has no orders; her `order_id` is `NULL`.

### RIGHT JOIN: all rows from the right

```sql
SELECT c.customer_name, o.order_id
FROM orders o
RIGHT JOIN customers c ON o.customer_id = c.customer_id;
```

Often clearer as an equivalent `LEFT JOIN` with table order reversed.

### FULL OUTER JOIN: matched and unmatched from both sides

```sql
SELECT e.employee_name, d.department_name
FROM employees e
FULL OUTER JOIN departments d ON e.department_id = d.department_id;
```

### CROSS JOIN: Cartesian product

```sql
SELECT c.customer_name, p.product_name
FROM customers c CROSS JOIN products p;
```

4 customers × 4 products = 16 combinations.

### SELF JOIN: a table joined to itself

```sql
SELECT e.employee_name, m.employee_name AS manager_name
FROM employees e
LEFT JOIN employees m ON e.manager_id = m.employee_id;
```

### Many-to-many relationships

`orders` ↔ `products` is many-to-many through the junction table `order_items`. The **composite primary key** `(order_id, product_id)` prevents duplicate product lines per order in this model.

### Avoid duplicate counting after JOINs

```sql
-- Wrong for order counts if an order has multiple items:
SELECT COUNT(*) FROM orders o JOIN order_items oi ON oi.order_id = o.order_id;

-- Correct number of distinct orders:
SELECT COUNT(DISTINCT o.order_id)
FROM orders o JOIN order_items oi ON oi.order_id = o.order_id;
```

## 11. Subqueries and EXISTS

### Scalar subquery

```sql
SELECT product_name, price
FROM products
WHERE price > (SELECT AVG(price) FROM products);
```

### IN subquery

```sql
SELECT customer_name
FROM customers
WHERE customer_id IN (SELECT customer_id FROM orders);
```

### Correlated EXISTS

```sql
SELECT c.customer_name
FROM customers c
WHERE EXISTS (
    SELECT 1 FROM orders o WHERE o.customer_id = c.customer_id
);
```

### NOT EXISTS: customers without orders

```sql
SELECT c.customer_name
FROM customers c
WHERE NOT EXISTS (
    SELECT 1 FROM orders o WHERE o.customer_id = c.customer_id
);
```

`EXISTS` checks whether at least one qualifying row exists; the subquery's selected literal is irrelevant.

## 12. Common Table Expressions (CTEs)

A CTE defined by `WITH` makes multi-step queries easier to read.

```sql
WITH order_totals AS (
    SELECT o.order_id, o.customer_id,
           SUM(oi.quantity * oi.unit_price) AS total
    FROM orders o
    JOIN order_items oi ON oi.order_id = o.order_id
    WHERE o.status <> 'cancelled'
    GROUP BY o.order_id, o.customer_id
)
SELECT c.customer_name, SUM(ot.total) AS revenue
FROM order_totals ot
JOIN customers c ON c.customer_id = ot.customer_id
GROUP BY c.customer_id, c.customer_name;
```

### Recursive CTE: employee hierarchy

```sql
WITH RECURSIVE hierarchy AS (
    SELECT employee_id, employee_name, manager_id, 0 AS depth
    FROM employees WHERE manager_id IS NULL
    UNION ALL
    SELECT e.employee_id, e.employee_name, e.manager_id, h.depth + 1
    FROM employees e
    JOIN hierarchy h ON e.manager_id = h.employee_id
)
SELECT * FROM hierarchy ORDER BY depth, employee_id;
```

**Caution:** In real data, cycles can cause runaway recursion; add cycle detection or depth limits when necessary. CTEs are not automatically faster than subqueries.

## 13. Set Operations

```sql
SELECT city FROM customers WHERE city IS NOT NULL
UNION
SELECT department_name FROM departments; -- DISTINCT combined results

SELECT city FROM customers WHERE city IS NOT NULL
UNION ALL
SELECT department_name FROM departments; -- Keep duplicates

SELECT city FROM customers WHERE city IS NOT NULL
INTERSECT
SELECT 'Penang' AS city;

SELECT city FROM customers WHERE city IS NOT NULL
EXCEPT
SELECT 'Penang' AS city;
```

Set operation inputs must have compatible column counts and data types. `UNION` removes duplicates; `UNION ALL` keeps them.

## 14. Inserting, Updating, and Deleting Data

```sql
INSERT INTO customers (customer_id, customer_name, city, email)
VALUES (5, 'Farah', 'Johor Bahru', 'farah@example.com');

UPDATE customers
SET city = 'Ipoh'
WHERE customer_id = 5;

DELETE FROM customers WHERE customer_id = 5;
```

### PostgreSQL UPSERT

```sql
INSERT INTO products (product_id, product_name, category, price)
VALUES (102, 'Wireless Mouse', 'Electronics', 95)
ON CONFLICT (product_id)
DO UPDATE SET product_name = EXCLUDED.product_name,
              price = EXCLUDED.price;
```

### RETURNING (PostgreSQL)

```sql
UPDATE products SET price = price * 1.05
WHERE product_id = 103
RETURNING product_id, price;
```

> **Safety:** `UPDATE` or `DELETE` without `WHERE` can affect every row. Practice destructive statements inside a transaction and roll back if needed. These examples change the sample dataset; recreate it before exercises requiring original values.

## 15. Tables, Data Types, and Constraints

Common PostgreSQL data types:

| Type | Example | Use |
|---|---|---|
| `INTEGER`, `BIGINT` | `42` | Whole numbers |
| `NUMERIC(10,2)` | `123.45` | Exact decimal / money calculations |
| `REAL`, `DOUBLE PRECISION` | `3.14159` | Approximate floating-point |
| `VARCHAR(n)`, `TEXT` | `'Hello'` | Strings |
| `BOOLEAN` | `TRUE` | Boolean flags |
| `DATE` | `'2026-01-05'` | Calendar date |
| `TIMESTAMP` | `'2026-01-05 10:30'` | Local timestamp without timezone |
| `TIMESTAMPTZ` | `'2026-01-05 10:30+08'` | Timezone-aware instant |
| `UUID` | UUID value | Identifiers |
| `JSONB` | `'{"a":1}'` | Queryable JSON document |

Constraints: `PRIMARY KEY`, `FOREIGN KEY`, `UNIQUE`, `NOT NULL`, `CHECK`, `DEFAULT`.

```sql
CREATE TABLE tickets (
    ticket_id BIGINT GENERATED ALWAYS AS IDENTITY PRIMARY KEY,
    title TEXT NOT NULL,
    priority INTEGER NOT NULL DEFAULT 3 CHECK (priority BETWEEN 1 AND 5),
    is_open BOOLEAN NOT NULL DEFAULT TRUE,
    created_at TIMESTAMPTZ NOT NULL DEFAULT CURRENT_TIMESTAMP
);

ALTER TABLE tickets ADD COLUMN description TEXT;
ALTER TABLE tickets RENAME COLUMN description TO details;
DROP TABLE tickets;
```

**Important:** `UNIQUE` and `NULL` interactions differ across engines; PostgreSQL generally allows multiple `NULL` values under a normal unique constraint. Foreign keys can specify `ON DELETE CASCADE`, `SET NULL`, or other referential actions—choose carefully.

## 16. Database Design and Normalization

Normalization reduces redundant data and prevents update anomalies.

- **1NF:** Each column holds an atomic value for the model; no repeating groups.
- **2NF:** In 1NF; non-key attributes depend on the **whole** candidate key (especially relevant for composite keys).
- **3NF:** In 2NF; non-key attributes do not depend transitively on a key through other non-key attributes.

**Bad design:** `orders(order_id, customer_name, customer_email, product_1, product_2, ...)` duplicates customer data and caps the number of products.

**Better:** `customers`, `orders`, `products`, and `order_items`, connected by foreign keys.

Relationship cardinalities:

- One-to-one: one person ↔ one profile (enforce uniqueness on FK).
- One-to-many: one customer → many orders.
- Many-to-many: many orders ↔ many products (junction table).

Denormalization can help read-heavy workloads but increases maintenance and consistency risks. Prefer normalization first, then measure performance.

## 17. Views and Materialized Views

```sql
CREATE VIEW active_orders AS
SELECT order_id, customer_id, order_date
FROM orders WHERE status <> 'cancelled';

SELECT * FROM active_orders;
DROP VIEW active_orders;
```

A **view** stores a query definition. A PostgreSQL **materialized view** stores the query result and needs refreshing:

```sql
CREATE MATERIALIZED VIEW category_sales AS
SELECT p.category, SUM(oi.quantity * oi.unit_price) AS sales
FROM order_items oi
JOIN products p ON p.product_id = oi.product_id
GROUP BY p.category;

REFRESH MATERIALIZED VIEW category_sales;
SELECT * FROM category_sales;
DROP MATERIALIZED VIEW category_sales;
```

Materialized views may be stale until refreshed; refresh behavior and availability differ across database products.

## 18. Window Functions

Window functions calculate values **across related rows without collapsing rows**, unlike `GROUP BY`.

```sql
SELECT employee_name, department_id, salary,
       ROW_NUMBER() OVER (PARTITION BY department_id ORDER BY salary DESC, employee_id) AS row_num,
       RANK() OVER (PARTITION BY department_id ORDER BY salary DESC) AS salary_rank,
       DENSE_RANK() OVER (PARTITION BY department_id ORDER BY salary DESC) AS dense_rank
FROM employees;
```

- `ROW_NUMBER()`: unique sequential position.
- `RANK()`: tied values share rank, leaving gaps.
- `DENSE_RANK()`: tied values share rank, no gaps.

### Running totals

```sql
WITH daily_sales AS (
    SELECT o.order_date, SUM(oi.quantity * oi.unit_price) AS revenue
    FROM orders o
    JOIN order_items oi ON oi.order_id = o.order_id
    WHERE o.status <> 'cancelled'
    GROUP BY o.order_date
)
SELECT order_date, revenue,
       SUM(revenue) OVER (
           ORDER BY order_date
           ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW
       ) AS running_revenue
FROM daily_sales;
```

### LAG, LEAD, and averages

```sql
SELECT employee_name, department_id, salary,
       LAG(salary) OVER (PARTITION BY department_id ORDER BY salary, employee_id) AS previous_salary,
       LEAD(salary) OVER (PARTITION BY department_id ORDER BY salary, employee_id) AS next_salary,
       AVG(salary) OVER (PARTITION BY department_id) AS department_avg
FROM employees;
```

### Top 2 earners per department

```sql
WITH ranked AS (
    SELECT e.*,
           ROW_NUMBER() OVER (
               PARTITION BY department_id ORDER BY salary DESC, employee_id
           ) AS rn
    FROM employees e
)
SELECT * FROM ranked WHERE rn <= 2;
```

Use `RANK()` instead if all tied employees at a rank should be included.

## 19. Date, Time, and Text Functions

```sql
SELECT CURRENT_DATE, CURRENT_TIMESTAMP;
SELECT order_id, EXTRACT(YEAR FROM order_date) AS order_year FROM orders;
SELECT DATE_TRUNC('month', order_date::timestamp) AS month, COUNT(*)
FROM orders GROUP BY 1 ORDER BY 1;
SELECT order_date + INTERVAL '7 days' AS next_week FROM orders;

SELECT UPPER(customer_name), LOWER(customer_name), LENGTH(customer_name)
FROM customers;
SELECT TRIM('  SQL  '), REPLACE('SQL notes', 'notes', 'guide');
SELECT CONCAT(customer_name, ' - ', COALESCE(city, 'Unknown')) FROM customers;
SELECT SUBSTRING('PostgreSQL' FROM 1 FOR 4); -- Post
```

**PostgreSQL syntax:** `::` casts a value (`order_date::timestamp`). Portable SQL often uses `CAST(order_date AS TIMESTAMP)`.

### SQL dialect differences

| Task | PostgreSQL / SQLite / MySQL | SQL Server |
|---|---|---|
| Limit rows | `LIMIT 5` | `TOP (5)` or `OFFSET … FETCH` |
| String concatenation | PostgreSQL/SQLite: `||`; MySQL: `CONCAT()` | `+` or `CONCAT()` |
| Case-insensitive pattern | PostgreSQL: `ILIKE` | Depends on collation |
| Current date | PostgreSQL: `CURRENT_DATE` | `CAST(GETDATE() AS date)` |
| Upsert | PostgreSQL: `ON CONFLICT`; MySQL: `ON DUPLICATE KEY UPDATE` | Engine-specific patterns |
| Full outer join | PostgreSQL, SQL Server: supported | MySQL: no direct `FULL OUTER JOIN` |

## 20. Transactions and ACID

A transaction groups operations into a unit of work.

- **Atomicity:** All-or-nothing changes.
- **Consistency:** Constraints/invariants remain valid when correctly enforced.
- **Isolation:** Concurrent transactions interact according to an isolation level.
- **Durability:** Committed changes survive supported failures.

```sql
BEGIN;
UPDATE products SET price = price - 10 WHERE product_id = 101;
UPDATE products SET price = price + 10 WHERE product_id = 102;
-- Inspect the changes, then choose ONE:
ROLLBACK; -- Undo both changes
-- COMMIT; -- Persist both changes (instead of ROLLBACK)
```

### SAVEPOINT

```sql
BEGIN;
UPDATE products SET price = 100 WHERE product_id = 102;
SAVEPOINT before_second_change;
UPDATE products SET price = 200 WHERE product_id = 103;
ROLLBACK TO SAVEPOINT before_second_change;
COMMIT;
```

Common isolation levels: `READ UNCOMMITTED`, `READ COMMITTED`, `REPEATABLE READ`, `SERIALIZABLE`. PostgreSQL treats `READ UNCOMMITTED` like `READ COMMITTED`. Higher isolation may require retrying failed transactions. Concurrency problems include dirty reads, nonrepeatable reads, phantoms, and lost updates (actual guarantees vary by engine).

### Row locking

```sql
BEGIN;
SELECT * FROM products WHERE product_id = 101 FOR UPDATE;
-- Perform a related update safely inside this transaction.
COMMIT;
```

Keep transactions short to reduce lock contention.

## 21. Indexes and Query Optimization

Indexes help the database locate matching rows but add storage and write overhead.

```sql
CREATE INDEX idx_orders_customer_date ON orders(customer_id, order_date);
CREATE INDEX idx_products_category ON products(category);

EXPLAIN ANALYZE
SELECT * FROM orders
WHERE customer_id = 1 AND order_date >= DATE '2026-01-01';
```

`EXPLAIN ANALYZE` **actually executes** the query in PostgreSQL; take care with writes. Inspect sequential/index scans, estimated vs actual row counts, sort operations, and join strategies.

**Optimization checklist:**

1. Check correctness and row counts first.
2. Filter early where semantically possible.
3. Index frequently used join keys and selective predicates.
4. Prefer sargable predicates: `order_date >= DATE '2026-01-01'` rather than `EXTRACT(YEAR FROM order_date) = 2026` for ordinary B-tree indexes.
5. Avoid unnecessary `SELECT *`, `DISTINCT`, and sorting.
6. Inspect execution plans and measure on representative data.
7. Maintain database statistics and avoid indexing every column.

An index is not guaranteed to be used; for tiny tables, a sequential scan can be faster. A composite index's column order matters.

## 22. Advanced SQL Patterns

### Conditional aggregation

```sql
SELECT customer_id,
       COUNT(*) AS all_orders,
       COUNT(*) FILTER (WHERE status = 'shipped') AS shipped_orders,
       SUM(CASE WHEN status = 'pending' THEN 1 ELSE 0 END) AS pending_orders
FROM orders
GROUP BY customer_id;
```

`FILTER` is supported by PostgreSQL; `SUM(CASE ...)` is more portable.

### Find duplicate values

```sql
SELECT city, COUNT(*) AS occurrences
FROM customers
WHERE city IS NOT NULL
GROUP BY city
HAVING COUNT(*) > 1;
```

### Second-highest distinct salary

```sql
SELECT MAX(salary) AS second_highest
FROM employees
WHERE salary < (SELECT MAX(salary) FROM employees);
```

Returns `NULL` if no second distinct salary exists.

### Latest order for each customer

```sql
WITH ranked_orders AS (
    SELECT o.*,
           ROW_NUMBER() OVER (
               PARTITION BY customer_id ORDER BY order_date DESC, order_id DESC
           ) AS rn
    FROM orders o
)
SELECT * FROM ranked_orders WHERE rn = 1;
```

### Percent of total revenue by product

```sql
WITH product_sales AS (
    SELECT p.product_id, p.product_name,
           SUM(oi.quantity * oi.unit_price) AS revenue
    FROM products p
    JOIN order_items oi ON oi.product_id = p.product_id
    JOIN orders o ON o.order_id = oi.order_id
    WHERE o.status <> 'cancelled'
    GROUP BY p.product_id, p.product_name
)
SELECT product_name, revenue,
       ROUND(100.0 * revenue / NULLIF(SUM(revenue) OVER (), 0), 2) AS pct_total
FROM product_sales
ORDER BY revenue DESC;
```

### JSONB (PostgreSQL)

```sql
SELECT '{"user":{"name":"Aisha"},"active":true}'::jsonb
       -> 'user' ->> 'name' AS name;
```

`->` returns JSON; `->>` returns text.

## 23. Security and Safe SQL

**Never build SQL by concatenating untrusted input.** Use parameterized queries. Example using Python and `psycopg` (version 3):

```python
import psycopg

with psycopg.connect("dbname=practice user=app_user") as conn:
    with conn.cursor() as cur:
        customer_id = 1  # In an app, this could come from user input
        cur.execute(
            "SELECT customer_name FROM customers WHERE customer_id = %s",
            (customer_id,)
        )
        print(cur.fetchall())
```

Parameter placeholders protect **values**, not arbitrary table/column identifiers; use an allowlist or a safe identifier-composition API for identifiers.

Other essentials: least-privilege roles, secrets outside source code, encrypted connections, tested backups, access logging, and careful use of `GRANT`/`REVOKE`.

```sql
-- Example only; requires suitable administrative permissions.
GRANT SELECT ON customers TO reporting_role;
REVOKE SELECT ON customers FROM reporting_role;
```

## 24. Stored Functions and Triggers

PostgreSQL function example:

```sql
CREATE OR REPLACE FUNCTION apply_discount(amount NUMERIC, pct NUMERIC)
RETURNS NUMERIC
LANGUAGE SQL
IMMUTABLE
AS $$
    SELECT amount * (1 - pct / 100);
$$;

SELECT apply_discount(100, 15); -- 85
```

Trigger example (practice in an isolated database):

```sql
CREATE TABLE audit_log (
    audit_id BIGINT GENERATED ALWAYS AS IDENTITY PRIMARY KEY,
    event_name TEXT NOT NULL,
    logged_at TIMESTAMPTZ NOT NULL DEFAULT CURRENT_TIMESTAMP
);

CREATE OR REPLACE FUNCTION log_product_insert()
RETURNS TRIGGER
LANGUAGE plpgsql
AS $$
BEGIN
    INSERT INTO audit_log(event_name)
    VALUES ('Product inserted: ' || NEW.product_id);
    RETURN NEW;
END;
$$;

CREATE TRIGGER trg_product_insert
AFTER INSERT ON products
FOR EACH ROW
EXECUTE FUNCTION log_product_insert();
```

Triggers can enforce workflows and audit changes, but hidden side effects make systems harder to debug. Prefer explicit application logic when appropriate.

## 25. SQL Interview Questions

**1. What is the difference between `WHERE` and `HAVING`?** `WHERE` filters input rows; `HAVING` filters grouped results, often using aggregate conditions.

**2. `INNER JOIN` vs `LEFT JOIN`?** `INNER JOIN` returns matches; `LEFT JOIN` preserves every left-side row and fills unmatched right-side columns with `NULL`.

**3. Primary key vs foreign key?** A PK uniquely identifies a row and is not `NULL`; a FK references a key to enforce relationships and may be nullable unless constrained.

**4. `DELETE` vs `TRUNCATE` vs `DROP`?** `DELETE` removes selected rows; `TRUNCATE` efficiently removes all rows in a table (engine-specific behavior); `DROP` removes the table definition and its data.

**5. `UNION` vs `UNION ALL`?** `UNION` deduplicates; `UNION ALL` preserves duplicates and is usually cheaper.

**6. `COUNT(*)` vs `COUNT(column)`?** All rows vs only rows with non-`NULL` values in that column.

**7. What is an index?** An auxiliary data structure that can speed up retrieval at the cost of extra storage and write work.

**8. What is normalization?** Structuring tables to reduce redundancy and update anomalies.

**9. What is ACID?** Atomicity, Consistency, Isolation, Durability.

**10. `RANK()` vs `DENSE_RANK()`?** Both share ranks for ties; `RANK()` leaves gaps and `DENSE_RANK()` does not.

**11. What is a CTE?** A named query expression introduced by `WITH`, useful for readable and recursive queries.

**12. Why might a JOIN return unexpected duplicates?** One-to-many or many-to-many relationships multiply rows; inspect join keys and intended result grain.

**13. How do you find customers with no orders?** Use `NOT EXISTS` or `LEFT JOIN ... WHERE o.order_id IS NULL`.

**14. What is SQL injection?** Manipulation of dynamically constructed SQL by untrusted input; prevent it using parameterized statements and least privilege.

**15. Why is `NULL = NULL` not true?** `NULL` represents unknown information; comparisons involving `NULL` generally evaluate to `UNKNOWN`.

## 26. Practice Exercises with Solutions

> Use the original sample data from Section 2. Try each challenge before expanding the solution.

### Exercise 1 — List electronics under 100

```sql
SELECT product_name, price
FROM products
WHERE category = 'Electronics' AND price < 100;
-- Expected: Mouse, 80.00
```

### Exercise 2 — Count orders for each customer, including zero

```sql
SELECT c.customer_name, COUNT(o.order_id) AS order_count
FROM customers c
LEFT JOIN orders o ON o.customer_id = c.customer_id
GROUP BY c.customer_id, c.customer_name
ORDER BY c.customer_id;
-- Aisha 2; Ben 1; Chen 1; Dina 0
```

### Exercise 3 — Employees paid above department average

```sql
SELECT e.employee_name, e.salary, e.department_id
FROM employees e
WHERE e.salary > (
    SELECT AVG(e2.salary)
    FROM employees e2
    WHERE e2.department_id = e.department_id
);
-- Maya, Lina
```

### Exercise 4 — Revenue from non-cancelled orders

```sql
SELECT SUM(oi.quantity * oi.unit_price) AS total_revenue
FROM orders o
JOIN order_items oi ON oi.order_id = o.order_id
WHERE o.status <> 'cancelled';
-- Expected: 3920.00
```

### Exercise 5 — Find customers who never ordered

```sql
SELECT c.customer_name
FROM customers c
WHERE NOT EXISTS (
    SELECT 1 FROM orders o WHERE o.customer_id = c.customer_id
);
-- Expected: Dina
```

### Exercise 6 — Top product by units sold (exclude cancellations)

```sql
SELECT p.product_name, SUM(oi.quantity) AS units_sold
FROM order_items oi
JOIN products p ON p.product_id = oi.product_id
JOIN orders o ON o.order_id = oi.order_id
WHERE o.status <> 'cancelled'
GROUP BY p.product_id, p.product_name
ORDER BY units_sold DESC, p.product_id
LIMIT 1;
-- Expected: Notebook, 5 units
```

### Exercise 7 — Department with highest average salary

```sql
SELECT d.department_name, ROUND(AVG(e.salary), 2) AS avg_salary
FROM departments d
JOIN employees e ON e.department_id = d.department_id
GROUP BY d.department_id, d.department_name
ORDER BY avg_salary DESC
LIMIT 1;
-- Expected: Engineering, 7500.00
```

### Exercise 8 — Monthly revenue (excluding cancellations)

```sql
SELECT DATE_TRUNC('month', o.order_date::timestamp)::date AS month,
       SUM(oi.quantity * oi.unit_price) AS revenue
FROM orders o
JOIN order_items oi ON oi.order_id = o.order_id
WHERE o.status <> 'cancelled'
GROUP BY 1
ORDER BY 1;
-- 2026-01-01: 3720.00; 2026-02-01: 200.00
```

### Exercise 9 — Orders whose total exceeds 100

```sql
SELECT o.order_id, SUM(oi.quantity * oi.unit_price) AS order_total
FROM orders o
JOIN order_items oi ON oi.order_id = o.order_id
GROUP BY o.order_id
HAVING SUM(oi.quantity * oi.unit_price) > 100
ORDER BY o.order_id;
-- Expected: 1001 (3660.00), 1003 (200.00)
```

### Exercise 10 — Display every employee and manager

```sql
SELECT e.employee_name AS employee,
       COALESCE(m.employee_name, 'No manager') AS manager
FROM employees e
LEFT JOIN employees m ON m.employee_id = e.manager_id
ORDER BY e.employee_id;
```

## 27. Mini Projects and Learning Roadmap

### Beginner: Student Management Database

Design `students`, `courses`, and `enrollments`. Practice `CREATE TABLE`, primary/foreign keys, `INSERT`, `SELECT`, `JOIN`, and basic reports. Deliver a schema file and ten example queries.

### Intermediate: E-Commerce Sales Analytics

Use the sample tables in this guide. Create reports for monthly revenue, top-selling products, repeat customers, average order value, and cancelled-order rate. Explain whether your metric is at **order**, **order-item**, or **customer** grain.

### Advanced: Inventory and Reorder Analytics

Design inventory movements, warehouses, products, and supplier lead times. Write rolling demand metrics, reorder alerts, stockout analysis, and performance-optimized reports. Add indexes and compare `EXPLAIN ANALYZE` plans on a realistically sized dataset.

**Suggested 6-week plan:**

| Week | Topics | Deliverable |
|---|---|---|
| 1 | SELECT, WHERE, ORDER BY, NULL, CASE | 20 simple queries |
| 2 | Aggregation, GROUP BY, HAVING | Sales summary report |
| 3 | JOINs, keys, subqueries, CTEs | Multi-table reporting queries |
| 4 | Schema design, DML, transactions | Student database project |
| 5 | Windows, indexes, optimization | Ranked reports and query plans |
| 6 | Advanced patterns, security, interview tasks | E-commerce analytics portfolio |

### GitHub repository suggestion

```text
sql-study-guide/
├── README.md
├── schema/
│   └── sample_database.sql
├── queries/
│   ├── beginner.sql
│   ├── intermediate.sql
│   └── advanced.sql
└── projects/
    └── ecommerce-analytics/
        └── README.md
```

To use this guide as your repository landing page, **rename `SQL_README.md` to `README.md`** before uploading it to the root of your SQL repository.

## 28. Quick Reference Cheat Sheet

```sql
-- READ
SELECT col1, col2 FROM table_name;
SELECT DISTINCT col1 FROM table_name;
SELECT * FROM table_name WHERE col1 IS NOT NULL;
SELECT * FROM table_name ORDER BY col1 DESC LIMIT 10;

-- AGGREGATE
SELECT col1, COUNT(*) FROM table_name GROUP BY col1;
SELECT col1, COUNT(*) FROM table_name GROUP BY col1 HAVING COUNT(*) > 1;

-- JOIN
SELECT a.*, b.* FROM table_a a INNER JOIN table_b b ON a.id = b.a_id;
SELECT a.*, b.* FROM table_a a LEFT JOIN table_b b ON a.id = b.a_id;

-- WRITE (review WHERE conditions first)
INSERT INTO table_name (col1) VALUES ('value');
UPDATE table_name SET col1 = 'new' WHERE id = 1;
DELETE FROM table_name WHERE id = 1;

-- SCHEMA
CREATE TABLE example (id INTEGER PRIMARY KEY, name TEXT NOT NULL);
ALTER TABLE example ADD COLUMN created_at TIMESTAMP;
CREATE INDEX idx_example_name ON example(name);

-- TRANSACTION
BEGIN;
-- statements
COMMIT; -- or ROLLBACK;
```

> **Note:** `table_name`, `table_a`, `table_b`, `col1`, and `id` in this cheat sheet are placeholders, not names from the sample schema. Replace them with your own table/column names before running.

---

## Further Reading

- [PostgreSQL Documentation](https://www.postgresql.org/docs/)
- [PostgreSQL Tutorial](https://www.postgresql.org/docs/current/tutorial.html)
- [SQLite Documentation](https://www.sqlite.org/docs.html)
- [MySQL Reference Manual](https://dev.mysql.com/doc/)
- [Microsoft T-SQL Documentation](https://learn.microsoft.com/en-us/sql/t-sql/language-reference)

**Learning tip:** Read a section, run its examples, change one condition, predict the output, then check your result. Understanding *why* rows appear or disappear is more useful than memorizing syntax.

**License:** These study notes may be adapted for your own learning repository.
