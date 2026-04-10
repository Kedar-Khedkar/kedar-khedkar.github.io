---
title: "ACID Properties in DBMS: Simple Guide & Interview Prep"
description: "Understand ACID properties (Atomicity, Consistency, Isolation, Durability) in layman's terms. Master database transactions for data engineering interviews."
author: kedarkhedkar
date: 2026-04-10 13:56:00 +0530
categories: [Data Engineering, Database Management]
tags: [acid properties, dbms, interview prep, database transactions, sql]
pin: false
image:
    path: /assets/img/posts/acid-properties-explained.png
    alt: Acid properties explained
---

# ACID Properties – Complete Interview Guide 🎯

ACID properties are the **backbone of reliable database transactions**. This is a **must-know topic** for Data Engineering, Backend, Database, and System Design interviews.

---

## 🧠 What are ACID Properties? (The Elevator Pitch)

> *"ACID is a set of **4 guarantees** that ensure database transactions are processed **reliably**, even in the face of errors, crashes, or concurrent access."*

---

## 🏦 The Real-World Analogy — Bank Transfer

Let's use a **₹5,000 transfer from Kedar → Raj** as our running example throughout:

```text
    Kedar's Account              Raj's Account
    Balance: ₹20,000            Balance: ₹10,000
    
    Step 1: Debit  Kedar  → ₹20,000 - ₹5,000 = ₹15,000
    Step 2: Credit Raj    → ₹10,000 + ₹5,000 = ₹15,000
    
    ✅ Both steps MUST succeed together
    ❌ If only Step 1 runs → ₹5,000 disappears into thin air!
```

This is exactly what ACID prevents. Let's break it down:

---

## 🔤 A.C.I.D — The 4 Properties

```text
┌─────────────────────────────────────────────────────────┐
│                    A.C.I.D                              │
├──────────────┬──────────────┬─────────────┬─────────────┤
│  Atomicity   │ Consistency  │  Isolation  │ Durability  │
├──────────────┼──────────────┼─────────────┼─────────────┤
│  "All or     │ "Valid state │"Transactions│ "Once saved,│
│   Nothing"   │  to valid    │  don't      │  it stays   │
│              │  state"      │  interfere" │  saved"     │
├──────────────┼──────────────┼─────────────┼─────────────┤
│  💣 Bomb     │  📏 Rules    │  🧱 Walls   │  💾 Cement  │
│  defusal     │  of the game │  between    │  it in      │
│  analogy     │              │  players    │  stone      │
└──────────────┴──────────────┴─────────────┴─────────────┘
```

---

## 🔹 A — Atomicity ("All or Nothing") 💣

> *"Either **ALL** operations in a transaction succeed, or **NONE** of them do. There's no in-between."*

### The Analogy
>
> *"Like defusing a bomb — you either cut ALL the right wires, or the whole thing resets. You can't leave it half-defused."*

### Example

```text
BEGIN TRANSACTION;
    UPDATE accounts SET balance = balance - 5000 WHERE user = 'Kedar';  -- Step 1 ✅
    UPDATE accounts SET balance = balance + 5000 WHERE user = 'Raj';    -- Step 2 ❌ ERROR!
ROLLBACK;   -- ⬅️ Step 1 is also UNDONE. Kedar gets his money back.
```

```text
✅ SUCCESS Scenario:                    ❌ FAILURE Scenario:
                                        
Step 1: Debit Kedar   ✅               Step 1: Debit Kedar   ✅
Step 2: Credit Raj    ✅               Step 2: Credit Raj    ❌ (crash!)
COMMIT ✅                               ROLLBACK ↩️ (Step 1 undone!)
                                        
Kedar: ₹15,000                         Kedar: ₹20,000 (restored!)
Raj:   ₹15,000                         Raj:   ₹10,000 (unchanged)
```

### How Databases Implement It

| Mechanism | How It Works |
|-----------|-------------|
| **Write-Ahead Log (WAL)** | Log all changes BEFORE applying them. On crash, replay or undo from log |
| **Undo Log** | Records the old values so changes can be rolled back |
| **Shadow Paging** | Writes to a copy; swap pointers only on commit |

- **Interview tip:** *"Atomicity = a transaction is an indivisible unit. If any part fails, the entire transaction rolls back. Think of it as a single atomic operation."*

---

## 🔹 C — Consistency ("Valid State → Valid State") 📏

> *"A transaction takes the database from one **valid state** to another **valid state**. All rules, constraints, and invariants are maintained."*

### The Analogy
>
> *"Like the rules of chess — every move must result in a **legal board position**. You can't end a move with two kings on the same square."*

### Example

```text
RULE: Total money in the system must ALWAYS = ₹30,000
      (Kedar ₹20,000 + Raj ₹10,000 = ₹30,000)

Before Transaction:
    Kedar: ₹20,000 + Raj: ₹10,000 = ₹30,000 ✅

After Transaction:
    Kedar: ₹15,000 + Raj: ₹15,000 = ₹30,000 ✅

❌ INVALID State (Consistency Violated):
    Kedar: ₹15,000 + Raj: ₹10,000 = ₹25,000 ❌ (₹5,000 vanished!)
```

### What Consistency Enforces

```text
┌──────────────────────────────────────────────────────┐
│              CONSISTENCY CHECKS                       │
├────────────────────┬─────────────────────────────────┤
│ Primary Keys       │ No duplicates, not null          │
│ Foreign Keys       │ References must exist            │
│ Unique Constraints │ No duplicate emails, usernames   │
│ Check Constraints  │ age > 0, balance >= 0            │
│ Triggers           │ Custom business rules            │
│ Data Types         │ INT field can't hold "hello"     │
│ NOT NULL           │ Required fields must have values  │
│ Business Rules     │ Total money conserved            │
└────────────────────┴─────────────────────────────────┘
```

### Example — Constraint Violation

```sql
-- Constraint: balance >= 0

BEGIN TRANSACTION;
    UPDATE accounts SET balance = balance - 25000 WHERE user = 'Kedar';
    -- ❌ FAILS! balance would be ₹20,000 - ₹25,000 = -₹5,000
    -- Violates CHECK constraint: balance >= 0
ROLLBACK;
-- Database stays in valid state ✅
```

- **Interview tip:** *"Consistency is about rules. The database was valid before, and it MUST be valid after. It's the application + database working together to enforce invariants."*

---

## 🔹 I — Isolation ("Transactions Don't Interfere") 🧱

> *"Concurrent transactions execute as if they were running **one after another** (serially). One transaction can't see another's uncommitted changes."*

### The Analogy
>
> *"Like exam halls — each student (transaction) works independently. You can't peek at another student's answer sheet (uncommitted data)."*

### The Problem Without Isolation

```text
    Transaction A                    Transaction B
    (Kedar → Raj ₹5,000)           (Read Kedar's balance)
    ─────────────────               ─────────────────
    
    1. Read Kedar: ₹20,000
    2. Debit Kedar: ₹15,000
                                    3. Read Kedar: ₹15,000 ← DIRTY READ!
    4. ❌ CRASH → ROLLBACK              (This data was never committed!)
       Kedar back to ₹20,000
                                    5. Transaction B used ₹15,000 
                                       which was WRONG! 💀
```

### The 4 Read Problems

```text
┌──────────────────────────────────────────────────────────────┐
│                   READ PROBLEMS                               │
├─────────────────┬────────────────────────────────────────────┤
│ 🔴 Dirty Read   │ Reading data that was NEVER COMMITTED      │
│                 │ "Seeing someone's draft before they send"  │
├─────────────────┼────────────────────────────────────────────┤
│ 🟡 Non-Repeatable│ Same query returns DIFFERENT VALUES        │
│    Read         │ because another txn modified & committed   │
│                 │ "Price changed while you were checking out"│
├─────────────────┼────────────────────────────────────────────┤
│ 🟠 Phantom Read │ Same query returns DIFFERENT NUMBER OF ROWS│
│                 │ because another txn inserted/deleted rows  │
│                 │ "New items appeared in your search results"│
├─────────────────┼────────────────────────────────────────────┤
│ 🔵 Lost Update  │ Two txns update the same row; one          │
│                 │ overwrites the other's change              │
│                 │ "Two people editing the same doc"          │
└─────────────────┴────────────────────────────────────────────┘
```

### Detailed Examples of Each Problem

**🔴 Dirty Read:**

```text
Txn A: UPDATE balance SET ₹15,000 (not committed yet)
Txn B: SELECT balance → reads ₹15,000 ← DIRTY!
Txn A: ROLLBACK → balance is back to ₹20,000
Txn B: Already used ₹15,000... WRONG! 💀
```

**🟡 Non-Repeatable Read:**

```text
Txn A: SELECT price WHERE id=1 → ₹100
Txn B: UPDATE price SET ₹150 WHERE id=1 → COMMIT ✅
Txn A: SELECT price WHERE id=1 → ₹150 ← DIFFERENT! 😱
```

**🟠 Phantom Read:**

```text
Txn A: SELECT COUNT(*) WHERE city='Mumbai' → 5 rows
Txn B: INSERT INTO customers (city='Mumbai') → COMMIT ✅
Txn A: SELECT COUNT(*) WHERE city='Mumbai' → 6 rows ← PHANTOM! 👻
```

---

### 🔒 Isolation Levels (From Lowest to Highest)

```text
    WEAKEST                                              STRONGEST
    (Fastest)                                            (Slowest)
    ───────────────────────────────────────────────────────────────
    Read          Read           Repeatable        Serializable
    Uncommitted   Committed      Read
    
    │             │              │                 │
    │ Allows:     │ Prevents:    │ Prevents:       │ Prevents:
    │ Everything  │ Dirty Reads  │ Dirty Reads     │ ALL problems
    │             │              │ Non-Repeatable  │
    │ ⚡ Fastest   │              │ Reads           │ 🐢 Slowest
    │ ❌ Unsafe   │ 🟡 Moderate  │ 🟢 Safe         │ ✅ Safest
    ───────────────────────────────────────────────────────────────
```

### Isolation Level vs Problems Matrix

| Isolation Level | Dirty Read | Non-Repeatable Read | Phantom Read | Performance |
|-----------------|------------|---------------------|--------------|-------------|
| **Read Uncommitted** | ❌ Possible | ❌ Possible | ❌ Possible | ⚡⚡⚡⚡ Fastest |
| **Read Committed** | ✅ Prevented | ❌ Possible | ❌ Possible | ⚡⚡⚡ Fast |
| **Repeatable Read** | ✅ Prevented | ✅ Prevented | ❌ Possible | ⚡⚡ Medium |
| **Serializable** | ✅ Prevented | ✅ Prevented | ✅ Prevented | ⚡ Slowest |

### What Databases Default To

| Database | Default Isolation Level |
|----------|----------------------|
| **PostgreSQL** | Read Committed |
| **MySQL (InnoDB)** | Repeatable Read |
| **Oracle** | Read Committed |
| **SQL Server** | Read Committed |
| **SQLite** | Serializable |

- **Interview tip:** *"Isolation is a spectrum — you trade off safety for performance. Most production systems use Read Committed as a balance."*

---

## 🔹 D — Durability ("Once Saved, It Stays Saved") 💾

> *"Once a transaction is **committed**, the changes are **permanent** — even if the system crashes immediately after."*

### The Analogy
>
> *"Like carving in stone — once it's engraved, even a power cut can't erase it. Unlike writing on a whiteboard."*

### Example

```text
BEGIN TRANSACTION;
    UPDATE accounts SET balance = ₹15,000 WHERE user = 'Kedar';
    UPDATE accounts SET balance = ₹15,000 WHERE user = 'Raj';
COMMIT; ✅   ← At this exact moment, it's PERMANENT

    💥 SERVER CRASHES 0.001 seconds later!
    
    🔄 Server restarts...
    
    SELECT balance FROM accounts WHERE user = 'Kedar';
    → ₹15,000 ✅  (Still there! Not lost!)
    
    SELECT balance FROM accounts WHERE user = 'Raj';
    → ₹15,000 ✅  (Still there! Not lost!)
```

### How Databases Ensure Durability

```text
┌──────────────────────────────────────────────────────────┐
│              DURABILITY MECHANISMS                       │
├──────────────────────┬───────────────────────────────────┤
│ Write-Ahead Log (WAL)│ Changes logged to disk BEFORE     │
│                      │ applying. On crash, replay log.   │
├──────────────────────┼───────────────────────────────────┤
│ Redo Log             │ Records committed changes.        │
│                      │ Replays on recovery.              │
├──────────────────────┼───────────────────────────────────┤
│ Checkpointing        │ Periodically flush in-memory      │
│                      │ changes to disk.                  │
├──────────────────────┼───────────────────────────────────┤
│ Replication          │ Copy data to multiple nodes.      │
│                      │ If one dies, others have it.      │
├──────────────────────┼───────────────────────────────────┤
│ Battery-backed cache │ Hardware ensures write cache      |
│                      │ survives power failure.           │
└──────────────────────┴───────────────────────────────────┘
```

- **Interview tip:** *"Durability means committed = permanent. Databases achieve this through WAL (Write-Ahead Logging) and replication."*

---

## 🔄 Complete Transaction Lifecycle

```text
    ┌─────────────┐
    │   BEGIN      │
    │ TRANSACTION  │
    └──────┬──────┘
           │
           ▼
    ┌─────────────┐     ┌───────────────────────────────┐
    │  Execute    │     │  ACID in Action:              │
    │  Operations │     │                               │
    │             │     │  A: All ops tracked as a unit │
    │  - Debit    │     │  C: Constraints checked       │
    │  - Credit   │     │  I: Isolated from other txns  │
    │  - Validate │     │  D: Changes logged to WAL     │
    └──────┬──────┘     └───────────────────────────────┘
           │
       ┌───┴───┐
       │Error? │
       └───┬───┘
          / \
        Yes   No
        /       \
       ▼         ▼
 ┌──────────┐ ┌──────────┐
 │ ROLLBACK │ │  COMMIT  │
 │          │ │          │
 │ Undo ALL │ │ Write to │
 │ changes  │ │ disk     │
 │ (Atom.)  │ │ (Dura.)  │
 └──────────┘ └──────────┘
```

---

## ⚖️ ACID vs BASE

In the world of **NoSQL** and **distributed systems**, there's an alternative philosophy called **BASE**:

```text
     ACID (RDBMS)                    BASE (NoSQL)
     ────────────                    ────────────
     Strong Consistency              Eventual Consistency
     Strict Rules                    Flexible Rules
     Pessimistic                     Optimistic
```

| | ACID | BASE |
|---|---|---|
| **Full Form** | Atomicity, Consistency, Isolation, Durability | **B**asically **A**vailable, **S**oft state, **E**ventual consistency |
| **Philosophy** | *"Be correct at all costs"* | *"Be available at all costs"* |
| **Consistency** | Strong (immediate) | Eventual (over time) |
| **Transactions** | Strict, locked | Flexible, optimistic |
| **Performance** | Lower (more locks) | Higher (fewer locks) |
| **Scalability** | Vertical (scale up) | Horizontal (scale out) |
| **Use Case** | Banking, Inventory, Booking | Social Media, IoT, Caching |
| **Examples** | PostgreSQL, MySQL, Oracle | Cassandra, DynamoDB, MongoDB |

### BASE Explained

```text
B.A. → Basically Available
       System guarantees availability (may serve stale data)
       "The store is always open, even if some shelves aren't restocked yet"

S.   → Soft State
       State may change over time even without new input (due to syncing)
       "The system is always in flux — data is flowing between nodes"

E.   → Eventual Consistency
       Given enough time, all nodes will converge to the same state
       "Everyone will eventually get the memo"
```

- **Interview tip:** *"ACID = pessimistic, prioritizes correctness. BASE = optimistic, prioritizes availability. Choose based on your use case."*

---

## 🔗 ACID + CAP Connection

```text
┌──────────────┬────────────────────────────────────┐
│ CAP Choice   │ ACID Behavior                      │
├──────────────┼────────────────────────────────────┤
│ CP Systems   │ Full ACID (strong consistency)     │
│ (MongoDB,    │ Transactions are strict            │
│  HBase)      │                                    │
├──────────────┼────────────────────────────────────┤
│ AP Systems   │ BASE (eventual consistency)        │
│ (Cassandra,  │ Relaxed ACID, flexible             │
│  DynamoDB)   │                                    │
├──────────────┼────────────────────────────────────┤
│ CA Systems   │ Full ACID (traditional RDBMS)      │
│ (PostgreSQL, │ Single-node, no partitions         │
│  MySQL)      │                                    │
└──────────────┴────────────────────────────────────┘
```

---

## 🧪 SQL Examples — ACID in Action

### Atomicity Example

```sql
-- Transfer ₹5,000 from Kedar to Raj
BEGIN TRANSACTION;

    UPDATE accounts SET balance = balance - 5000 
    WHERE user_id = 'Kedar' AND balance >= 5000;  -- Check sufficient funds (Consistency)
    
    IF @@ROWCOUNT = 0
        ROLLBACK;  -- ❌ Insufficient funds → undo everything (Atomicity)
        RETURN;
    END IF;
    
    UPDATE accounts SET balance = balance + 5000 
    WHERE user_id = 'Raj';

COMMIT;  -- ✅ Both succeeded → make permanent (Durability)
```

### Isolation Example

```sql
-- Set isolation level explicitly
SET TRANSACTION ISOLATION LEVEL SERIALIZABLE;

BEGIN TRANSACTION;
    SELECT balance FROM accounts WHERE user_id = 'Kedar';  -- Locked! 🔒
    -- No other transaction can modify Kedar's balance until we COMMIT
    UPDATE accounts SET balance = balance - 5000 WHERE user_id = 'Kedar';
COMMIT;  -- Lock released 🔓
```

### Consistency Example

```sql
-- Constraints enforce consistency automatically
CREATE TABLE accounts (
    user_id    VARCHAR(50) PRIMARY KEY,           -- No duplicates
    balance    DECIMAL(15,2) CHECK (balance >= 0), -- No negative balance
    email      VARCHAR(255) UNIQUE NOT NULL,       -- Required & unique
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- This will FAIL → maintains consistency
UPDATE accounts SET balance = -500 WHERE user_id = 'Kedar';
-- ❌ ERROR: CHECK constraint violated (balance >= 0)
```

---

## 🎯 Memory Aid — The ACID Story

> *Imagine you're at an **ATM** withdrawing money:*
>
> **A (Atomicity):** The ATM either gives you the cash AND debits your account, or does neither. It won't debit without dispensing. *"All or nothing."*
>
> **C (Consistency):** Your total wealth doesn't change — money moves from bank to wallet. No money created or destroyed. *"Rules are followed."*
>
> **I (Isolation):** If someone else is transferring to your account simultaneously, their transaction won't mess up yours. *"No interference."*
>
> **D (Durability):** Once the ATM says "Transaction Successful," even if the ATM crashes the next second, your money is safe. *"It's permanent."*

---

## 🎤 Top Interview Questions & Answers

| # | Question | Best Answer |
|---|----------|-------------|
| 1 | *What is ACID?* | 4 properties guaranteeing reliable transactions: Atomicity, Consistency, Isolation, Durability |
| 2 | *Explain Atomicity with an example* | Bank transfer — either both debit and credit happen, or neither does |
| 3 | *What's the difference between Consistency in ACID vs CAP?* | ACID Consistency = rules/constraints enforced. CAP Consistency = all nodes see same data |
| 4 | *What are isolation levels?* | Read Uncommitted → Read Committed → Repeatable Read → Serializable (weakest to strongest) |
| 5 | *What is a dirty read?* | Reading uncommitted data from another transaction that might rollback |
| 6 | *Phantom vs Non-Repeatable read?* | Non-Repeatable = same row, different value. Phantom = different number of rows |
| 7 | *How is durability achieved?* | Write-Ahead Logging (WAL), redo logs, checkpointing, replication |
| 8 | *ACID vs BASE?* | ACID = strict, consistent, RDBMS. BASE = flexible, eventually consistent, NoSQL |
| 9 | *Which databases are ACID compliant?* | PostgreSQL, MySQL (InnoDB), Oracle, SQL Server, SQLite |
| 10 | *Can NoSQL be ACID?* | Yes! MongoDB (4.0+) supports multi-document ACID transactions. DynamoDB has DynamoDB Transactions |
| 11 | *Default isolation level of MySQL?* | Repeatable Read (InnoDB engine) |
| 12 | *Default isolation level of PostgreSQL?* | Read Committed |

---

## 🏁 Quick Revision Cheat Sheet

```text
A = Atomicity     → "All or Nothing"        → ROLLBACK on failure
C = Consistency   → "Valid → Valid"          → Constraints enforced
I = Isolation     → "No interference"        → Isolation levels
D = Durability    → "Committed = Permanent"  → WAL, Redo logs

🔒 Isolation Levels (weak → strong):
   Read Uncommitted → Read Committed → Repeatable Read → Serializable

🔴 Read Problems:
   Dirty Read        → Reading UNCOMMITTED data
   Non-Repeatable    → Same query, DIFFERENT values
   Phantom Read      → Same query, DIFFERENT number of rows

⚖️ ACID vs BASE:
   ACID → Strict, Correct, RDBMS (PostgreSQL, MySQL)
   BASE → Flexible, Available, NoSQL (Cassandra, DynamoDB)

🏦 ATM Analogy:
   A → Cash + Debit happen TOGETHER
   C → Total money conserved
   I → Other transactions don't interfere
   D → "Success" message = money is SAFE forever
```

---

> 💡 **Pro Interview Tip:** If an interviewer asks *"How do you ensure data integrity in your system?"*, structure your answer as:
>
> 1. **Database level:** ACID transactions with appropriate isolation level
> 2. **Application level:** Validation, idempotency, retry logic
> 3. **Infrastructure level:** Replication, backups, WAL
> 4. **Monitoring:** Alerts on constraint violations, transaction failures
>
> This shows you think **holistically**, not just textbook definitions!
