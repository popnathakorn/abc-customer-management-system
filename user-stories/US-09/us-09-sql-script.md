# US-09 — SQL Script: Daily Subscription History Report

**User Story:**
> As a **Database Administrator**, I want to run a SQL query to retrieve a service subscription history report for each customer per day, so that I can access raw subscription data directly from the database for analysis and auditing.

**Traces To:** SR-2.2 | **Acceptance Criteria:** `user-stories/US-09/us-09-acceptance-criteria-and-scope.md`

---

## Overview

This document provides two SQL queries for the Daily Subscription History Report:

| # | Query | Purpose |
|---|---|---|
| 1 | **Main Report Query** | Returns all subscription records enrolled on the specified date with full column detail |
| 2 | **Total Count Query** | Returns the total number of subscription records enrolled on the specified date |

Both queries target the `SUBSCRIPTION`, `CUSTOMER`, `SERVICE_PACKAGE`, and `USER` tables. The only value to change between runs is the **date parameter** (`'YYYY-MM-DD'`).

---

## Tables Used

| Table | Alias | Role in Query |
|---|---|---|
| `SUBSCRIPTION` | `s` | Primary data source — one row per subscription enrolled |
| `CUSTOMER` | `c` | Joined to resolve `customer_id` (foreign key) to customer name and ID |
| `SERVICE_PACKAGE` | `sp` | Joined to resolve `package_id` (foreign key) to a human-readable package name |
| `USER` | `u` | Joined to resolve `created_by` (foreign key) to the CS Staff full name |

**Relevant columns:**

| Column | Table | Description |
|---|---|---|
| `subscription_id` | `SUBSCRIPTION` | System-generated subscription identifier |
| `customer_id` | `SUBSCRIPTION` | FK referencing `CUSTOMER.customer_id` |
| `package_id` | `SUBSCRIPTION` | FK referencing `SERVICE_PACKAGE.package_id` |
| `start_date` | `SUBSCRIPTION` | Date the subscription becomes active |
| `end_date` | `SUBSCRIPTION` | Date the subscription expires |
| `created_at` | `SUBSCRIPTION` | Timestamp of enrollment — used for date filtering and ordering |
| `created_by` | `SUBSCRIPTION` | FK referencing `USER.user_id` — the CS Staff who enrolled the subscription |
| `customer_id` | `CUSTOMER` | Primary key matched against `SUBSCRIPTION.customer_id` |
| `full_name` | `CUSTOMER` | Customer full name — displayed as "Customer Name" |
| `package_id` | `SERVICE_PACKAGE` | Primary key matched against `SUBSCRIPTION.package_id` |
| `package_name` | `SERVICE_PACKAGE` | Package name — displayed as "Package Name" |
| `user_id` | `USER` | Primary key matched against `SUBSCRIPTION.created_by` |
| `full_name` | `USER` | CS Staff full name — displayed as "Enrolled By" |

---

## Query 1 — Main Report Query

Returns one row per subscription enrolled on the target date, ordered by enrollment time ascending.

```sql
-- ============================================================
-- US-09: Daily Subscription History Report
-- SR-2.2: Service subscription history report per customer per day
--
-- PARAMETER: Replace the date value below with the target date
--            Format: 'YYYY-MM-DD'
-- ============================================================

SELECT
    ROW_NUMBER() OVER (ORDER BY s.created_at ASC)  AS row_num,
    c.customer_id,
    c.full_name                                     AS customer_name,
    s.subscription_id,
    sp.package_name,
    s.start_date,
    s.end_date,
    u.full_name                                     AS enrolled_by,
    s.created_at                                    AS enrolled_datetime
FROM
    SUBSCRIPTION s
    INNER JOIN CUSTOMER      c  ON s.customer_id = c.customer_id
    INNER JOIN SERVICE_PACKAGE sp ON s.package_id  = sp.package_id
    INNER JOIN USER           u  ON s.created_by  = u.user_id
WHERE
    DATE(s.created_at) = '2026-02-21'   -- << CHANGE THIS DATE
ORDER BY
    s.created_at ASC;
```

### Clause Explanation

| Clause | Purpose |
|---|---|
| `ROW_NUMBER() OVER (ORDER BY s.created_at ASC)` | Generates a sequential row number within the result set, ordered by enrollment time |
| `INNER JOIN CUSTOMER c ON s.customer_id = c.customer_id` | Resolves the customer foreign key to the customer's full name and ID (AC-09-03) |
| `INNER JOIN SERVICE_PACKAGE sp ON s.package_id = sp.package_id` | Resolves the package foreign key to a human-readable package name (AC-09-03) |
| `INNER JOIN USER u ON s.created_by = u.user_id` | Resolves the CS Staff foreign key to a human-readable full name (AC-09-03) |
| `WHERE DATE(s.created_at) = '...'` | Filters records to only those enrolled on the specified date, ignoring the time component (AC-09-01) |
| `ORDER BY s.created_at ASC` | Ensures earliest enrollments appear first (AC-09-04) |

---

## Query 2 — Total Count Query

Returns a single value: the total number of subscriptions enrolled on the target date.

```sql
-- ============================================================
-- US-09: Daily Subscription History Report — Total Count
-- SR-2.2: Service subscription history report per customer per day
--
-- PARAMETER: Replace the date value below with the target date
--            Format: 'YYYY-MM-DD'
-- ============================================================

SELECT
    COUNT(*) AS total_subscriptions
FROM
    SUBSCRIPTION s
WHERE
    DATE(s.created_at) = '2026-02-21';  -- << CHANGE THIS DATE
```

> Run this query alongside Query 1 to confirm the row count matches the on-screen total shown in the US-04 Operations Staff subscription history report.

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
| `subscription_id` | string | `SUB-000087` |
| `package_name` | string | `5G Next Speed` |
| `start_date` | date | `2026-02-21` |
| `end_date` | date | `2027-02-20` |
| `enrolled_by` | string | `นางสาว อรุณ พนักงาน` |
| `enrolled_datetime` | datetime | `2026-02-21 10:32:15` |

**Empty result:** If no subscriptions were enrolled on the specified date, Query 1 returns zero rows without error (AC-09-06).

## Expected Output — Query 2

| Column | Data Type | Example |
|---|---|---|
| `total_subscriptions` | integer | `12` |

---

## Notes

- `DATE(s.created_at)` extracts the date portion of the `datetime` column for comparison. This is compatible with MySQL and MariaDB. For other databases use the equivalent function (e.g. `CAST(s.created_at AS DATE)` for SQL Server, `s.created_at::date` for PostgreSQL).
- The `INNER JOIN` on `CUSTOMER`, `SERVICE_PACKAGE`, and `USER` means any subscription row where a foreign key does not match a valid primary key will be excluded from results. This should not occur under normal system operation as all three foreign keys are non-nullable and set at enrollment time.
- These queries are **read-only** (`SELECT` only). No data is modified.
- Column output aligns with the US-04 Operations Staff subscription history report to support cross-verification between the UI report and raw database data.
