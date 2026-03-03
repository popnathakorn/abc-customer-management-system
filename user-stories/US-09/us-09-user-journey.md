# US-09 User Journey — Run SQL Query for Daily Subscription History Report

**User Story:**
> As a **Database Administrator**, I want to run a SQL query to retrieve a service subscription history report for each customer per day, so that I can access raw subscription data directly from the database for analysis and auditing.

---

## User Journey Diagram

```mermaid
journey
    title US-09: Run SQL Query for Daily Subscription History Report (DBA)

    section Access Database
      Open SQL client and connect to database: 5: DBA
      Confirm connection to correct schema: 4: DBA, System

    section Load Query Script
      Open the daily subscription history report script (.sql): 5: DBA
      Review query structure and parameter placeholder: 4: DBA

    section Set Date Parameter
      Replace date parameter with target date (YYYY-MM-DD): 4: DBA
      Verify parameter is set correctly before execution: 4: DBA

    section Execute Query
      Run the main report query: 5: DBA, System
      System returns result set with subscription rows: 4: DBA, System

    section Review Results
      Review rows (Customer ID, name, Subscription ID, package, dates, enrolled by): 4: DBA
      Run COUNT query to confirm total subscriptions enrolled for the day: 4: DBA, System

    section No Data (alternative)
      Query returns empty result set for date with no subscription records: 2: DBA, System

    section Export (optional)
      Export result set to CSV via SQL client export function: 4: DBA
```

---

## Journey Summary

| Step | Actor | Action | Satisfaction |
|---|---|---|---|
| 1 | DBA | Connect to database via SQL client and confirm correct schema | High |
| 2 | DBA | Open the daily subscription history report `.sql` script | High |
| 3 | DBA | Set the target date parameter to the desired date | High |
| 4 | DBA + System | Execute main report query; system returns subscription rows | High |
| 5 | DBA + System | Run COUNT query to verify total subscriptions enrolled for the date | High |
| 6 | System | (Alternative) Return empty result set when no subscription records exist for the date | Low |
| 7 | DBA | (Optional) Export result set to CSV via SQL client | High |

---

## Notes

- **Direct database access**: This story does not involve any web UI. The DBA interacts directly with the database using a SQL client (e.g., DBeaver, MySQL Workbench, pgAdmin, or similar).
- **Query parameterisation**: The script must use a clear parameter placeholder (e.g., a comment-marked variable or a bind variable) so the DBA only changes the date value, not the query logic.
- **Date filtering**: Filtering is applied on `SUBSCRIPTION.created_at` using a date-only comparison (`DATE(created_at) = :target_date` or equivalent) to capture all enrollments regardless of time of day.
- **Multi-table JOIN**: The query joins four tables — `SUBSCRIPTION`, `CUSTOMER`, `SERVICE_PACKAGE`, and `USER` — to resolve all human-readable columns without requiring the DBA to run separate lookup queries.
- **COUNT companion query**: A separate `SELECT COUNT(*) FROM SUBSCRIPTION WHERE DATE(created_at) = :target_date` query provides the daily total without requiring the DBA to count rows manually. This companion query uses the same date parameter as the main report query.
- **Read-only intent**: The script is a `SELECT`-only query. No `INSERT`, `UPDATE`, or `DELETE` statements are included, ensuring no data is modified during reporting.
- **Data source alignment**: The columns returned by this query directly mirror those displayed in the US-04 Operations Staff subscription history report screen, enabling cross-verification between the UI report and raw database data.
