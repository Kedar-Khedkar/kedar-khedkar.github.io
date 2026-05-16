---
title: "DBMS Fundamentals: Core Concepts Every Dev Must Know"
description: "A beginner-friendly guide to DBMS fundamentals covering keys,
  normalization, transactions, ACID properties, and SQL basics with clear examples."
keywords:
  - dbms fundamentals
  - database management system basics
  - dbms concepts for beginners
  - ACID properties explained
  - normalization in dbms
  - primary key foreign key dbms
  - dbms interview concepts
  - relational database fundamentals
  - sql and dbms basics guide
  - database transactions explained
categories: [Data Engineering,DBMS]
tags:
  - dbms
  - relational-database
  - sql
  - normalization
  - acid-properties
  - database-design
  - beginner-guide
author: kedarkhedkar
date: 2026-05-16 10:00:00 +0000
slug: dbms-fundamentals-core-concepts-guide
canonical_url: https://kedar-khedkar.github.io/posts/dbms-fundamentals-core-concepts-guide
image:

og_title: "DBMS Fundamentals: Core Concepts Every Dev Must Know"
og_description: "From ACID to normalization — a plain-English guide to DBMS
  fundamentals every developer and data engineer should know."
og_type: article
og_url: https://kedar-khedkar.github.io/posts/dbms-fundamentals-core-concepts-guide

twitter_card: summary_large_image
twitter_title: "DBMS Fundamentals: Core Concepts Every Dev Must Know"
twitter_description: "ACID, normalization, keys, transactions — all the DBMS
  basics explained simply. Save this for your next interview prep!"
twitter_site: "@khedkar_kedar"
---

# 📚 DBMS Fundamentals for Data Engineers — Interview Prep Guide

## 1. What is a DBMS?

A **Database Management System (DBMS)** is software that stores, retrieves, and manages data efficiently. Think of it as a **smart librarian** — it knows where everything is, who can access what, and keeps things organized.

> **Easy mnemonic: "ACID keeps data SOLID"**

---

## 2. Types of DBMS

| Type                   | Description                                            | Examples                         | When to Use                             |
| ---------------------- | ------------------------------------------------------ | -------------------------------- | --------------------------------------- |
| **Relational (RDBMS)** | Tables with rows & columns, SQL-based                  | PostgreSQL, MySQL, Oracle        | Structured data, OLTP                   |
| **NoSQL**              | Flexible schema (document, key-value, graph, columnar) | MongoDB, Redis, Cassandra, Neo4j | Unstructured/semi-structured, scale-out |
| **NewSQL**             | SQL + horizontal scalability                           | CockroachDB, Google Spanner      | Best of both worlds                     |
| **Columnar/OLAP**      | Column-oriented storage                                | Snowflake, BigQuery, Redshift    | Analytics, Data Warehousing             |

---

## 3. Core Concepts (The "Big 5" Pillars)

### 🏛️ Pillar 1: ACID Properties

> **Mnemonic: "A Cat In Darkness"**

| Property        | Meaning                                                            | Real-world Analogy                                           |
| --------------- | ------------------------------------------------------------------ | ------------------------------------------------------------ |
| **A**tomicity   | All or nothing — a transaction fully completes or fully rolls back | Bank transfer: both debit AND credit happen, or neither does |
| **C**onsistency | Data moves from one valid state to another                         | Account balance can't go negative if rules say so            |
| **I**solation   | Concurrent transactions don't interfere                            | Two people booking the last seat — only one wins             |
| **D**urability  | Once committed, data survives crashes                              | After "payment successful," it stays even if server crashes  |

---

### 🏛️ Pillar 2: Normalization

> **Mnemonic: "No Redundant Data Allowed"**

| Normal Form | Rule (simplified)                                                              | Fix                                             |
| ----------- | ------------------------------------------------------------------------------ | ----------------------------------------------- |
| **1NF**     | No repeating groups, atomic values                                             | Split multi-value cells into rows               |
| **2NF**     | 1NF + No partial dependency (every non-key depends on the *whole* primary key) | Move partially dependent columns to a new table |
| **3NF**     | 2NF + No transitive dependency (non-key shouldn't depend on another non-key)   | Remove columns that depend on non-key columns   |
| **BCNF**    | Every determinant is a candidate key                                           | Stricter version of 3NF                         |

> 🎯 **Data Engineer Tip:** In practice, data warehouses often use **denormalized** schemas (Star/Snowflake) for read performance. Know *when to normalize* (OLTP) vs *when to denormalize* (OLAP).

---

### 🏛️ Pillar 3: Keys
>
> **Mnemonic: "PCFUSA" — "Please Can Friends Understand SQL Already"**

| Key               | Purpose                                          |
| ----------------- | ------------------------------------------------ |
| **P**rimary Key   | Uniquely identifies each row                     |
| **C**andidate Key | All columns that *could* be a primary key        |
| **F**oreign Key   | Links to another table's primary key             |
| **U**nique Key    | Like PK but allows one NULL                      |
| **S**uper Key     | Any set of columns that uniquely identifies rows |
| **A**lternate Key | Candidate keys not chosen as PK                  |

---

### 🏛️ Pillar 4: Joins & Set Operations
> **Mnemonic: Visualize a Venn Diagram**

```text
  ┌───────┐
  │  A    ∩│  B   │
  │       ││      │
  └───────┘

  INNER JOIN  = Only the intersection (∩)
  LEFT JOIN   = All of A + matching B
  RIGHT JOIN  = All of B + matching A
  FULL JOIN   = Everything from A and B
  CROSS JOIN  = Every row of A × every row of B (Cartesian product)
```

---

### 🏛️ Pillar 5: Indexing

> **Mnemonic: "Index = Book's Table of Contents"**

| Index Type        | How it works               | Best for                                     |
| ----------------- | -------------------------- | -------------------------------------------- |
| **B-Tree**        | Balanced tree, sorted      | Range queries, equality (`WHERE age > 25`)   |
| **Hash**          | Hash function lookup       | Exact match (`WHERE id = 42`)                |
| **Bitmap**        | Bit arrays per value       | Low-cardinality columns (`gender`, `status`) |
| **Composite**     | Multi-column index         | Queries filtering on multiple columns        |
| **Clustered**     | Physically reorders data   | One per table, primary key lookups           |
| **Non-Clustered** | Separate pointer structure | Multiple per table, secondary lookups        |

---

## 4. Data Engineer-Specific Must-Knows

### 📦 Schema Design Patterns

| Pattern              | Use Case                                                        |
| -------------------- | --------------------------------------------------------------- |
| **Star Schema**      | Fact table + dimension tables (simple, fast reads)              |
| **Snowflake Schema** | Normalized dimensions (saves space, more joins)                 |
| **Data Vault**       | Hub + Link + Satellite (auditable, scalable for enterprise DWH) |

### 🔄 Transactions & Concurrency Control

| Concept                   | What to Know                                                                               |
| ------------------------- | ------------------------------------------------------------------------------------------ |
| **WAL (Write-Ahead Log)** | Changes are logged *before* being applied — crash recovery                                 |
| **MVCC**                  | Multiple versions of data for concurrent reads without locking (PostgreSQL, Snowflake)     |
| **Locking**               | Shared (read) vs Exclusive (write) locks; know **deadlocks**                               |
| **Isolation Levels**      | Read Uncommitted → Read Committed → Repeatable Read → Serializable (increasing strictness) |

### ⚡ Query Optimization

- **EXPLAIN / EXPLAIN ANALYZE** — Always check query plans
- **Partitioning** — Split large tables (by date, region, etc.)
- **Sharding** — Distribute data across machines
- **Materialized Views** — Pre-computed query results
- **Query pushdown** — Push filters close to storage (important in Spark/BigQuery)

---

## 5. CAP Theorem (Distributed Systems)

> **Mnemonic: "Pick 2 out of 3"**

| Property                | Meaning                             |
| ----------------------- | ----------------------------------- |
| **C**onsistency         | Every read gets the latest write    |
| **A**vailability        | Every request gets a response       |
| **P**artition Tolerance | System works despite network splits |

- **CP:** MongoDB, HBase (sacrifice availability)
- **AP:** Cassandra, DynamoDB (sacrifice consistency)
- **CA:** Traditional RDBMS (single-node, no partition tolerance)

---

## 6. 🎯 Interview Prep Strategy

### ✅ Top 20 Questions to Prepare

| #   | Question                                                    |
| --- | ----------------------------------------------------------- |
| 1   | What are ACID properties? Give a real-world example.        |
| 2   | Difference between SQL and NoSQL databases?                 |
| 3   | Explain normalization (1NF through BCNF) with examples.     |
| 4   | When would you denormalize?                                 |
| 5   | Star schema vs Snowflake schema?                            |
| 6   | What is a clustered vs non-clustered index?                 |
| 7   | How does a B-Tree index work internally?                    |
| 8   | Explain isolation levels with examples.                     |
| 9   | What is MVCC?                                               |
| 10  | How do you optimize a slow SQL query?                       |
| 11  | What is partitioning vs sharding?                           |
| 12  | Explain the CAP theorem.                                    |
| 13  | What is a deadlock? How to prevent it?                      |
| 14  | Difference between DELETE, TRUNCATE, and DROP?              |
| 15  | What are window functions? (ROW_NUMBER, RANK, LEAD/LAG)     |
| 16  | Explain CTEs and recursive CTEs.                            |
| 17  | What is a materialized view vs a regular view?              |
| 18  | What is database replication? (Master-Slave, Master-Master) |
| 19  | Explain slowly changing dimensions (SCD Type 1, 2, 3).      |
| 20  | What is a transaction log / WAL?                            |

### 📅 7-Day Study Plan

| Day   | Focus Area                                  | Action                                         |
| ----- | ------------------------------------------- | ---------------------------------------------- |
| **1** | ACID, Keys, Normalization                   | Memorize with examples                         |
| **2** | SQL Joins, Subqueries, CTEs                 | Write 10 queries on LeetCode/HackerRank        |
| **3** | Indexing & Query Optimization               | Practice EXPLAIN plans on a sample DB          |
| **4** | Schema Design (Star, Snowflake, Data Vault) | Draw diagrams from scratch                     |
| **5** | Transactions, Locking, Isolation Levels     | Walk through concurrency scenarios             |
| **6** | CAP Theorem, Replication, Sharding          | Compare real databases (Postgres vs Cassandra) |
| **7** | Mock Interview                              | Answer all 20 questions aloud in 2 min each    |

### 🛠️ Practice Resources

- **SQL Practice:** LeetCode (Database section), HackerRank SQL, StrataScratch
- **System Design:** "Designing Data-Intensive Applications" by Martin Kleppmann (the **bible** for Data Engineers)
- **Hands-on:** Set up PostgreSQL locally, load sample data, and practice EXPLAIN ANALYZE

---

## 💡 Golden Rules for Interviews

1. **Always give examples** — Don't just define; illustrate with a scenario
2. **Think aloud** — Walk the interviewer through your reasoning
3. **Trade-offs matter** — "It depends on…" is often the right start
4. **Know your resume** — If you listed Snowflake/Spark/Redshift, you'll be asked about it
5. **Ask clarifying questions** — Great engineers don't assume

> **Remember:** As a Data Engineer, interviewers test you on **practical application** (schema design, query tuning, pipeline reliability) more than pure theory. Always tie concepts back to real-world data pipeline scenarios. Good luck! 🚀