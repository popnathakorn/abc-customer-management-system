# US-08 — SQL Script: Daily New Customer Registration Report

**User Story:**
> As a **Database Administrator**, I want to run a SQL query to retrieve a new customer registration report for each day, so that I can access raw registration data directly from the database for analysis and auditing.

**Traces To:** SR-2.1 | **Acceptance Criteria:** `US-08/us-08-acceptance-criteria-and-scope.md`

---

## Overview

This document provides two SQL queries for the Daily New Customer Registration Report:

| # | Query | Purpose |
|---|---|---|
| 1 | **Main Report Query** | Returns all customers registered on the specified date with full column detail |
| 2 | **Total Count Query** | Returns the total number of new registrations for the specified date |

Both queries target the `CUSTOMER` and `USER` tables. The only value to change between runs is the **date parameter** (`'YYYY-MM-DD'`).

---

## Tables Used

| Table | Alias | Role in Query |
|---|---|---|
| `CUSTOMER` | `c` | Primary data source — one row per registered customer |
| `USER` | `u` | Joined to resolve `created_by` (foreign key) to a staff full name |

**Relevant columns:**

| Column | Table | Description |
|---|---|---|
| `customer_id` | `CUSTOMER` | System-generated customer identifier (e.g. `CUS-000001`) |
| `full_name` | `CUSTOMER` | Customer's full name |
| `phone_number` | `CUSTOMER` | Customer's phone number |
| `email_address` | `CUSTOMER` | Customer's email address |
| `created_at` | `CUSTOMER` | Timestamp of registration — used for date filtering and ordering |
| `created_by` | `CUSTOMER` | FK referencing `USER.user_id` — the CS Staff who registered the customer |
| `user_id` | `USER` | Primary key matched against `CUSTOMER.created_by` |
| `full_name` | `USER` | CS Staff full name — displayed as "Registered By" |

---

## Query 1 — Main Report Query

Returns one row per customer registered on the target date, ordered by registration time ascending.

```sql
-- ============================================================
-- US-08: Daily New Customer Registration Report
-- SR-2.1: New customer report per day
--
-- PARAMETER: Replace the date value below with the target date
--            Format: 'YYYY-MM-DD'
-- ============================================================

SELECT
    ROW_NUMBER() OVER (ORDER BY c.created_at ASC)  AS row_num,
    c.customer_id,
    c.full_name                                     AS customer_name,
    c.phone_number,
    c.email_address,
    c.created_at                                    AS registration_datetime,
    u.full_name                                     AS registered_by
FROM
    CUSTOMER c
    INNER JOIN USER u ON c.created_by = u.user_id
WHERE
    DATE(c.created_at) = '2026-02-21'   -- << CHANGE THIS DATE
ORDER BY
    c.created_at ASC;
```

### Clause Explanation

| Clause | Purpose |
|---|---|
| `ROW_NUMBER() OVER (ORDER BY c.created_at ASC)` | Generates a sequential row number within the result set, ordered by registration time |
| `INNER JOIN USER u ON c.created_by = u.user_id` | Resolves the CS Staff foreign key to a human-readable full name (AC-08-03) |
| `WHERE DATE(c.created_at) = '...'` | Filters records to only those registered on the specified date, ignoring the time component (AC-08-01) |
| `ORDER BY c.created_at ASC` | Ensures earliest registrations appear first (AC-08-04) |

---

## Query 2 — Total Count Query

Returns a single value: the total number of customers registered on the target date.

```sql
-- ============================================================
-- US-08: Daily New Customer Registration — Total Count
-- SR-2.1: New customer report per day
--
-- PARAMETER: Replace the date value below with the target date
--            Format: 'YYYY-MM-DD'
-- ============================================================

SELECT
    COUNT(*) AS total_new_customers
FROM
    CUSTOMER c
WHERE
    DATE(c.created_at) = '2026-02-21';  -- << CHANGE THIS DATE
```

> Run this query alongside Query 1 to confirm the row count matches the on-screen total shown in the US-03 Operations Staff report.

---

## How to Use

1. Open the script in your SQL client (e.g. DBeaver, MySQL Workbench, pgAdmin).
2. Locate the `-- << CHANGE THIS DATE` comment in each query.
3. Replace the date string (`'2026-02-21'`) with the target date in `'YYYY-MM-DD'` format.
4. Execute **Query 1** to retrieve the full report.
5. Execute **Query 2** to verify the total count.
6. (Optional) Use your SQL client's export function to save the result as a `.csv` file.

---

## Expected Output — Query 1

| Column | Data Type | Example |
|---|---|---|
| `row_num` | integer | `1` |
| `customer_id` | string | `CUS-000042` |
| `customer_name` | string | `สมชาย ใจดี` |
| `phone_number` | string | `081-234-5678` |
| `email_address` | string | `somchai@email.com` |
| `registration_datetime` | datetime | `2026-02-21 09:14:03` |
| `registered_by` | string | `นางสาว อรุณ พนักงาน` |

**Empty result:** If no customers were registered on the specified date, Query 1 returns zero rows without error (AC-08-06).

## Expected Output — Query 2

| Column | Data Type | Example |
|---|---|---|
| `total_new_customers` | integer | `7` |

---

## Notes

- `DATE(c.created_at)` extracts the date portion of the `datetime` column for comparison. This is compatible with MySQL and MariaDB. For other databases use the equivalent function (e.g. `CAST(c.created_at AS DATE)` for SQL Server, `c.created_at::date` for PostgreSQL).
- The `INNER JOIN` on `USER` means any customer row where `created_by` does not match a valid `user_id` will be excluded from results. This should not occur under normal system operation as `created_by` is a non-nullable foreign key set at registration time.
- These queries are **read-only** (`SELECT` only). No data is modified.
- Column output aligns with the US-03 Operations Staff report to support cross-verification between the UI and raw database data.
