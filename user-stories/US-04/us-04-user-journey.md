# US-04 User Journey — View Daily Subscription History Report

**User Story:**
> As an **Operations Staff**, I want to view a daily service subscription history report for each customer, so that I can audit subscription changes and ensure accurate billing records.

---

## User Journey Diagram

```mermaid
journey
    title US-04: View Daily Subscription History Report (Operations Staff)

    section Authentication
      Log in to system: 5: Ops Staff
      Navigate to Reports section: 4: Ops Staff

    section Open Report
      Select "Daily Subscription History Report": 5: Ops Staff
      System loads report with today's date by default: 4: Ops Staff, System

    section Review Report
      Review total subscription count for the day: 4: Ops Staff
      Browse report table (Customer ID, name, package, dates, enrolled by): 4: Ops Staff

    section Change Date (optional)
      Select a different date from date picker: 3: Ops Staff
      System refreshes and displays report for selected date: 4: Ops Staff, System

    section No Data (alternative)
      System displays no-data message for selected date: 2: Ops Staff, System

    section Export (optional)
      Click "Export": 4: Ops Staff
      System downloads report as CSV file: 4: Ops Staff, System
```

---

## Journey Summary

| Step | Actor | Action | Satisfaction |
|---|---|---|---|
| 1 | Ops Staff | Log in and navigate to Reports section | High |
| 2 | Ops Staff + System | Open Daily Subscription History Report; report loads for today by default | High |
| 3 | Ops Staff | Review total count and subscription rows for the selected date | High |
| 4 | Ops Staff + System | (Optional) Select a different date; report refreshes | Medium–High |
| 5 | System | (Alternative) Display no-data message when no subscription records found | Low |
| 6 | Ops Staff + System | (Optional) Export report as CSV | High |

---

## Notes

- **Default date**: The report always defaults to today's date on load to minimise the steps needed for the most common daily audit check.
- **Read-only**: The report is a read-only view. No subscription data can be edited from this screen.
- **Enrolled By**: Displaying the CS Staff name who enrolled each subscription supports accountability and audit trails.
- **CSV export**: The exported filename should include the report date (e.g. `subscription-history-2026-02-21.csv`) so files are easily identifiable when saved.
- **Data source**: Report rows are derived from `SUBSCRIPTION.created_at` filtered to the selected date, joined to `CUSTOMER` for customer details, `SERVICE_PACKAGE` for package name, and `USER` for the "Enrolled By" column.
