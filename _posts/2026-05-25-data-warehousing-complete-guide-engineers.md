---
title: "Data Warehousing Complete Guide for Data Engineers"
description: "A complete data warehousing guide for data engineers covering
  architecture, ETL vs ELT, schema design, BigQuery, and modern
  warehouse best practices."
keywords:
  - data warehousing complete guide
  - data warehouse for data engineers
  - data warehouse architecture explained
  - ETL vs ELT data pipeline
  - modern data warehouse design
  - BigQuery data warehouse tutorial
  - data warehouse vs data lake
  - OLAP vs OLTP differences
  - cloud data warehouse GCP
  - data engineering warehouse concepts
categories: [Data Engineering,DBMS]
tags:
  - data-warehousing
  - data-engineering
  - bigquery
  - etl
  - elt
  - olap
  - gcp
  - database-design
author: kedarkhedkar
date: 2026-05-25 10:00:00 +0000
slug: data-warehousing-complete-guide-engineers
canonical_url: https://kedar-khedkar.github.io/posts/data-warehousing-complete-guide-engineers
image:

og_title: "Data Warehousing Complete Guide for Data Engineers"
og_description: "Everything a data engineer needs to know about data warehousing
  — architecture, ETL/ELT, schema design, BigQuery & more."
og_type: article
og_url: https://kedar-khedkar.github.io/posts/data-warehousing-complete-guide-engineers

twitter_card: summary_large_image
twitter_title: "Data Warehousing Complete Guide for Data Engineers"
twitter_description: "Everything about data warehousing in one post — architecture,
  ETL vs ELT, schema design, BigQuery & GCP. Bookmark this!"
twitter_site: "@khedkar_kedar"
---

# 🏢 Data Warehousing — Complete Guide for Data Engineers

## 1. What is a Data Warehouse?

A **Data Warehouse (DWH)** is a **centralized repository** that stores large volumes of structured, historical data from multiple sources, optimized for **analytical queries and reporting**.

> **Simple Analogy:** If databases are like **individual shop registers**, a data warehouse is like the **company's central accounting office** — it collects all data, organizes it, and helps leadership make decisions.

---

## 2. Data Warehouse vs Database vs Data Lake vs Data Lakehouse

| Feature | Database (OLTP) | Data Warehouse (OLAP) | Data Lake | Data Lakehouse |
|---|---|---|---|---|
| **Purpose** | Day-to-day operations | Analytics & Reporting | Store everything raw | Analytics on raw + structured |
| **Data Type** | Structured | Structured | Structured + Semi + Unstructured | All types |
| **Schema** | Schema-on-Write | Schema-on-Write | Schema-on-Read | Schema-on-Read + Write |
| **Query Pattern** | Simple CRUD (INSERT, UPDATE) | Complex aggregations, joins | Batch/ML processing | Both OLAP + ML |
| **Users** | App developers, end users | Analysts, BI teams, Data Engineers | Data Scientists, ML Engineers | Everyone |
| **Examples** | PostgreSQL, MySQL | Snowflake, Redshift, BigQuery | S3 + Hive, ADLS | Databricks, Apache Iceberg, Delta Lake |
| **Data Volume** | GBs | TBs to PBs | PBs+ | PBs+ |
| **Freshness** | Real-time | Periodic loads (batch/near real-time) | Raw ingestion | Near real-time |

> 🎯 **Interview Tip:** Always be ready to explain *why* a company needs a DWH when they already have databases. Answer: **"Operational databases aren't designed for heavy analytical queries — running reports on them would slow down the application."**

---

## 3. Core Characteristics of a Data Warehouse

> **Mnemonic: "SINT" — Subject-oriented, Integrated, Non-volatile, Time-variant**
> *(coined by Bill Inmon, the "Father of Data Warehousing")*

| Property | Meaning | Example |
|---|---|---|
| **S**ubject-Oriented | Organized by business subjects, not applications | "Sales," "Customers," "Products" — not "App1_DB," "App2_DB" |
| **I**ntegrated | Data from multiple sources is cleaned & unified | Customer from CRM + ERP + Website → single customer view |
| **N**on-Volatile | Once loaded, data is not frequently changed/deleted | Historical records are preserved for trend analysis |
| **T**ime-Variant | Data is stored with a time dimension | You can query sales for Q1 2024 vs Q1 2025 |

---

## 4. Data Warehouse Architecture

### 🔹 The Classic 3-Tier Architecture

```text
┌─────────────────────────────────────────────────────────────────┐
│                        TIER 3: FRONT END                        │
│         BI Tools / Dashboards / Reports / Ad-hoc Queries        │
│        (Tableau, Power BI, Looker, Metabase, Superset)          │
└──────────────────────────┬──────────────────────────────────────┘
                           │ SQL / ODBC / API
┌──────────────────────────▼──────────────────────────────────────┐
│                     TIER 2: DATA WAREHOUSE                      │
│                                                                 │
│  ┌──────────┐    ┌──────────────────┐    ┌──────────────────┐   │
│  │ Staging  │───▶│  Core Warehouse  │───▶│   Data Marts     │   │
│  │  Area    │    │  (Fact + Dims)   │    │ (Sales, Finance) │   │
│  └──────────┘    └──────────────────┘    └──────────────────┘   │
│                                                                 │
└──────────────────────────▲──────────────────────────────────────┘
                           │ ETL / ELT
┌──────────────────────────┴──────────────────────────────────────┐
│                     TIER 1: DATA SOURCES                        │
│       CRM  |  ERP  |  Flat Files  |  APIs  |  Logs  |  IoT     │
└─────────────────────────────────────────────────────────────────┘
```

### Key Layers Explained

| Layer | What Happens Here |
|---|---|
| **Staging Area** | Raw data lands here first; temporary; cleansing & deduplication |
| **Core Warehouse (ODS/DWH)** | Clean, integrated, modeled data (facts + dimensions) |
| **Data Marts** | Subset of DWH for specific departments (Sales Mart, Finance Mart) |

---

## 5. ETL vs ELT

> **Mnemonic: "Where does **T**ransformation happen?"**

```text
ETL:  Source → [Transform] → Load into DWH     (Traditional)
ELT:  Source → Load into DWH → [Transform]      (Modern/Cloud)
```

| Aspect | ETL | ELT |
|---|---|---|
| **Transform Location** | Outside DWH (ETL server) | Inside DWH (uses DWH compute) |
| **Speed** | Slower (separate processing) | Faster (leverages DWH power) |
| **Best For** | On-premise, smaller data | Cloud DWH (Snowflake, BigQuery) |
| **Tools** | Informatica, Talend, SSIS | dbt, Snowflake SQL, Spark |
| **Cost** | Separate infra needed | Pay for DWH compute |

> 🎯 **Modern Trend:** ELT is dominant in cloud-native warehouses. **dbt (data build tool)** has become the industry standard for the "T" in ELT.

---

## 6. Schema Design Patterns (The Heart of DWH)

### ⭐ Star Schema
> **Most common. Easy to understand. Fast queries.**

```text
                    ┌──────────────┐
                    │ dim_product  │
                    │──────────────│
                    │ product_id   │
                    │ product_name │
                    │ category     │
                    └──────┬───────┘
                           │
┌──────────────┐   ┌──────▼───────────┐   ┌──────────────┐
│ dim_customer │   │  fact_sales      │   │  dim_date    │
│──────────────│   │──────────────────│   │──────────────│
│ customer_id  │◄──│ customer_id (FK) │   │ date_id      │
│ name         │   │ product_id  (FK) │──▶│ date         │
│ city         │   │ date_id     (FK) │   │ month        │
└──────────────┘   │ store_id    (FK) │   │ quarter      │
                   │──────────────────│   │ year         │
                   │ quantity         │   └──────────────┘
                   │ revenue          │
                   │ discount         │   ┌──────────────┐
                   └──────┬───────────┘   │  dim_store   │
                          │               │──────────────│
                          └──────────────▶│ store_id     │
                                          │ store_name   │
                                          │ region       │
                                          └──────────────┘
```

**Characteristics:**
- **Fact Table** (center) = Measurable events (sales, clicks, transactions)
- **Dimension Tables** (points of the star) = Descriptive context (who, what, when, where)
- Denormalized dimensions → fewer JOINs → **fast queries**

---

### ❄️ Snowflake Schema
> **Normalized dimensions — dimensions have sub-dimensions**

```text
┌────────────┐    ┌──────────────┐    ┌──────────────┐
│ dim_city   │◄───│ dim_customer │◄───│  fact_sales  │
│────────────│    │──────────────│    │──────────────│
│ city_id    │    │ customer_id  │    │ customer_id  │
│ city_name  │    │ name         │    │ product_id   │
│ state_id   │──▶ │ city_id (FK) │    │ revenue      │
└────────────┘    └──────────────┘    └──────────────┘
      │
      ▼
┌────────────┐
│ dim_state  │
│────────────│
│ state_id   │
│ state_name │
│ country    │
└────────────┘
```

**Characteristics:**
- Dimensions are **normalized** (broken into sub-tables)
- Saves storage, reduces redundancy
- **More JOINs** → slightly slower queries
- Used when dimension tables are very large

---

### 🏛️ Star vs Snowflake — Quick Comparison

| Aspect | Star Schema | Snowflake Schema |
|---|---|---|
| **Dimension Tables** | Denormalized (flat) | Normalized (hierarchical) |
| **Query Performance** | ⚡ Faster (fewer joins) | 🐢 Slower (more joins) |
| **Storage** | More (redundant data) | Less (no redundancy) |
| **Complexity** | Simple | More complex |
| **Best For** | BI/Reporting, most DWH | Very large dimensions, strict storage needs |
| **Industry Preference** | ✅ Most common | Less common |

---

### 🗄️ Data Vault 2.0
> **For enterprise-scale, audit-heavy environments**

| Component | Purpose | Example |
|---|---|---|
| **Hub** | Business keys (unique identifiers) | `hub_customer` → customer_bk |
| **Link** | Relationships between hubs | `link_customer_order` |
| **Satellite** | Descriptive attributes + history | `sat_customer_details` (name, address, load_date) |

**When to use:** Large enterprises needing full audit trails, parallel loading, and agility.

---

## 7. Fact Table Types

> **Mnemonic: "TAS" — Transaction, Accumulating, Snapshot**

| Type | What it Captures | Example | Grain |
|---|---|---|---|
| **Transaction Fact** | One row per event | Each sale, each click | Individual event |
| **Periodic Snapshot** | State at regular intervals | Monthly account balance, daily inventory | One row per period |
| **Accumulating Snapshot** | Lifecycle of a process | Order lifecycle (placed → shipped → delivered) | One row per entity, updated over time |
| **Factless Fact** | Events with no measures | Student attended class (just FKs, no metrics) | Event occurrence |

---

## 8. Slowly Changing Dimensions (SCD)

> **Mnemonic: "How do you handle change?"**

| SCD Type | Strategy | Example | Pros | Cons |
|---|---|---|---|---|
| **Type 0** | No change ever | Date of birth | Simple | No history |
| **Type 1** | Overwrite old value | Update address directly | Simple, current data | ❌ Loses history |
| **Type 2** | Add new row with versioning | New row with `start_date`, `end_date`, `is_current` | ✅ Full history | Table grows large |
| **Type 3** | Add new column | `current_city`, `previous_city` | Limited history | Only 1 level of history |
| **Type 4** | Separate history table | Current in main, history in another table | Clean separation | More complexity |
| **Type 6** | Hybrid (1 + 2 + 3) | Combines overwrite + new row + extra columns | Flexible | Complex to implement |

> 🎯 **Interview Favorite:** SCD Type 2 is the **most commonly asked**. Be ready to write SQL that implements it.

---

## 9. Key Measures & Concepts

### Grain
> **"What does one row in the fact table represent?"**

This is the **most important decision** in DWH design.

- ✅ "One row per transaction per product per store per day"
- ❌ Vague grain → broken reports

### Additive vs Non-Additive Measures

| Type | Can Sum Across All Dimensions? | Example |
|---|---|---|
| **Additive** | ✅ Yes | Revenue, Quantity |
| **Semi-Additive** | ⚠️ Some dimensions only | Account Balance (sum across customers, NOT across time) |
| **Non-Additive** | ❌ No | Ratios, Percentages, Unit Price |

---

## 10. Modern Data Warehouse Platforms

| Platform | Provider | Key Strength |
|---|---|---|
| **Snowflake** | Independent | Separation of storage & compute, auto-scaling |
| **BigQuery** | Google Cloud | Serverless, great for petabyte-scale analytics |
| **Redshift** | AWS | Deep AWS integration, Spectrum for data lake queries |
| **Azure Synapse** | Microsoft | Unified analytics, integrates with Power BI |
| **Databricks SQL** | Databricks | Lakehouse pattern, Delta Lake, Spark-powered |

---

## 11. Important Design Principles

```text
┌────────────────────────────────────────────────┐
│         DATA WAREHOUSE DESIGN RULES            │
├────────────────────────────────────────────────┤
│ 1. Define the GRAIN first                      │
│ 2. Choose dimensions before facts              │
│ 3. Use surrogate keys (not business keys) in   │
│    fact tables                                  │
│ 4. Prefer Star Schema unless you have a reason │
│    not to                                      │
│ 5. Make dimensions wide (many columns) and     │
│    facts tall (many rows)                      │
│ 6. Handle NULLs with "Unknown" dimension rows  │
│ 7. Design for query patterns, not source       │
│    systems                                     │
│ 8. Implement SCD Type 2 for critical           │
│    dimensions                                  │
│ 9. Partition fact tables by date               │
│10. Document everything — lineage matters       │
└────────────────────────────────────────────────┘
```

---

## 12. Inmon vs Kimball — The Two Schools of Thought

| Aspect | Bill Inmon (Top-Down) | Ralph Kimball (Bottom-Up) |
|---|---|---|
| **Approach** | Build enterprise DWH first → then data marts | Build data marts first → integrate into DWH |
| **Schema** | 3NF (Normalized) | Star Schema (Denormalized) |
| **Time to Value** | Slower (big upfront investment) | Faster (iterative delivery) |
| **Complexity** | Higher initial complexity | Simpler to start |
| **Best For** | Large enterprises with clear data strategy | Agile teams, business-driven analytics |
| **Modern Trend** | Less common standalone | ✅ More widely adopted |

> 🎯 **Interview Answer:** *"Most modern cloud warehouses follow Kimball's dimensional modeling approach with Star Schemas, because it's optimized for analytical query patterns and BI tool compatibility."*

---

## 13. 🎯 Interview Questions to Prepare

| # | Question |
|---|---|
| 1 | What is a Data Warehouse and why do we need one? |
| 2 | Explain Star Schema vs Snowflake Schema with examples |
| 3 | What are Fact tables and Dimension tables? |
| 4 | Explain SCD Types 1, 2, and 3 with SQL examples |
| 5 | What is the grain of a fact table and why does it matter? |
| 6 | ETL vs ELT — when would you choose each? |
| 7 | Inmon vs Kimball approach? |
| 8 | What are additive, semi-additive, and non-additive facts? |
| 9 | How do you handle late-arriving facts/dimensions? |
| 10 | What is a Data Mart vs Data Warehouse? |
| 11 | Explain Data Vault modeling |
| 12 | How do you optimize query performance in a DWH? |
| 13 | What is a surrogate key and why use it? |
| 14 | Explain partitioning and clustering in Snowflake/BigQuery |
| 15 | What is a Junk Dimension? Degenerate Dimension? Role-Playing Dimension? |

---

## 14. 💡 Quick-Reference Cheat Sheet

```text
┌──────────────────────────────────────────────────────┐
│              DWH CHEAT SHEET                         │
├──────────────────────────────────────────────────────┤
│ OLTP = Operations | OLAP = Analytics                 │
│ Fact = Measures (numbers) | Dim = Context (who/what) │
│ Star = Denormalized dims | Snowflake = Normalized    │
│ SCD2 = Full history (most used)                      │
│ Grain = What 1 row represents                        │
│ Surrogate Key = System-generated (not business key)  │
│ ETL = Transform outside DWH | ELT = Transform inside │
│ Kimball = Star + Bottom-up | Inmon = 3NF + Top-down  │
│ Partition by DATE | Cluster by HIGH-CARDINALITY cols  │
│ dbt = Modern "T" in ELT                             │
└──────────────────────────────────────────────────────┘
```

---

> **🚀 Final Tip:** Data warehousing interviews test your ability to **design, not just define**. Practice drawing schemas on a whiteboard, writing SCD Type 2 SQL, and explaining *trade-offs* for every design decision you make. Good luck!