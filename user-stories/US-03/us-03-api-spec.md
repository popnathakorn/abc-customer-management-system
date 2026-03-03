# US-03 — RESTful API Specification: Daily New Customer Report

**User Story:**
> As an **Operations Staff**, I want to view a daily report of newly registered customers, so that I can monitor business growth and follow up with onboarding processes.

**Traces To:** AC-03-02, AC-03-03, AC-03-04, AC-03-05, AC-03-06, AC-03-07, SR-2.1

---

## Overview

| Item | Detail |
|---|---|
| **Endpoint 1** | `GET /reports/customers/daily` |
| **Endpoint 2** | `GET /reports/customers/daily/export` |
| **Purpose** | Retrieve the daily new customer report for a given date; export as CSV |
| **Auth Required** | Yes — Bearer token (Operations Staff session) |

---

## Endpoint 1 — Get Daily New Customer Report

### `GET /reports/customers/daily`

Returns a JSON report of all customers registered on the specified date.

### Headers

| Header | Value | Required |
|---|---|---|
| `Authorization` | `Bearer <token>` | Yes |

### Query Parameters

| Parameter | Type | Required | Constraints | Description |
|---|---|---|---|---|
| `date` | string | No | Format: `YYYY-MM-DD`; must be a valid calendar date | The date to report on. Defaults to today's date if omitted. |

### Response — `200 OK`

Returned for both dates with data and dates with no registrations. An empty `customers` array with `total_count: 0` represents the no-data state (AC-03-06).

| Field | Type | Description |
|---|---|---|
| `status` | string | `"success"` |
| `report_date` | string | The date the report covers (`YYYY-MM-DD`) |
| `total_count` | integer | Total number of newly registered customers on the report date |
| `customers` | array | List of customer objects (empty array if none) |
| `customers[].row_number` | integer | Sequential row number starting from 1 |
| `customers[].customer_id` | string | System-generated Customer ID (pattern: `CUS-XXXXXX`) |
| `customers[].full_name` | string | Customer's full name |
| `customers[].phone_number` | string | Customer's phone number |
| `customers[].email_address` | string | Customer's email address |
| `customers[].registered_at` | string | Registration timestamp (`YYYY-MM-DD HH:MM:SS`) |
| `customers[].registered_by` | string | Full name of the CS Staff who registered the customer |

### Response — `400 Bad Request`

Returned when the `date` parameter is present but contains an invalid value.

| Field | Type | Description |
|---|---|---|
| `status` | string | `"error"` |
| `code` | string | `"INVALID_DATE"` |
| `message` | string | Human-readable description of the date error |

### Response — `401 Unauthorized`

Returned when the request is missing a valid Bearer token.

### Error Codes

| Code | HTTP Status | Description |
|---|---|---|
| `INVALID_DATE` | 400 | `date` parameter does not match `YYYY-MM-DD` or is not a valid calendar date |
| `UNAUTHORIZED` | 401 | Missing or invalid authentication token |

---

## Endpoint 2 — Export Daily New Customer Report as CSV

### `GET /reports/customers/daily/export`

Returns the same report data as Endpoint 1 but as a downloadable CSV file.

### Headers

| Header | Value | Required |
|---|---|---|
| `Authorization` | `Bearer <token>` | Yes |

### Query Parameters

| Parameter | Type | Required | Constraints | Description |
|---|---|---|---|---|
| `date` | string | No | Format: `YYYY-MM-DD`; must be a valid calendar date | The date to export. Defaults to today's date if omitted. |

### Response — `200 OK`

| Header | Value |
|---|---|
| `Content-Type` | `text/csv; charset=utf-8` |
| `Content-Disposition` | `attachment; filename="new-customers-{YYYY-MM-DD}.csv"` |

**CSV columns (in order):**

| Column | Source Field |
|---|---|
| No. | `row_number` |
| Customer ID | `customer_id` |
| Full Name | `full_name` |
| Phone Number | `phone_number` |
| Email Address | `email_address` |
| Registration Date & Time | `registered_at` |
| Registered By | `registered_by` |

Returns an empty CSV (header row only) when no registrations exist for the selected date.

### Response — `400 Bad Request` / `401 Unauthorized`

Same error contract as Endpoint 1.

---

## Sample Payloads

### Sample Request — with date parameter

```
GET /reports/customers/daily?date=2026-02-21
Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...
```

### Sample Request — without date (defaults to today)

```
GET /reports/customers/daily
Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...
```

### Sample Response — `200 OK` (with data)

```json
{
  "status": "success",
  "report_date": "2026-02-21",
  "total_count": 3,
  "customers": [
    {
      "row_number": 1,
      "customer_id": "CUS-000010",
      "full_name": "Somchai Jaidee",
      "phone_number": "0812345678",
      "email_address": "somchai.j@example.com",
      "registered_at": "2026-02-21 09:15:32",
      "registered_by": "Wanna Srisuk"
    },
    {
      "row_number": 2,
      "customer_id": "CUS-000011",
      "full_name": "Malee Sriwan",
      "phone_number": "0898765432",
      "email_address": "malee.s@example.com",
      "registered_at": "2026-02-21 11:03:47",
      "registered_by": "Wanna Srisuk"
    },
    {
      "row_number": 3,
      "customer_id": "CUS-000012",
      "full_name": "Prasit Phongphan",
      "phone_number": "0876543210",
      "email_address": "prasit.p@example.com",
      "registered_at": "2026-02-21 14:22:10",
      "registered_by": "Nattapong Chai"
    }
  ]
}
```

### Sample Response — `200 OK` (no data)

```json
{
  "status": "success",
  "report_date": "2026-02-15",
  "total_count": 0,
  "customers": []
}
```

### Sample Response — `400 Bad Request`

```json
{
  "status": "error",
  "code": "INVALID_DATE",
  "message": "The date '2026-13-01' is not a valid date. Use format YYYY-MM-DD."
}
```

### Sample Export Request

```
GET /reports/customers/daily/export?date=2026-02-21
Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...
```

### Sample CSV Output

```
No.,Customer ID,Full Name,Phone Number,Email Address,Registration Date & Time,Registered By
1,CUS-000010,Somchai Jaidee,0812345678,somchai.j@example.com,2026-02-21 09:15:32,Wanna Srisuk
2,CUS-000011,Malee Sriwan,0898765432,malee.s@example.com,2026-02-21 11:03:47,Wanna Srisuk
3,CUS-000012,Prasit Phongphan,0876543210,prasit.p@example.com,2026-02-21 14:22:10,Nattapong Chai
```

---

## Validation Rules Summary

| Rule | Parameter | Detail |
|---|---|---|
| Format | `date` | Must match `YYYY-MM-DD` when provided |
| Valid date | `date` | Must be a real calendar date (e.g. `2026-02-30` is rejected) |
| Default | `date` | If omitted, defaults to the server's current date |

---

## Traceability

| Artifact | Reference |
|---|---|
| User Story | US-03 |
| Acceptance Criteria | AC-03-02, AC-03-03, AC-03-04, AC-03-05, AC-03-06, AC-03-07 |
| Stakeholder Requirement | SR-2.1 |
| Database Entities | `CUSTOMER`, `USER` — see `diagrams/er-diagram.md` |
