# Use-CREATE-VIEW-with-complex-SELECT

Got it! Here’s a simple, point-by-point **step-by-step process** to run and understand your script.

---

# Step-by-step (MySQL Workbench)

1. **Create a database and switch to it**

```sql
CREATE DATABASE view_complex_select;
USE view_complex_select;
```

* Makes a clean workspace so tables/views don’t clash with others.

2. **Create the `Sales` table**

```sql
CREATE TABLE Sales (
    SaleID INT PRIMARY KEY,
    ProductName VARCHAR(50),
    Quantity INT,
    Price DECIMAL(10,2),
    SaleDate DATE
);
```

* Columns: id, name, qty, unit price, date.

3. **Insert some rows**

```sql
INSERT INTO Sales (SaleID, ProductName, Quantity, Price, SaleDate) VALUES
(1, 'Laptop',   2, 55000.00, '2025-08-01'),
(2, 'Mouse',    5,   500.00, '2025-08-02'),
(3, 'Keyboard', 3,  1500.00, '2025-08-02'),
(4, 'Laptop',   1, 55000.00, '2025-08-03');
```

4. **(Optional) Check the raw data**

```sql
SELECT * FROM Sales;
```

5. **Create the view (summary logic)**

```sql
CREATE VIEW ProductSalesSummary AS
SELECT 
    ProductName,
    SUM(Quantity) AS TotalQuantitySold,
    SUM(Quantity * Price) AS TotalRevenue
FROM Sales
GROUP BY ProductName;
```

* `SUM(Quantity)` → totals units per product.
* `SUM(Quantity * Price)` → totals money per product.
* `GROUP BY ProductName` → one row per product.

6. **Use the view**

```sql
SELECT * FROM ProductSalesSummary;
```

### Expected result

| ProductName | TotalQuantitySold | TotalRevenue |
| ----------- | ----------------: | -----------: |
| Laptop      |                 3 |       165000 |
| Mouse       |                 5 |         2500 |
| Keyboard    |                 3 |         4500 |

(Explanation: Laptop 2+1 = 3 units → 2×55000 + 1×55000 = **165000**.)

---

## Helpful extras

* **Re-run safely (avoids “already exists” errors):**

```sql
DROP VIEW IF EXISTS ProductSalesSummary;
DROP TABLE IF EXISTS Sales;
```

* **See all views in this DB:**

```sql
SHOW FULL TABLES WHERE Table_type = 'VIEW';
```

* **See the view’s SQL:**

```sql
SHOW CREATE VIEW ProductSalesSummary;
```

* **Prove views update automatically (insert one more sale):**

```sql
INSERT INTO Sales (SaleID, ProductName, Quantity, Price, SaleDate)
VALUES (5, 'Mouse', 2, 500.00, '2025-08-04');

SELECT * FROM ProductSalesSummary; -- Mouse totals will increase
```

---

## If you are using **DB Browser for SQLite**

* Skip `CREATE DATABASE` and `USE ...` (SQLite opens a `.db` file instead).
* Table & inserts are the same.
* Date is stored as text; your ISO format `'YYYY-MM-DD'` is perfect.
* To list views:

```sql
SELECT name FROM sqlite_master WHERE type='view';
```

* To see definition:

```sql
SELECT sql FROM sqlite_master WHERE type='view' AND name='ProductSalesSummary';
```

* Safe re-run:

```sql
DROP VIEW IF EXISTS ProductSalesSummary;
DROP TABLE IF EXISTS Sales;
```

---

If you want, I can also add a **JOIN-based view** next (customers + sales) so you see a “complex SELECT” with multiple tables.
