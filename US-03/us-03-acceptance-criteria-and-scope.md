# US-03 — Acceptance Criteria & Scope of Work

**User Story:**
> As an **Operations Staff**, I want to view a daily report of newly registered customers, so that I can monitor business growth and follow up with onboarding processes.

**Traces To:** SR-2.1
**User Journey:** `US-03/us-03-user-journey.md`

---

## Acceptance Criteria

### AC-03-01 — Report Page Access
- **Given** the Operations Staff is logged in to the system,
- **When** they navigate to the Reports section,
- **Then** the system must display a "Daily New Customer Report" option that they can select.

### AC-03-02 — Default Date to Today
- **Given** the Operations Staff opens the Daily New Customer Report,
- **When** the report page loads,
- **Then** the system must default the date selector to today's date and automatically display the report for that date.

### AC-03-03 — Date Selection
- **Given** the report page is displayed,
- **When** the Operations Staff selects a different date using the date picker,
- **Then** the system must refresh and display the report for the newly selected date.

### AC-03-04 — Report Content
- **Given** a date is selected and new customer registrations exist for that date,
- **When** the report is displayed,
- **Then** each row must show the following columns for every newly registered customer:
  - Row number
  - Customer ID
  - Full name
  - Phone number
  - Email address
  - Registration date and time
  - Registered by (CS Staff name)

### AC-03-05 — Total Count Summary
- **Given** the report is displayed,
- **When** the Operations Staff views the report,
- **Then** the system must show a summary line with the total number of newly registered customers for the selected date.

### AC-03-06 — No Data State
- **Given** the Operations Staff selects a date with no new customer registrations,
- **When** the report is generated,
- **Then** the system must display a clear message indicating that no new customers were registered on the selected date.

### AC-03-07 — Export Report
- **Given** the report has at least one customer record,
- **When** the Operations Staff clicks "Export",
- **Then** the system must download the report as a CSV file containing the same columns as the on-screen report, along with the selected report date in the filename.

---

## Scope of Work

### In Scope

| # | Deliverable | Description |
|---|---|---|
| 1 | **Daily New Customer Report Screen** | Design and implement the report page accessible from the Reports section |
| 2 | **Date Picker** | Date selector defaulting to today; refreshes report on change |
| 3 | **Report Table** | Display registered customers for the selected date with columns: No., Customer ID, Full Name, Phone Number, Email Address, Registration Date & Time, Registered By |
| 4 | **Total Count Summary** | Show the total number of new customers at the top or bottom of the report |
| 5 | **No Data State** | Display an informative message when no registrations exist for the selected date |
| 6 | **CSV Export** | Allow Operations Staff to download the report as a CSV file |

### Out of Scope

| # | Item | Reason |
|---|---|---|
| 1 | Multi-day or date-range reports | SR-2.1 specifies a per-day report only |
| 2 | Filtering or searching within report results | Not requested in this story |
| 3 | Customer registration | Covered by US-01 |
| 4 | Daily subscription history report | Covered by US-04 |
| 5 | Editing customer data from the report | Read-only view; modification is out of scope |

---

## Definition of Done

- [ ] Report page accessible from the Reports section (AC-03-01)
- [ ] Date picker defaults to today and auto-loads report (AC-03-02)
- [ ] Changing the date refreshes the report correctly (AC-03-03)
- [ ] Report table displays all required columns per customer row (AC-03-04)
- [ ] Total count summary displayed on the report (AC-03-05)
- [ ] No-data state message displayed when no registrations found (AC-03-06)
- [ ] CSV export functional with correct columns and date in filename (AC-03-07)
- [ ] Screen design reviewed and approved by stakeholder
- [ ] All acceptance criteria verified through functional testing
