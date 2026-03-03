# US-09 — Acceptance Criteria & Scope of Work

**User Story:**
> As a **Database Administrator**, I want to run a SQL query to retrieve a service subscription history report for each customer per day, so that I can access raw subscription data directly from the database for analysis and auditing.

**Traces To:** SR-2.2
**User Journey:** `user-stories/US-09/us-09-user-journey.md`

---

## Acceptance Criteria

### AC-09-01 — Date Parameter Input
- **Given** the DBA has access to the production or reporting database,
- **When** they execute the SQL query with a specified date as the input parameter,
- **Then** the query must accept a single date value (format: `YYYY-MM-DD`) and filter results to only subscription records whose `created_at` falls within that date.

### AC-09-02 — Required Output Columns
- **Given** the query is executed with a valid date parameter,
- **When** the result set is returned,
- **Then** each row must include the following columns in order:
  - Row number
  - Customer ID
  - Customer full name
  - Subscription ID
  - Package name
  - Start date
  - End date
  - Enrolled by (CS Staff full name)
  - Enrolled date and time

### AC-09-03 — Customer and Package Name Resolution via JOIN
- **Given** the result set includes "Customer Full Name", "Package Name", and "Enrolled By" columns,
- **When** the query is executed,
- **Then** the columns must be resolved via the following joins:
  - `CUSTOMER.customer_id` → to retrieve `CUSTOMER.full_name` as customer name
  - `SERVICE_PACKAGE.package_id` → to retrieve `SERVICE_PACKAGE.package_name`
  - `USER.user_id` → resolved via `SUBSCRIPTION.created_by → USER.user_id` to retrieve `USER.full_name` as "Enrolled By"

### AC-09-04 — Result Set Ordering
- **Given** the query returns one or more rows,
- **When** the result set is displayed,
- **Then** rows must be ordered by `SUBSCRIPTION.created_at` ascending so that the earliest subscription enrolled on the selected date appears first.

### AC-09-05 — Total Count Query
- **Given** the DBA needs a summary count for the selected date,
- **When** they execute the accompanying COUNT query with the same date parameter,
- **Then** the query must return the total number of subscription records enrolled on that date as a single scalar value.

### AC-09-06 — No Data State
- **Given** the DBA executes the query for a date with no subscription records,
- **When** the result set is returned,
- **Then** the query must return an empty result set (zero rows) without raising errors or exceptions.

### AC-09-07 — Query Reusability
- **Given** the SQL query is delivered as a documented, parameterised script,
- **When** the DBA needs to run it for any date,
- **Then** the only change required must be the date parameter value, with no modification to the query logic itself.

---

## Scope of Work

### In Scope

| # | Deliverable | Description |
|---|---|---|
| 1 | **Main Report Query** | Parameterised SQL `SELECT` query that returns all required columns for subscriptions enrolled on the specified date, ordered by `SUBSCRIPTION.created_at` ascending |
| 2 | **Total Count Query** | Companion SQL `SELECT COUNT(*)` query using the same date parameter to return the total number of subscription records for the day |
| 3 | **Table JOIN Definitions** | `INNER JOIN` from `SUBSCRIPTION` to `CUSTOMER` (on `customer_id`), to `SERVICE_PACKAGE` (on `package_id`), and to `USER` (on `created_by = user_id`) to resolve all human-readable columns |
| 4 | **Query Script File** | Delivered as a `.sql` script file with inline comments explaining each clause, parameter usage, and expected output columns |
| 5 | **Sample Output Documentation** | Example of expected query output (column names, data types, and sample rows) documented alongside the script |

### Out of Scope

| # | Item | Reason |
|---|---|---|
| 1 | Web UI or dashboard for this report | Covered by US-04 (Operations Staff view) |
| 2 | RESTful API endpoint | Not required for direct database access by DBA |
| 3 | Date-range or multi-day queries | SR-2.2 specifies a per-day report only |
| 4 | Modification of subscription data | Read-only query; data modification is out of scope |
| 5 | Daily new customer registration report | Covered by US-08 |

---

## Definition of Done

- [ ] Query filters correctly by the supplied date parameter (AC-09-01)
- [ ] Result set returns all nine required columns per row (AC-09-02)
- [ ] Customer name, package name, and "Enrolled By" resolved via JOINs (AC-09-03)
- [ ] Rows are ordered by `SUBSCRIPTION.created_at` ascending (AC-09-04)
- [ ] COUNT query returns correct total for the specified date (AC-09-05)
- [ ] Query returns an empty result set (no error) when no data exists for the date (AC-09-06)
- [ ] Script file is parameterised and reusable for any date without logic changes (AC-09-07)
- [ ] Query validated against the database schema defined in `diagrams/er-diagram.md`
- [ ] All acceptance criteria verified by executing the query against test data
