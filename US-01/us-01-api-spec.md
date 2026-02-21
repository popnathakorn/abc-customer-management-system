# US-01 — RESTful API Specification: Register New Customer

**User Story:**
> As a **Customer Service Staff**, I want to register a new customer's personal information, registered address, and document delivery address into the system, so that the company has complete and accurate customer records for service and communication purposes.

**Traces To:** AC-01-09, SR-3

---

## Overview

| Item | Detail |
|---|---|
| **Endpoint** | `POST /customers` |
| **Purpose** | Register a new customer with personal information and addresses |
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

#### Personal Information

| Field | Type | Required | Constraints | Description |
|---|---|---|---|---|
| `full_name` | string | Yes | 1–150 chars | Customer's full name |
| `date_of_birth` | string | Yes | Format: `YYYY-MM-DD` | Customer's date of birth |
| `id_card_number` | string | Yes | Exactly 13 digits, numeric only, unique | National ID card number |
| `phone_number` | string | Yes | 9–10 digits, numeric only | Customer's contact phone number |
| `email_address` | string | Yes | Valid email format, max 255 chars | Customer's email address |

#### Registered Address (`registered_address`)

| Field | Type | Required | Constraints | Description |
|---|---|---|---|---|
| `registered_address.house_street` | string | Yes | 1–255 chars | House number and street |
| `registered_address.sub_district` | string | Yes | 1–100 chars | Sub-district name (ตำบล / แขวง) |
| `registered_address.district` | string | Yes | 1–100 chars | District name (อำเภอ / เขต) |
| `registered_address.province` | string | Yes | 1–100 chars | Province name (จังหวัด) |
| `registered_address.postal_code` | string | Yes | Exactly 5 digits, numeric only | Postal code |

#### Document Delivery Address (`delivery_address`)

| Field | Type | Required | Constraints | Description |
|---|---|---|---|---|
| `delivery_address.same_as_registered` | boolean | Yes | `true` or `false` | If `true`, delivery address mirrors registered address; address fields below are ignored |
| `delivery_address.house_street` | string | Conditional | Required if `same_as_registered` is `false`; 1–255 chars | House number and street |
| `delivery_address.sub_district` | string | Conditional | Required if `same_as_registered` is `false`; 1–100 chars | Sub-district name (ตำบล / แขวง) |
| `delivery_address.district` | string | Conditional | Required if `same_as_registered` is `false`; 1–100 chars | District name (อำเภอ / เขต) |
| `delivery_address.province` | string | Conditional | Required if `same_as_registered` is `false`; 1–100 chars | Province name (จังหวัด) |
| `delivery_address.postal_code` | string | Conditional | Required if `same_as_registered` is `false`; exactly 5 digits | Postal code |

---

## Response

### Success — `201 Created`

Returned when the customer record is saved successfully and a Customer ID is generated.

| Field | Type | Description |
|---|---|---|
| `status` | string | `"success"` |
| `customer_id` | string | System-generated unique Customer ID (pattern: `CUS-XXXXXX`) |
| `message` | string | Human-readable confirmation message |

### Error — `400 Bad Request`

Returned when required fields are missing or fail validation.

| Field | Type | Description |
|---|---|---|
| `status` | string | `"error"` |
| `code` | string | Machine-readable error code (see Error Codes table) |
| `message` | string | Human-readable summary of the error |
| `errors` | array | List of field-level error objects |
| `errors[].field` | string | Dot-notation field path that failed (e.g. `"id_card_number"`) |
| `errors[].message` | string | Description of why the field failed |

### Error — `409 Conflict`

Returned when the submitted `id_card_number` already exists in the system.

| Field | Type | Description |
|---|---|---|
| `status` | string | `"error"` |
| `code` | string | `"DUPLICATE_CUSTOMER"` |
| `message` | string | Human-readable conflict description |

### Error — `401 Unauthorized`

Returned when the request is missing a valid Bearer token.

### Error Codes

| Code | HTTP Status | Description |
|---|---|---|
| `VALIDATION_ERROR` | 400 | One or more fields are missing or contain invalid values |
| `DUPLICATE_CUSTOMER` | 409 | A customer with the same `id_card_number` already exists |
| `UNAUTHORIZED` | 401 | Missing or invalid authentication token |

---

## Sample Payloads

### Sample Request — Delivery address differs from registered address

```json
POST /customers
Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...
Content-Type: application/json

{
  "full_name": "Somchai Jaidee",
  "date_of_birth": "1990-04-15",
  "id_card_number": "1234567890123",
  "phone_number": "0812345678",
  "email_address": "somchai.j@example.com",
  "registered_address": {
    "house_street": "123 Sukhumvit Rd",
    "sub_district": "Khlong Toei Nuea",
    "district": "Watthana",
    "province": "Bangkok",
    "postal_code": "10110"
  },
  "delivery_address": {
    "same_as_registered": false,
    "house_street": "456 Rama IX Rd",
    "sub_district": "Bang Kapi",
    "district": "Huai Khwang",
    "province": "Bangkok",
    "postal_code": "10310"
  }
}
```

### Sample Request — Delivery address same as registered address

```json
POST /customers
Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...
Content-Type: application/json

{
  "full_name": "Malee Sriwan",
  "date_of_birth": "1985-11-30",
  "id_card_number": "9876543210987",
  "phone_number": "0898765432",
  "email_address": "malee.s@example.com",
  "registered_address": {
    "house_street": "78 Charoen Nakhon Rd",
    "sub_district": "Khlong Ton Sai",
    "district": "Khlong San",
    "province": "Bangkok",
    "postal_code": "10600"
  },
  "delivery_address": {
    "same_as_registered": true
  }
}
```

### Sample Response — `201 Created`

```json
{
  "status": "success",
  "customer_id": "CUS-000001",
  "message": "Customer registered successfully."
}
```

### Sample Response — `400 Bad Request`

```json
{
  "status": "error",
  "code": "VALIDATION_ERROR",
  "message": "One or more fields are invalid.",
  "errors": [
    {
      "field": "id_card_number",
      "message": "ID card number must be exactly 13 numeric digits."
    },
    {
      "field": "delivery_address.postal_code",
      "message": "Postal code is required when same_as_registered is false."
    }
  ]
}
```

### Sample Response — `409 Conflict`

```json
{
  "status": "error",
  "code": "DUPLICATE_CUSTOMER",
  "message": "A customer with ID card number '1234567890123' already exists."
}
```

---

## Validation Rules Summary

| Rule | Field | Detail |
|---|---|---|
| Required | All personal info fields | Must not be null or empty string |
| Required | `registered_address` (all sub-fields) | Must not be null or empty string — includes `sub_district` and `district` as separate fields |
| Required | `delivery_address.same_as_registered` | Must be explicitly `true` or `false` |
| Conditional required | `delivery_address` address fields | Required when `same_as_registered` is `false` |
| Format | `date_of_birth` | Must match `YYYY-MM-DD` and be a valid calendar date |
| Format | `email_address` | Must match standard email format (RFC 5322) |
| Length | `id_card_number` | Exactly 13 characters, digits only |
| Length | `phone_number` | 9–10 characters, digits only |
| Length | `postal_code` | Exactly 5 characters, digits only |
| Uniqueness | `id_card_number` | Must not already exist in the CUSTOMER table |

---

## Traceability

| Artifact | Reference |
|---|---|
| User Story | US-01 |
| Acceptance Criteria | AC-01-09 |
| Stakeholder Requirement | SR-3 |
| Database Entities | `CUSTOMER`, `ADDRESS` — see `diagrams/er-diagram.md` |