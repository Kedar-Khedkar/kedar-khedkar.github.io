---
title: "SCD Types Explained: Slowly Changing Dimensions Guide"
description: "Understand SCD Types 0, 1, 2, 3 and 6 in data engineering.
  Covers implementation strategies, SQL examples, and when
  to use each in modern data warehouses."
keywords:
  - SCD types data engineering
  - slowly changing dimensions explained
  - SCD type 1 2 3 differences
  - slowly changing dimension type 2 sql
  - SCD implementation data warehouse
  - SCD type 2 BigQuery example
  - slowly changing dimensions dbt
  - dimension history tracking sql
  - SCD types interview questions
  - data warehouse dimension modeling SCD
categories:
  - DBMS
  - GCP Data Engineering
tags:
  - scd
  - data-warehousing
  - dimensional-modeling
  - data-engineering
  - bigquery
  - dbt
  - sql
  - etl
author: kedarkhedkar
date: 2026-07-24 10:00:00 +0000
slug: scd-types-slowly-changing-dimensions-guide
canonical_url: https://kedar-khedkar.github.io/posts/scd-types-slowly-changing-dimensions-guide
image:
    path: /assets/img/posts/scd-in-sql.png
    alt: SCD in SQL
og_title: "SCD Types Explained: Slowly Changing Dimensions Guide"
og_description: "SCD Type 0 through 6 — plain-English explanations, SQL
  examples, and BigQuery/dbt implementation tips for data engineers."
og_type: article
og_url: https://kedar-khedkar.github.io/posts/scd-types-slowly-changing-dimensions-guide

twitter_card: summary_large_image
twitter_title: "SCD Types Explained: Slowly Changing Dimensions Guide"
twitter_description: "SCD Type 1, 2, 3 & 6 finally explained with real SQL examples.
  A must-read for data engineers and warehouse designers. 📌"
twitter_site: "@khedkar_kedar"
---
# Slowly Changing Dimensions (SCD) — Made Simple 🧠

SCDs describe **how a data warehouse handles changes to dimension data over time** (like a customer changing their address, or an employee getting promoted).

Think of it like this: *"When something about a person/thing changes, what do I do with the old info?"*

---

## 🎯 The Easy Memory Trick

Imagine **"Sarah moved from New York to Los Angeles."** Here's how each SCD type reacts:

| Type | Nickname | What it does | Sarah Example |
|------|----------|--------------|---------------|
| **Type 0** | 🗿 "Frozen" / Retain | Never changes. Original stays forever. | Stays "New York" forever (e.g., birth city) |
| **Type 1** | ✏️ "Overwrite" | Replace old with new. No history. | "Los Angeles" replaces "New York" — gone! |
| **Type 2** | 📚 "Add a Row" | Keep history by adding a new row. | Two rows: one for NY (expired), one for LA (current) |
| **Type 3** | ➕ "Add a Column" | Keep limited history in extra columns. | Columns: `Current_City = LA`, `Previous_City = NY` |
| **Type 4** | 🗄️ "History Table" | Current data in main table; history in a separate table. | Main: LA. History table: NY record |
| **Type 6** | 🎯 "1+2+3 Combo" | Hybrid (1+2+3 = 6). Best of all worlds. | New row added (Type 2) + current flag (Type 1) + previous column (Type 3) |

> 🧠 **Memory hack:** "**0 = Never, 1 = Overwrite, 2 = New Row, 3 = New Column, 4 = New Table, 6 = Combo (1+2+3)**"

---

## 📊 Visual Example

**Original Row:**
```
CustID | Name  | City     
101    | Sarah | New York 
```

### Type 1 (Overwrite)
```
101 | Sarah | Los Angeles   ← History lost
```

### Type 2 (Add Row + flags)
```
SurrogateKey | CustID | Name  | City        | StartDate  | EndDate    | IsCurrent
1            | 101    | Sarah | New York    | 2020-01-01 | 2024-05-01 | N
2            | 101    | Sarah | Los Angeles | 2024-05-02 | 9999-12-31 | Y
```

### Type 3 (Add Column)
```
101 | Sarah | Current_City=LA | Previous_City=NY
```

### Type 4 (Separate History Table)
```
Main:    101 | Sarah | LA
History: 101 | NY | 2020–2024
```

### Type 6 (Hybrid)
```
SK | CustID | Name  | Hist_City | Curr_City | Start      | End        | Flag
1  | 101    | Sarah | NY        | LA        | 2020-01-01 | 2024-05-01 | N
2  | 101    | Sarah | LA        | LA        | 2024-05-02 | 9999-12-31 | Y
```

---

## 💼 Interview Tips & Tricks

### 🔥 Most Common Questions
1. **"Which SCD type is most commonly used in real projects?"**
   → **Type 2** — because businesses usually want full historical tracking for analytics (e.g., "What were our sales by region *as of last year*?").

2. **"Difference between Type 1 and Type 2?"**
   → Type 1 **overwrites** (no history). Type 2 **adds a new row** (full history preserved).

3. **"How do you implement SCD Type 2?"**
   → Use a **surrogate key** (not the natural/business key) + columns like `Start_Date`, `End_Date`, `Is_Current` flag, or `Version_Number`.

4. **"Why surrogate keys for Type 2?"**
   → Because the natural key (e.g., CustID 101) repeats across rows. Surrogate key uniquely identifies each *version*.

5. **"What's the disadvantage of Type 2?"**
   → Table grows large quickly → storage + slower queries. Needs careful indexing.

6. **"When would you use Type 3?"**
   → When you only need to track **previous + current** (limited history). E.g., a sales rep's previous and current territory.

---

### 🎤 Pro Interview Tips

✅ **Always give a real-world example.** Don't just define — say *"For example, if a customer's address changes..."*

✅ **Mention surrogate keys + active flag/date range** when explaining Type 2 — interviewers love it.

✅ **Tie it to business requirement.** Say: *"The SCD type depends on whether the business needs history. If yes → Type 2. If not → Type 1."*

✅ **Know the trade-offs:**
   - Type 1 → Simple but no history.
   - Type 2 → Full history but bigger table.
   - Type 3 → Limited history, fixed columns.
   - Type 4 → Clean separation, but joins needed.
   - Type 6 → Powerful but complex to maintain.

✅ **Tools mention:** If asked about implementation — mention **Informatica, SSIS, dbt, Talend, Snowflake streams/MERGE statements, or PySpark MERGE INTO** depending on your stack.

✅ **MERGE / UPSERT statement knowledge** is a huge plus — interviewers often ask you to write SQL for SCD2 logic.

---

### ⚡ Quick One-Liner Cheat Sheet (memorize these!)

> - **Type 0:** "Never change it."
> - **Type 1:** "Overwrite, no history."
> - **Type 2:** "New row, full history." ⭐ *most asked*
> - **Type 3:** "New column, limited history."
> - **Type 4:** "History in a separate table."
> - **Type 6:** "1 + 2 + 3 = Hybrid power."

---

### 🧩 Bonus Trick Question
**"What's SCD Type 5?"**
→ It's **Type 4 + Type 1** = Mini-dimension + outrigger. Rarely asked but knowing it impresses interviewers. (4 + 1 = 5 😉)
