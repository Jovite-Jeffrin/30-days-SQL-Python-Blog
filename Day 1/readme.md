# 🚀 Day 1: Mastering Window Functions in PostgreSQL – `ROW_NUMBER`, `RANK`, and `DENSE_RANK`

## 🔍 Overview

Window functions are incredibly powerful tools in SQL for performing calculations across a set of rows that are somehow related to the current row. Unlike aggregate functions, window functions **do not collapse rows**—you retain the full detail of your dataset while still performing calculations.

Today, we focus on:
- `ROW_NUMBER()`
- `RANK()`
- `DENSE_RANK()`

---

## 🎯 Why You Need Window Functions

These functions are commonly used in:
- Leaderboards (e.g., top-selling products)
- Identifying duplicates
- Pagination
- Time-series analysis (more on this in Day 8)

---

## 🧠 Quick Syntax

```sql
<function_name>() OVER (
  PARTITION BY column1
  ORDER BY column2
)
```

---

## 🧪 Sample Dataset

Let’s use a fictional **sales table**:

```sql
CREATE TABLE sales (
    region TEXT,
    salesperson TEXT,
    revenue INT
);

INSERT INTO sales VALUES
('East', 'Alice', 1000),
('East', 'Bob', 1500),
('East', 'Charlie', 1500),
('West', 'Diana', 2000),
('West', 'Eve', 2000),
('West', 'Frank', 1800);
```

---

## ⚙️ 1. `ROW_NUMBER()`

Assigns a unique sequential number **without regard to ties**.

```sql
SELECT
    region,
    salesperson,
    revenue,
    ROW_NUMBER() OVER (PARTITION BY region ORDER BY revenue DESC) AS row_num
FROM sales;
```

🧾 **Result:**

| region | salesperson | revenue | row_num |
|--------|-------------|---------|---------|
| East   | Bob         | 1500    | 1       |
| East   | Charlie     | 1500    | 2       |
| East   | Alice       | 1000    | 3       |
| West   | Diana       | 2000    | 1       |
| West   | Eve         | 2000    | 2       |
| West   | Frank       | 1800    | 3       |

---

## ⚙️ 2. `RANK()`

Ranks items with **gaps** in case of ties.

```sql
SELECT
    region,
    salesperson,
    revenue,
    RANK() OVER (PARTITION BY region ORDER BY revenue DESC) AS rank
FROM sales;
```

🧾 **Result:**

| region | salesperson | revenue | rank |
|--------|-------------|---------|------|
| East   | Bob         | 1500    | 1    |
| East   | Charlie     | 1500    | 1    |
| East   | Alice       | 1000    | 3    |
| West   | Diana       | 2000    | 1    |
| West   | Eve         | 2000    | 1    |
| West   | Frank       | 1800    | 3    |

---

## ⚙️ 3. `DENSE_RANK()`

Ranks items **without gaps** for ties.

```sql
SELECT
    region,
    salesperson,
    revenue,
    DENSE_RANK() OVER (PARTITION BY region ORDER BY revenue DESC) AS dense_rank
FROM sales;
```

🧾 **Result:**

| region | salesperson | revenue | dense_rank |
|--------|-------------|---------|------------|
| East   | Bob         | 1500    | 1          |
| East   | Charlie     | 1500    | 1          |
| East   | Alice       | 1000    | 2          |
| West   | Diana       | 2000    | 1          |
| West   | Eve         | 2000    | 1          |
| West   | Frank       | 1800    | 2          |

---

## 💼 Scenario-Based Interview Question

### **❓Problem:**
You work as a data analyst for a retail company. Your manager wants to know the **top 2 salespersons per region** based on total revenue, and **you should not include ties beyond the top 2.**

### 🔧 Solution using `ROW_NUMBER()`

```sql
WITH ranked_sales AS (
  SELECT
    region,
    salesperson,
    revenue,
    ROW_NUMBER() OVER (PARTITION BY region ORDER BY revenue DESC) AS row_num
  FROM sales
)
SELECT region, salesperson, revenue
FROM ranked_sales
WHERE row_num <= 2;
```

✅ **Why this works:**  
Unlike `RANK()` and `DENSE_RANK()`, `ROW_NUMBER()` guarantees exactly 2 rows per region, ignoring ties after the limit.

---

## 📝 Summary & Takeaways

| Function      | Handles Ties? | Gaps in Ranking? | Use When...                          |
|---------------|----------------|-------------------|--------------------------------------|
| `ROW_NUMBER()` | ❌             | N/A               | You want a strict top-N list         |
| `RANK()`       | ✅             | ✅                 | You need rank with visible tie gaps  |
| `DENSE_RANK()` | ✅             | ❌                 | You want rank without gaps           |

---

## 🧠 Challenge for Readers

Try this:
> Write a query to get the **3rd highest** salesperson per region, considering ties, using `RANK()`.
