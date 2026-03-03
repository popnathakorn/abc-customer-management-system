# US-02 — Acceptance Criteria & Scope of Work

**User Story:**
> As a **Customer Service Staff**, I want to enroll a customer into a service package (e.g. 5G Next Speed, 599 ฿/month) and record the subscription start and end dates, so that the customer's active service is tracked and managed correctly.

**Traces To:** SR-1.2, SR-3
**User Journey:** `user-stories/US-02/us-02-user-journey.md`

---

## Acceptance Criteria

### AC-02-01 — Enrollment Form Availability
- **Given** the CS Staff is logged in to the system,
- **When** they open an existing customer record and click "Add Service Enrollment",
- **Then** the system must display a service enrollment form pre-loaded with the customer's name and Customer ID.

### AC-02-02 — Customer Confirmation Prior to Enrollment
- **Given** the enrollment form is displayed,
- **When** the CS Staff reviews the form header,
- **Then** the system must clearly show the target customer's name and Customer ID so the CS Staff can confirm the correct customer before proceeding.

### AC-02-03 — Service Package Selection
- **Given** the enrollment form is displayed,
- **When** the CS Staff selects a service package,
- **Then** the form must present a selectable list of available packages, each showing:
  - Package name (e.g. 5G Next Speed)
  - Monthly fee (฿)
  - Key inclusions (e.g. call minutes to all networks, data allowance and speed tier)

### AC-02-04 — Package Details Display on Selection
- **Given** the CS Staff selects a package from the list,
- **When** the selection is made,
- **Then** the system must display the full package details in a summary panel, including:
  - Package name
  - Monthly fee
  - Included call minutes
  - Data allowance (volume and speed tier)

### AC-02-05 — Subscription Date Fields
- **Given** the enrollment form is displayed,
- **When** the CS Staff fills in the subscription period,
- **Then** the form must capture all of the following fields:
  - Subscription start date
  - Subscription end date

### AC-02-06 — Subscription Date Validation
- **Given** the CS Staff has entered subscription start and end dates,
- **When** the end date is on or before the start date,
- **Then** the system must display an inline error message on the end date field and prevent form submission until a valid date range is entered.

### AC-02-07 — Required Field Validation
- **Given** the CS Staff attempts to submit the enrollment form,
- **When** any required field (package selection, start date, or end date) is empty or invalid,
- **Then** the system must highlight the problematic field(s) and display an appropriate error message.
- **And** the form must not be submitted until all required fields are valid.

### AC-02-08 — Successful Enrollment
- **Given** all required fields are filled and valid,
- **When** the CS Staff clicks "Confirm Enrollment",
- **Then** the system must:
  - Save the subscription record to the database linked to the customer's Customer ID,
  - Generate and display a unique Subscription ID,
  - Show a success confirmation message summarising the enrolled package, subscription start date, and subscription end date.

### AC-02-09 — RESTful API Support
- **Given** the system exposes a RESTful API for service enrollment (SR-3),
- **When** a valid POST request is submitted with all required parameters,
- **Then** the API must:
  - Store the subscription record in the database,
  - Return a `201 Created` response with the generated Subscription ID and a success status.
- **And** when required parameters are missing, the customer ID does not exist, or date validation fails, the API must return a `400 Bad Request` response with descriptive error details.

---

## Scope of Work

### In Scope

| # | Deliverable | Description |
|---|---|---|
| 1 | **UI Enrollment Screen** | Design and implement a service enrollment form that presents available packages and captures subscription start and end dates |
| 2 | **Customer Confirmation Display** | Pre-load and display the target customer's name and Customer ID on the enrollment form header |
| 3 | **Package List & Detail Panel** | Display a selectable list of available service packages with key details; show full package details upon selection |
| 4 | **Subscription Date Entry** | Input fields for subscription start date and end date with date-picker support |
| 5 | **Client-Side Validation** | Real-time field validation including date-range check (start before end) with inline error messages |
| 6 | **Server-Side Validation** | Backend validation of all inputs — package existence, customer existence, date range — prior to database write |
| 7 | **Subscription Record Storage** | Persist the subscription record (Customer ID, package reference, start date, end date) to the database (SR-1.2) |
| 8 | **Subscription ID Generation** | Auto-generate a unique Subscription ID upon successful enrollment |
| 9 | **Success / Error Feedback** | Display confirmation with Subscription ID and enrollment summary on success; display field-level errors on failure |
| 10 | **RESTful API Endpoint** | Design and implement a `POST /subscriptions` endpoint with defined request parameters and response contract (SR-3) |
| 11 | **API Parameter Specification** | Document all API request parameters, data types, constraints, and sample request/response payloads |

### Out of Scope

| # | Item | Reason |
|---|---|---|
| 1 | Customer registration | Covered by US-01; a valid Customer ID is a prerequisite for enrollment |
| 2 | Modifying or cancelling an existing subscription | Not part of the initial enrollment flow |
| 3 | Billing or payment processing | Outside the system boundary; handled by a separate billing system |
| 4 | Package creation or management | Admin/configuration feature not requested in this story |
| 5 | Daily subscription history report | Covered by US-04 |
| 6 | External system integration beyond API definition | Integration consumption is covered by US-05 |

---

## Definition of Done

- [ ] Enrollment form implemented with customer confirmation, package selection, and date fields (AC-02-01 to AC-02-05)
- [ ] Package list populated and selectable; full package details displayed upon selection (AC-02-03, AC-02-04)
- [ ] Date-range validation in place — end date must be after start date (AC-02-06)
- [ ] Required field validation (client-side and server-side) implemented (AC-02-07)
- [ ] Subscription record successfully saved to database on valid submission (AC-02-08)
- [ ] Unique Subscription ID generated and displayed upon success (AC-02-08)
- [ ] RESTful API endpoint functional with correct status codes and response body (AC-02-09)
- [ ] API parameters documented (request body, data types, constraints, sample payloads)
- [ ] Screen design reviewed and approved by stakeholder
- [ ] All acceptance criteria verified through functional testing
