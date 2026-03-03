# US-01 — Acceptance Criteria & Scope of Work

**User Story:**
> As a **Customer Service Staff**, I want to register a new customer's personal information, registered address, and document delivery address into the system, so that the company has complete and accurate customer records for service and communication purposes.

**Traces To:** SR-1.1, SR-3
**User Journey:** `user-stories/US-01/us-01-user-journey.md`

---

## Acceptance Criteria

### AC-01-01 — Registration Form Availability
- **Given** the CS Staff is logged in to the system,
- **When** they navigate to the Customer Management section and click "Add New Customer",
- **Then** the system must display a registration form with all required input fields.

### AC-01-02 — Personal Information Fields
- **Given** the registration form is displayed,
- **When** the CS Staff fills in personal information,
- **Then** the form must capture all of the following fields:
  - Full name
  - Date of birth
  - ID card number
  - Phone number
  - Email address

### AC-01-03 — Registered Address Fields
- **Given** the registration form is displayed,
- **When** the CS Staff fills in the registered address,
- **Then** the form must capture all of the following fields:
  - House number / street
  - Province, district (อำเภอ/เขต), and sub-district (ตำบล/แขวง) — all selectable
  - Postal code

### AC-01-04 — Document Delivery Address Fields
- **Given** the CS Staff reaches the document delivery address section,
- **When** the document delivery address is the same as the registered address,
- **Then** the CS Staff must be able to select a "Same as registered address" option that pre-fills all delivery address fields automatically.
- **And** when a different delivery address is needed, the CS Staff must be able to enter it manually with the same address fields.

### AC-01-05 — Required Field Validation
- **Given** the CS Staff attempts to submit the registration form,
- **When** any required field is empty or contains invalid data,
- **Then** the system must highlight the problematic field(s) and display an appropriate error message.
- **And** the form must not be submitted until all required fields are valid.

### AC-01-06 — Real-Time Validation Feedback
- **Given** the CS Staff is filling in the registration form,
- **When** a field loses focus or the user finishes typing,
- **Then** the system must validate the field in real-time and display inline error indicators without requiring a full form submission.

### AC-01-07 — Successful Submission
- **Given** all required fields are filled and valid,
- **When** the CS Staff clicks "Submit / Save",
- **Then** the system must:
  - Save the customer record to the database,
  - Generate and display a unique Customer ID,
  - Show a success confirmation message.

### AC-01-08 — Customer ID Visibility
- **Given** the customer record has been saved successfully,
- **When** the success confirmation is displayed,
- **Then** the generated Customer ID must be clearly visible so the CS Staff can record or share it with the customer.

### AC-01-09 — RESTful API Support
- **Given** the system exposes a RESTful API for customer registration (SR-3),
- **When** a valid POST request is submitted with all required parameters,
- **Then** the API must:
  - Store the customer record in the database,
  - Return a `201 Created` response with the generated Customer ID and a success status.
- **And** when required parameters are missing or invalid, the API must return a `400 Bad Request` response with descriptive error details.

---

## Scope of Work

### In Scope

| # | Deliverable | Description |
|---|---|---|
| 1 | **UI Registration Screen** | Design and implement a customer registration form covering personal information, registered address, and document delivery address |
| 2 | **Same-Address Toggle** | Implement a checkbox/toggle that pre-fills document delivery address from registered address |
| 3 | **Client-Side Validation** | Real-time field validation with inline error messages before form submission |
| 4 | **Server-Side Validation** | Backend validation of all inputs prior to database write |
| 5 | **Customer Record Storage** | Persist personal information, registered address, and document delivery address to the database (SR-1.1) |
| 6 | **Customer ID Generation** | Auto-generate a unique Customer ID upon successful registration |
| 7 | **Success / Error Feedback** | Display confirmation with Customer ID on success; display field-level errors on failure |
| 8 | **RESTful API Endpoint** | Design and implement a `POST /customers` endpoint with defined request parameters and response contract (SR-3) |
| 9 | **API Parameter Specification** | Document all API request parameters, data types, constraints, and sample request/response payloads |

### Out of Scope

| # | Item | Reason |
|---|---|---|
| 1 | Service package enrollment | Covered by US-02 |
| 2 | Edit or update existing customer records | Not part of initial registration flow |
| 3 | Customer search or lookup | Separate feature not requested in this story |
| 4 | Daily new customer report | Covered by US-03 |
| 5 | External system integration beyond API definition | Integration consumption is covered by US-05 |

---

## Definition of Done

- [ ] Registration form implemented with all required fields (AC-01-01 to AC-01-04)
- [ ] Real-time and server-side validation in place (AC-01-05, AC-01-06)
- [ ] Customer record successfully saved to database on valid submission (AC-01-07)
- [ ] Unique Customer ID generated and displayed upon success (AC-01-08)
- [ ] RESTful API endpoint functional with correct status codes and response body (AC-01-09)
- [ ] API parameters documented (request body, data types, constraints, sample payloads)
- [ ] Screen design reviewed and approved by stakeholder
- [ ] All acceptance criteria verified through functional testing
