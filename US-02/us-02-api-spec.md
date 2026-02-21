# US-02 — RESTful API Specification: Enroll Customer into Service Package

**User Story:**
> As a **Customer Service Staff**, I want to enroll a customer into a service package (e.g. 5G Next Speed, 599 ฿/month) and record the subscription start and end dates, so that the customer's active service is tracked and managed correctly.

**Traces To:** AC-02-09, SR-3

---

## Overview

| Item | Detail |
|---|---|
| **Endpoint** | `POST /subscriptions` |
| **Purpose** | Enroll an existing customer into a service package for a defined subscription period |
| **Auth Required** | Yes — Bearer token (CS Staff session) |
| **Content-Type** | `application/json` |

---

## Request

### Headers

| Header | Value | Required |
|---|---|---|
| `Content-Type` | `application/json` | Yes |
| `Authorization` | `Bearer <token>` | Yes |

### Request Body Parameters

| Field | Type | Required | Constraints | Description |
|---|---|---|---|---|
| `customer_id` | string | Yes | Must exist in the CUSTOMER table | Customer ID of the customer being enrolled (pattern: `CUS-XXXXXX`) |
| `package_id` | integer | Yes | Must exist in SERVICE_PACKAGE and `is_active = true` | ID of the service package to enroll the customer into |
| `start_date` | string | Yes | Format: `YYYY-MM-DD`; must be a valid calendar date | Subscription start date |
| `end_date` | string | Yes | Format: `YYYY-MM-DD`; must be strictly after `start_date` | Subscription end date |

---

## Response

### Success — `201 Created`

Returned when the subscription record is saved successfully and a Subscription ID is generated.

| Field | Type | Description |
|---|---|---|
| `status` | string | `"success"` |
| `subscription_id` | string | System-generated unique Subscription ID (pattern: `SUB-XXXXXX`) |
| `customer_id` | string | Customer ID of the enrolled customer |
| `package_id` | integer | ID of the enrolled service package |
| `package_name` | string | Name of the enrolled service package |
| `start_date` | string | Confirmed subscription start date (`YYYY-MM-DD`) |
| `end_date` | string | Confirmed subscription end date (`YYYY-MM-DD`) |
| `message` | string | Human-readable confirmation message |

### Error — `400 Bad Request`

Returned when required fields are missing, a referenced record does not exist, or date validation fails.

| Field | Type | Description |
|---|---|---|
| `status` | string | `"error"` |
| `code` | string | Machine-readable error code (see Error Codes table) |
| `message` | string | Human-readable summary of the error |
| `errors` | array | List of field-level error objects |
| `errors[].field` | string | Field name that caused the error (e.g. `"end_date"`) |
| `errors[].message` | string | Description of why the field failed |

### Error — `401 Unauthorized`

Returned when the request is missing a valid Bearer token.

### Error Codes

| Code | HTTP Status | Description |
|---|---|---|
| `VALIDATION_ERROR` | 400 | One or more fields are missing or contain invalid values |
| `CUSTOMER_NOT_FOUND` | 400 | The supplied `customer_id` does not exist in the system |
| `PACKAGE_NOT_FOUND` | 400 | The supplied `package_id` does not exist or is not active |
| `INVALID_DATE_RANGE` | 400 | `end_date` is on or before `start_date` |
| `UNAUTHORIZED` | 401 | Missing or invalid authentication token |

---

## Sample Payloads

### Sample Request

```json
POST /subscriptions
Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...
Content-Type: application/json

{
  "customer_id": "CUS-000001",
  "package_id": 3,
  "start_date": "2026-03-01",
  "end_date": "2027-02-28"
}
```

### Sample Response — `201 Created`

```json
{
  "status": "success",
  "subscription_id": "SUB-000001",
  "customer_id": "CUS-000001",
  "package_id": 3,
  "package_name": "5G Next Speed",
  "start_date": "2026-03-01",
  "end_date": "2027-02-28",
  "message": "Customer enrolled into service package successfully."
}
```

### Sample Response — `400 Bad Request` (missing field)

```json
{
  "status": "error",
  "code": "VALIDATION_ERROR",
  "message": "One or more fields are invalid.",
  "errors": [
    {
      "field": "package_id",
      "message": "package_id is required."
    }
  ]
}
```

### Sample Response — `400 Bad Request` (invalid date range)

```json
{
  "status": "error",
  "code": "INVALID_DATE_RANGE",
  "message": "Subscription date range is invalid.",
  "errors": [
    {
      "field": "end_date",
      "message": "end_date must be strictly after start_date."
    }
  ]
}
```

### Sample Response — `400 Bad Request` (customer not found)

```json
{
  "status": "error",
  "code": "CUSTOMER_NOT_FOUND",
  "message": "Customer with ID 'CUS-000001' does not exist.",
  "errors": [
    {
      "field": "customer_id",
      "message": "No customer record found for the supplied customer_id."
    }
  ]
}
```

---

## Validation Rules Summary

| Rule | Field | Detail |
|---|---|---|
| Required | `customer_id` | Must not be null or empty string |
| Required | `package_id` | Must not be null |
| Required | `start_date` | Must not be null or empty string |
| Required | `end_date` | Must not be null or empty string |
| Format | `start_date` | Must match `YYYY-MM-DD` and be a valid calendar date |
| Format | `end_date` | Must match `YYYY-MM-DD` and be a valid calendar date |
| Date range | `end_date` | Must be strictly after `start_date` |
| Existence | `customer_id` | Must match an existing record in the CUSTOMER table |
| Existence | `package_id` | Must match an existing, active record in the SERVICE_PACKAGE table (`is_active = true`) |

---

## Traceability

| Artifact | Reference |
|---|---|
| User Story | US-02 |
| Acceptance Criteria | AC-02-09 |
| Stakeholder Requirement | SR-3 |
| Database Entities | `SUBSCRIPTION`, `SERVICE_PACKAGE`, `CUSTOMER` — see `diagrams/er-diagram.md` |