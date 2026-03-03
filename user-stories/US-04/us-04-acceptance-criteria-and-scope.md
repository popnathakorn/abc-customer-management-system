# US-04 — Acceptance Criteria & Scope of Work

**User Story:**
> As an **Operations Staff**, I want to view a daily service subscription history report for each customer, so that I can audit subscription changes and ensure accurate billing records.

**Traces To:** SR-2.2
**User Journey:** `user-stories/US-04/us-04-user-journey.md`

---

## Acceptance Criteria

### AC-04-01 — Report Page Access
- **Given** the Operations Staff is logged in to the system,
- **When** they navigate to the Reports section,
- **Then** the system must display a "Daily Subscription History Report" option that they can select.

### AC-04-02 — Default Date to Today
- **Given** the Operations Staff opens the Daily Subscription History Report,
- **When** the report page loads,
- **Then** the system must default the date selector to today's date and automatically display the report for that date.

### AC-04-03 — Date Selection
- **Given** the report page is displayed,
- **When** the Operations Staff selects a different date using the date picker,
- **Then** the system must refresh and display the report for the newly selected date.

### AC-04-04 — Report Content
- **Given** a date is selected and subscription records exist for that date,
- **When** the report is displayed,
- **Then** each row must show the following columns for every subscription enrolled on that date:
  - Row number
  - Customer ID
  - Customer full name
  - Subscription ID
  - Package name
  - Start date
  - End date
  - Enrolled by (CS Staff name)
  - Enrolled date and time

### AC-04-05 — Total Count Summary
- **Given** the report is displayed,
- **When** the Operations Staff views the report,
- **Then** the system must show a summary line with the total number of subscription records for the selected date.

### AC-04-06 — No Data State
- **Given** the Operations Staff selects a date with no subscription records,
- **When** the report is generated,
- **Then** the system must display a clear message indicating that no subscription changes were recorded on the selected date.

### AC-04-07 — Export Report
- **Given** the report has at least one subscription record,
- **When** the Operations Staff clicks "Export",
- **Then** the system must download the report as a CSV file containing the same columns as the on-screen report, along with the selected report date in the filename.

---

## Scope of Work

### In Scope

| # | Deliverable | Description |
|---|---|---|
| 1 | **Daily Subscription History Report Screen** | Design and implement the report page accessible from the Reports section |
| 2 | **Date Picker** | Date selector defaulting to today; refreshes report on change |
| 3 | **Report Table** | Display subscription records for the selected date with columns: No., Customer ID, Customer Full Name, Subscription ID, Package Name, Start Date, End Date, Enrolled By, Enrolled Date & Time |
| 4 | **Total Count Summary** | Show the total number of subscription records at the top or bottom of the report |
| 5 | **No Data State** | Display an informative message when no subscription records exist for the selected date |
| 6 | **CSV Export** | Allow Operations Staff to download the report as a CSV file |

### Out of Scope

| # | Item | Reason |
|---|---|---|
| 1 | Multi-day or date-range reports | SR-2.2 specifies a per-day report only |
| 2 | Filtering or searching within report results | Not requested in this story |
| 3 | Subscription enrollment | Covered by US-02 |
| 4 | Daily new customer report | Covered by US-03 |
| 5 | Editing subscription data from the report | Read-only view; modification is out of scope |

---

## Definition of Done

- [ ] Report page accessible from the Reports section (AC-04-01)
- [ ] Date picker defaults to today and auto-loads report (AC-04-02)
- [ ] Changing the date refreshes the report correctly (AC-04-03)
- [ ] Report table displays all required columns per subscription row (AC-04-04)
- [ ] Total count summary displayed on the report (AC-04-05)
- [ ] No-data state message displayed when no records found (AC-04-06)
- [ ] CSV export functional with correct columns and date in filename (AC-04-07)
- [ ] Screen design reviewed and approved by stakeholder
- [ ] All acceptance criteria verified through functional testing
