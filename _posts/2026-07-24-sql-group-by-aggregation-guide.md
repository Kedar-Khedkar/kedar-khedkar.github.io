---
title: "SQL GROUP BY and Aggregation: Complete Guide with Examples"
description: "Master SQL GROUP BY and aggregation functions — COUNT, SUM,
  AVG, MIN, MAX, HAVING, and ROLLUP with real query examples
  and common pitfalls explained."
keywords:
  - sql group by aggregation
  - group by clause sql examples
  - sql aggregate functions tutorial
  - COUNT SUM AVG MIN MAX sql
  - sql having vs where clause
  - group by multiple columns sql
  - sql rollup grouping sets
  - aggregation functions with group by
  - sql group by order of execution
  - bigquery group by aggregation examples
categories:
  - DBMS
tags:
  - sql
  - dbms
  - group-by
  - aggregation
  - query-optimization
  - bigquery
  - database-design
author: kedarkhedkar
date: 2026-07-24 10:00:00 +0000
slug: sql-group-by-aggregation-guide
canonical_url: https://kedar-khedkar.github.io/posts/sql-group-by-aggregation-guide
image:
  path: /assets/img/posts/group-by-aggregation-in-sql.png
  alt: SQL GROUP BY and Aggregation

og_title: "SQL GROUP BY and Aggregation: Complete Guide with Examples"
og_description: "Everything about SQL GROUP BY — aggregate functions, HAVING,
  ROLLUP, GROUPING SETS & common mistakes, with real examples."
og_type: article
og_url: https://kedar-khedkar.github.io/posts/sql-group-by-aggregation-guide

twitter_card: summary_large_image
twitter_title: "SQL GROUP BY and Aggregation: Complete Guide with Examples"
twitter_description: "GROUP BY, HAVING, ROLLUP, GROUPING SETS & every aggregate
  function — with real SQL examples. Bookmark this one 📌"
twitter_site: "@khedkar_kedar"
---
# GROUP BY & Aggregate Functions — Explained Like You're 10 🎯
Let's forget SQL for a minute and imagine you're a **shopkeeper** with a notebook full of sales. That's the best way to understand this.

---

## 🛒 The Story: Imagine a Fruit Shop

You sold fruits all day and wrote each sale in your notebook:

| Sale # | Fruit  | Customer | Quantity | Price (₹) |
|--------|--------|----------|----------|-----------|
| 1      | Apple  | Ravi     | 2        | 100       |
| 2      | Banana | Priya    | 6        | 60        |
| 3      | Apple  | Sita     | 3        | 150       |
| 4      | Mango  | Ravi     | 1        | 80        |
| 5      | Banana | Ravi     | 4        | 40        |
| 6      | Apple  | Priya    | 5        | 250       |
| 7      | Mango  | Sita     | 2        | 160       |

At the end of the day, your dad asks:
> *"How much of each fruit did we sell today?"*

You can't just stare at 7 rows. You need to **group similar things together** and then **calculate something** (sum, count, average, etc.).

That's exactly what **GROUP BY + Aggregate Functions** do!

---

## 🧠 Part 1: What is GROUP BY?

**GROUP BY = "Put similar things in the same bucket."**

It's like sorting your laundry:
- 👕 All shirts in one pile
- 👖 All pants in another
- 🧦 All socks in another

In our fruit shop, GROUP BY Fruit means:

🍎 **Apple bucket** → Sales 1, 3, 6
🍌 **Banana bucket** → Sales 2, 5
🥭 **Mango bucket** → Sales 4, 7

**But wait — just grouping doesn't give an answer yet.** You need to *do something* with each bucket. That's where aggregate functions come in.

---

## 🧮 Part 2: What are Aggregate Functions?

**Aggregate functions = "A calculation done on the whole bucket."**

They take many values and squish them into ONE answer.

### The Famous 5 Aggregate Functions

| Function | What it does | Layman Meaning |
|----------|--------------|----------------|
| **SUM()** | Adds everything up | "Total" |
| **COUNT()** | Counts how many | "How many?" |
| **AVG()** | Average | "On average..." |
| **MAX()** | Largest value | "Biggest" |
| **MIN()** | Smallest value | "Smallest" |

---

## 🎬 Part 3: GROUP BY + Aggregate in Action

### Question 1: "How much money did each fruit make?"

**Step 1 — Group by Fruit:**
```
🍎 Apple bucket  → ₹100, ₹150, ₹250
🍌 Banana bucket → ₹60, ₹40
🥭 Mango bucket  → ₹80, ₹160
```

**Step 2 — Apply SUM() on Price:**
```
🍎 Apple  → ₹500
🍌 Banana → ₹100
🥭 Mango  → ₹240
```

**SQL:**
```sql
SELECT Fruit, SUM(Price) AS Total_Sales
FROM Sales
GROUP BY Fruit;
```

**Result:**
| Fruit  | Total_Sales |
|--------|-------------|
| Apple  | 500         |
| Banana | 100         |
| Mango  | 240         |

🎉 7 rows became 3 rows. That's the magic!

---

### Question 2: "How many sales did each customer make?"

**Step 1 — Group by Customer:**
```
Ravi  → Sale 1, 4, 5
Priya → Sale 2, 6
Sita  → Sale 3, 7
```

**Step 2 — Apply COUNT():**
```
Ravi  → 3 sales
Priya → 2 sales
Sita  → 2 sales
```

**SQL:**
```sql
SELECT Customer, COUNT(*) AS Number_Of_Sales
FROM Sales
GROUP BY Customer;
```

---

### Question 3: "What's the average price each customer paid?"

**SQL:**
```sql
SELECT Customer, AVG(Price) AS Avg_Spent
FROM Sales
GROUP BY Customer;
```

---

## 🧩 Part 4: The Golden Rule (Don't Forget!)

👉 **Whatever column you put in SELECT must either:**
1. Be in the GROUP BY clause, OR
2. Be inside an aggregate function.

❌ **Wrong:**
```sql
SELECT Fruit, Customer, SUM(Price)
FROM Sales
GROUP BY Fruit;   -- Customer is NOT grouped or aggregated → ERROR!
```

✅ **Right:**
```sql
SELECT Fruit, Customer, SUM(Price)
FROM Sales
GROUP BY Fruit, Customer;
```

🧠 **Memory trick:** *"If it's in SELECT, it must be in GROUP BY or wrapped in an aggregate function."*

---

## 🎚️ Part 5: GROUP BY Multiple Columns

What if you want: *"How much did each customer spend on each fruit?"*

You need **2 levels of buckets** — group by Customer **AND** Fruit:

```sql
SELECT Customer, Fruit, SUM(Price) AS Total
FROM Sales
GROUP BY Customer, Fruit;
```

**Result:**
| Customer | Fruit  | Total |
|----------|--------|-------|
| Ravi     | Apple  | 100   |
| Ravi     | Mango  | 80    |
| Ravi     | Banana | 40    |
| Priya    | Banana | 60    |
| Priya    | Apple  | 250   |
| Sita     | Apple  | 150   |
| Sita     | Mango  | 160   |

Think of it as **buckets inside buckets** 🪣🪣

---

## 🚪 Part 6: WHERE vs HAVING (The Most Confusing Part!)

This trips up EVERYONE. Let's nail it:

### WHERE → Filter BEFORE grouping (filters individual rows)
### HAVING → Filter AFTER grouping (filters groups)

**Analogy:** Imagine a school assembly:
- **WHERE** = Bouncer at the door — *"Only students with uniforms can enter."* (filters people before they form groups)
- **HAVING** = Teacher checking class strength — *"Only classes with more than 30 students get a prize."* (filters groups after they're formed)

### Example:
> *"Show fruits with total sales > ₹200, considering only sales above ₹50."*

```sql
SELECT Fruit, SUM(Price) AS Total
FROM Sales
WHERE Price > 50          -- filters individual rows first
GROUP BY Fruit
HAVING SUM(Price) > 200;  -- filters groups after aggregation
```

🧠 **Memory trick:**
> **WHERE works on rows. HAVING works on groups.**
> **WHERE comes before GROUP BY. HAVING comes after.**

---

## 📜 Part 7: SQL Order of Execution (Super Important!)

Even though you *write* SQL in this order:
```
SELECT → FROM → WHERE → GROUP BY → HAVING → ORDER BY
```

The database actually *executes* it in this order:

```
1. FROM       → Get the table
2. WHERE      → Filter rows
3. GROUP BY   → Form buckets
4. HAVING     → Filter buckets
5. SELECT     → Pick columns
6. ORDER BY   → Sort the result
```

🧠 **Memory trick:** *"FWGHSO"* → **F**rom, **W**here, **G**roupBy, **H**aving, **S**elect, **O**rderBy.

---

## 🎁 Part 8: Cool Tricks & Gotchas

### 1. COUNT(*) vs COUNT(column)
- `COUNT(*)` → counts all rows (including NULLs)
- `COUNT(column)` → counts only non-NULL values
- `COUNT(DISTINCT column)` → counts unique values

### 2. NULL values are ignored by aggregates
`AVG(salary)` ignores NULL salaries. So averages may surprise you!

### 3. GROUP BY without aggregate = same as DISTINCT
```sql
SELECT Fruit FROM Sales GROUP BY Fruit;
-- same as
SELECT DISTINCT Fruit FROM Sales;
```

### 4. You can use expressions in GROUP BY
```sql
SELECT YEAR(SaleDate), SUM(Price)
FROM Sales
GROUP BY YEAR(SaleDate);
```

---

## 💼 Interview Tips on GROUP BY

🎤 **Common questions you'll be asked:**

1. **"Difference between WHERE and HAVING?"**
   → WHERE filters rows before grouping; HAVING filters groups after grouping.

2. **"Can we use HAVING without GROUP BY?"**
   → Yes! It treats the whole table as one group.
   ```sql
   SELECT SUM(Price) FROM Sales HAVING SUM(Price) > 1000;
   ```

3. **"What's the difference between COUNT(*), COUNT(1), and COUNT(column)?"**
   → COUNT(*) and COUNT(1) → count all rows. COUNT(column) → ignores NULLs.

4. **"Can you use aggregate in WHERE?"**
   → ❌ No! Aggregates can't be in WHERE because grouping hasn't happened yet. Use HAVING.

5. **"Order of execution of SQL clauses?"**
   → FROM → WHERE → GROUP BY → HAVING → SELECT → ORDER BY → LIMIT.

6. **"Find the 2nd highest salary per department"** → Classic GROUP BY + window function question.

---

## 🏆 The 30-Second Summary

> **GROUP BY** = Put similar rows into buckets.
> **Aggregate functions** = Do math on each bucket (SUM, COUNT, AVG, MIN, MAX).
> **WHERE** filters rows *before* grouping; **HAVING** filters buckets *after* grouping.
> **Rule:** Every column in SELECT must be in GROUP BY or inside an aggregate.

---
