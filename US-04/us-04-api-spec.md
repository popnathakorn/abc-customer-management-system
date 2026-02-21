# US-04 — RESTful API Specification: Daily Subscription History Report

**User Story:**
> As an **Operations Staff**, I want to view a daily service subscription history report for each customer, so that I can audit subscription changes and ensure accurate billing records.

**Traces To:** AC-04-02, AC-04-03, AC-04-04, AC-04-05, AC-04-06, AC-04-07, SR-2.2

---

## Overview

| Item | Detail |
|---|---|
| **Endpoint 1** | `GET /reports/subscriptions/daily` |
| **Endpoint 2** | `GET /reports/subscriptions/daily/export` |
| **Purpose** | Retrieve the daily subscription history report for a given date; export as CSV |
| **Auth Required** | Yes — Bearer token (Operations Staff session) |

---

## Endpoint 1 — Get Daily Subscription History Report

### `GET /reports/subscriptions/daily`

Returns a JSON report of all subscriptions enrolled on the specified date.

### Headers

| Header | Value | Required |
|---|---|---|
| `Authorization` | `Bearer <token>` | Yes |

### Query Parameters

| Parameter | Type | Required | Constraints | Description |
|---|---|---|---|---|
| `date` | string | No | Format: `YYYY-MM-DD`; must be a valid calendar date | The date to report on. Defaults to today's date if omitted. |

### Response — `200 OK`

Returned for both dates with data and dates with no subscription records. An empty `subscriptions` array with `total_count: 0` represents the no-data state (AC-04-06).

| Field | Type | Description |
|---|---|---|
| `status` | string | `"success"` |
| `report_date` | string | The date the report covers (`YYYY-MM-DD`) |
| `total_count` | integer | Total number of subscriptions enrolled on the report date |
| `subscriptions` | array | List of subscription objects (empty array if none) |
| `subscriptions[].row_number` | integer | Sequential row number starting from 1 |
| `subscriptions[].customer_id` | string | System-generated Customer ID (pattern: `CUS-XXXXXX`) |
| `subscriptions[].customer_full_name` | string | Customer's full name |
| `subscriptions[].subscription_id` | string | System-generated Subscription ID (pattern: `SUB-XXXXXX`) |
| `subscriptions[].package_name` | string | Name of the enrolled service package |
| `subscriptions[].start_date` | string | Subscription start date (`YYYY-MM-DD`) |
| `subscriptions[].end_date` | string | Subscription end date (`YYYY-MM-DD`) |
| `subscriptions[].enrolled_by` | string | Full name of the CS Staff who enrolled the subscription |
| `subscriptions[].enrolled_at` | string | Enrollment timestamp (`YYYY-MM-DD HH:MM:SS`) |

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

## Endpoint 2 — Export Daily Subscription History Report as CSV

### `GET /reports/subscriptions/daily/export`

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
| `Content-Disposition` | `attachment; filename="subscription-history-{YYYY-MM-DD}.csv"` |

**CSV columns (in order):**

| Column | Source Field |
|---|---|
| No. | `row_number` |
| Customer ID | `customer_id` |
| Customer Full Name | `customer_full_name` |
| Subscription ID | `subscription_id` |
| Package Name | `package_name` |
| Start Date | `start_date` |
| End Date | `end_date` |
| Enrolled By | `enrolled_by` |
| Enrolled Date & Time | `enrolled_at` |

Returns an empty CSV (header row only) when no subscription records exist for the selected date.

### Response — `400 Bad Request` / `401 Unauthorized`

Same error contract as Endpoint 1.

---

## Sample Payloads

### Sample Request — with date parameter

```
GET /reports/subscriptions/daily?date=2026-02-21
Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...
```

### Sample Request — without date (defaults to today)

```
GET /reports/subscriptions/daily
Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...
```

### Sample Response — `200 OK` (with data)

```json
{
  "status": "success",
  "report_date": "2026-02-21",
  "total_count": 3,
  "subscriptions": [
    {
      "row_number": 1,
      "customer_id": "CUS-000010",
      "customer_full_name": "Somchai Jaidee",
      "subscription_id": "SUB-000015",
      "package_name": "5G Next Speed",
      "start_date": "2026-03-01",
      "end_date": "2027-02-28",
      "enrolled_by": "Wanna Srisuk",
      "enrolled_at": "2026-02-21 09:30:11"
    },
    {
      "row_number": 2,
      "customer_id": "CUS-000011",
      "customer_full_name": "Malee Sriwan",
      "subscription_id": "SUB-000016",
      "package_name": "4G Value Plus",
      "start_date": "2026-03-01",
      "end_date": "2026-08-31",
      "enrolled_by": "Wanna Srisuk",
      "enrolled_at": "2026-02-21 11:15:44"
    },
    {
      "row_number": 3,
      "customer_id": "CUS-000012",
      "customer_full_name": "Prasit Phongphan",
      "subscription_id": "SUB-000017",
      "package_name": "5G Next Speed",
      "start_date": "2026-03-01",
      "end_date": "2027-02-28",
      "enrolled_by": "Nattapong Chai",
      "enrolled_at": "2026-02-21 14:50:02"
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
  "subscriptions": []
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
GET /reports/subscriptions/daily/export?date=2026-02-21
Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...
```

### Sample CSV Output

```
No.,Customer ID,Customer Full Name,Subscription ID,Package Name,Start Date,End Date,Enrolled By,Enrolled Date & Time
1,CUS-000010,Somchai Jaidee,SUB-000015,5G Next Speed,2026-03-01,2027-02-28,Wanna Srisuk,2026-02-21 09:30:11
2,CUS-000011,Malee Sriwan,SUB-000016,4G Value Plus,2026-03-01,2026-08-31,Wanna Srisuk,2026-02-21 11:15:44
3,CUS-000012,Prasit Phongphan,SUB-000017,5G Next Speed,2026-03-01,2027-02-28,Nattapong Chai,2026-02-21 14:50:02
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
| User Story | US-04 |
| Acceptance Criteria | AC-04-02, AC-04-03, AC-04-04, AC-04-05, AC-04-06, AC-04-07 |
| Stakeholder Requirement | SR-2.2 |
| Database Entities | `SUBSCRIPTION`, `CUSTOMER`, `SERVICE_PACKAGE`, `USER` — see `diagrams/er-diagram.md` |
