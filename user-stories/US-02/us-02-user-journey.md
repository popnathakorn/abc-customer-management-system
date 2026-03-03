# US-02 User Journey — Enroll Customer into Service Package

**User Story:**
> As a **Customer Service Staff**, I want to enroll a customer into a service package (e.g. 5G Next Speed, 599 ฿/month) and record the subscription start and end dates, so that the customer's active service is tracked and managed correctly.

---

## User Journey Diagram

```mermaid
journey
    title US-02: Enroll Customer into Service Package (Customer Service Staff)

    section Authentication
      Log in to system: 5: CS Staff
      Navigate to Customer Management: 4: CS Staff

    section Locate Customer
      Search for customer by name or Customer ID: 4: CS Staff
      System displays matching customer record: 4: CS Staff, System
      Open customer record: 5: CS Staff

    section Initiate Enrollment
      Click "Add Service Enrollment": 5: CS Staff
      System displays enrollment form with customer name and ID pre-loaded: 4: CS Staff, System

    section Confirm Customer
      CS Staff verifies customer name and Customer ID on form header: 4: CS Staff

    section Select Service Package
      Browse available package list: 4: CS Staff
      Select a package (e.g. 5G Next Speed – 599 ฿/month): 5: CS Staff
      System displays full package details in summary panel: 4: CS Staff, System

    section Enter Subscription Dates
      Enter subscription start date: 4: CS Staff
      Enter subscription end date: 4: CS Staff

    section Validation & Submission
      System validates package selection and date range: 4: System
      System flags missing or invalid fields: 2: CS Staff, System
      CS Staff corrects errors: 2: CS Staff
      Re-validate form: 4: CS Staff, System
      Click "Confirm Enrollment": 5: CS Staff

    section Confirmation
      System saves subscription record: 5: System
      System displays success message with Subscription ID and enrollment summary: 5: CS Staff, System
```

---

## Journey Summary

| Step | Actor | Action | Satisfaction |
|---|---|---|---|
| 1 | CS Staff | Log in and navigate to Customer Management | High |
| 2 | CS Staff + System | Search for and open an existing customer record | High |
| 3 | CS Staff + System | Initiate enrollment; form opens pre-loaded with customer details | High |
| 4 | CS Staff | Verify customer name and Customer ID on form header | High |
| 5 | CS Staff + System | Browse package list, select a package, review details panel | High |
| 6 | CS Staff | Enter subscription start date and end date | Medium |
| 7 | CS Staff + System | Validate form and correct any errors | Low–High |
| 8 | System | Save subscription record and return Subscription ID | High |

---

## Notes

- **Prerequisite — registered customer**: A valid Customer ID must already exist in the system (completed via US-01) before enrollment can proceed.
- **Customer confirmation**: The form header pre-loads the customer's name and ID so the CS Staff can verify the correct customer before making any changes.
- **Package detail panel**: Selecting a package immediately reveals full details (fee, call minutes, data allowance) to help the CS Staff confirm the right package with the customer.
- **Date validation**: End date must be strictly after start date; the system must prevent submission if this rule is violated.
- **Subscription ID**: Upon successful enrollment, a unique Subscription ID is generated and displayed for reference and audit purposes.
