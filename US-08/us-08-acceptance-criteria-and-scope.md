# US-08 — Acceptance Criteria & Scope of Work

**User Story:**
> As a **Database Administrator**, I want to run a SQL query to retrieve a new customer registration report for each day, so that I can access raw registration data directly from the database for analysis and auditing.

**Traces To:** SR-2.1
**User Journey:** `US-08/us-08-user-journey.md`

---

## Acceptance Criteria

### AC-08-01 — Date Parameter Input
- **Given** the DBA has access to the production or reporting database,
- **When** they execute the SQL query with a specified date as the input parameter,
- **Then** the query must accept a single date value (format: `YYYY-MM-DD`) and filter results to only customer records whose `created_at` falls within that date.

### AC-08-02 — Required Output Columns
- **Given** the query is executed with a valid date parameter,
- **When** the result set is returned,
- **Then** each row must include the following columns in order:
  - Row number
  - Customer ID
  - Full name
  - Phone number
  - Email address
  - Registration date and time
  - Registered by (CS Staff full name)

### AC-08-03 — Staff Name Resolution via JOIN
- **Given** the result set includes a "Registered By" column,
- **When** the query is executed,
- **Then** the column must display the CS Staff's `full_name` from the `USER` table (resolved via `CUSTOMER.created_by → USER.user_id`), not the raw numeric foreign key.

### AC-08-04 — Result Set Ordering
- **Given** the query returns one or more rows,
- **When** the result set is displayed,
- **Then** rows must be ordered by `created_at` ascending so that the earliest registration on the selected date appears first.

### AC-08-05 — Total Count Query
- **Given** the DBA needs a summary count for the selected date,
- **When** they execute the accompanying COUNT query with the same date parameter,
- **Then** the query must return the total number of customer records registered on that date as a single scalar value.

### AC-08-06 — No Data State
- **Given** the DBA executes the query for a date with no customer registrations,
- **When** the result set is returned,
- **Then** the query must return an empty result set (zero rows) without raising errors or exceptions.

### AC-08-07 — Query Reusability
- **Given** the SQL query is delivered as a documented, parameterised script,
- **When** the DBA needs to run it for any date,
- **Then** the only change required must be the date parameter value, with no modification to the query logic itself.

---

## Scope of Work

### In Scope

| # | Deliverable | Description |
|---|---|---|
| 1 | **Main Report Query** | Parameterised SQL `SELECT` query that returns all required columns for customers registered on the specified date, ordered by `created_at` ascending |
| 2 | **Total Count Query** | Companion SQL `SELECT COUNT(*)` query using the same date parameter to return the total number of new registrations for the day |
| 3 | **Table JOIN Definition** | `INNER JOIN` from `CUSTOMER` to `USER` on `created_by = user_id` to resolve the Registered By staff name |
| 4 | **Query Script File** | Delivered as a `.sql` script file with inline comments explaining each clause, parameter usage, and expected output columns |
| 5 | **Sample Output Documentation** | Example of expected query output (column names, data types, and sample rows) documented alongside the script |

### Out of Scope

| # | Item | Reason |
|---|---|---|
| 1 | Web UI or dashboard for this report | Covered by US-03 (Operations Staff view) |
| 2 | RESTful API endpoint | Not required for direct database access by DBA |
| 3 | Date-range or multi-day queries | SR-2.1 specifies a per-day report only |
| 4 | Modification of customer data | Read-only query; data modification is out of scope |
| 5 | Subscription history report | Covered by US-09 |

---

## Definition of Done

- [ ] Query filters correctly by the supplied date parameter (AC-08-01)
- [ ] Result set returns all seven required columns per row (AC-08-02)
- [ ] "Registered By" column resolves to CS Staff full name via JOIN (AC-08-03)
- [ ] Rows are ordered by `created_at` ascending (AC-08-04)
- [ ] COUNT query returns correct total for the specified date (AC-08-05)
- [ ] Query returns an empty result set (no error) when no data exists for the date (AC-08-06)
- [ ] Script file is parameterised and reusable for any date without logic changes (AC-08-07)
- [ ] Query validated against the database schema defined in `diagrams/er-diagram.md`
- [ ] All acceptance criteria verified by executing the query against test data
