**Name:** Nathakorn Soontornworachan
**Position:** SA/System Analyst

---

## 1. ออกแบบฐานข้อมูลสำหรับจัดเก็บข้อมูลลูกค้าที่สมัครใช้บริการเครือข่ายมือถือ ABC
(Design a database to store customer data for those subscribing to ABC mobile network services)

### ER Diagram — ABC-CMS (ABC Customer Management System)

```mermaid
erDiagram
    USER {
        int     user_id         PK
        string  username
        string  full_name
        string  password_hash
        string  role
        datetime created_at
    }

    CUSTOMER {
        string  customer_id     PK  "CUS-XXXXXX"
        string  full_name
        date    date_of_birth
        string  id_card_number  UK
        string  phone_number
        string  email_address
        int     created_by      FK
        datetime created_at
    }

    ADDRESS {
        int     address_id      PK
        string  customer_id     FK
        string  address_type        "REGISTERED | DELIVERY"
        string  house_street
        string  sub_district        "ตำบล / แขวง"
        string  district            "อำเภอ / เขต"
        string  province
        string  postal_code
        boolean same_as_registered
    }

    SERVICE_PACKAGE {
        int     package_id      PK
        string  package_name
        decimal monthly_fee
        int     call_minutes
        int     data_gb
        string  speed_tier          "e.g. 5G, 4G"
        boolean is_active
    }

    SUBSCRIPTION {
        string  subscription_id PK  "SUB-XXXXXX"
        string  customer_id     FK
        int     package_id      FK
        date    start_date
        date    end_date
        int     enrolled_by     FK
        datetime created_at
    }

    USER ||--o{ CUSTOMER : "registers"
    CUSTOMER ||--|{ ADDRESS : "has"
    CUSTOMER ||--o{ SUBSCRIPTION : "enrolls in"
    SERVICE_PACKAGE ||--o{ SUBSCRIPTION : "is subscribed via"
    USER ||--o{ SUBSCRIPTION : "enrolls"
```

---

### 1.1 เก็บข้อมูลลูกค้าของบริษัทประกอบไปด้วยข้อมูลส่วนตัวต่างๆ, ที่อยู่ตามทะเบียนบ้าน, ที่อยู่ในการจัดส่งเอกสาร
(Store company customer data including various personal information, registered address, and document delivery address)

**Entities:** `CUSTOMER`, `ADDRESS`

| Entity | Description |
|---|---|
| **CUSTOMER** | Core customer record; `customer_id` is system-generated on successful registration (pattern: `CUS-XXXXXX`). Stores personal information including full name, date of birth, ID card number (unique), phone number, and email address. |
| **ADDRESS** | Stores both registered address and document delivery address; `address_type` field distinguishes between `REGISTERED` and `DELIVERY`. When `same_as_registered = true` on the `DELIVERY` row, delivery address fields mirror the registered address. |

**Key fields — CUSTOMER:**

| Field | Type | Description |
|---|---|---|
| `customer_id` | string (PK) | Auto-generated unique customer ID (pattern: `CUS-XXXXXX`) |
| `full_name` | string | Customer's full name |
| `date_of_birth` | date | Customer's date of birth |
| `id_card_number` | string (UK) | National ID card number — unique across all customers |
| `phone_number` | string | Customer's contact phone number |
| `email_address` | string | Customer's email address |
| `created_by` | int (FK) | References `USER.user_id` — CS Staff who registered the customer |
| `created_at` | datetime | Timestamp of registration |

**Key fields — ADDRESS:**

| Field | Type | Description |
|---|---|---|
| `address_id` | int (PK) | Auto-generated address record ID |
| `customer_id` | string (FK) | References `CUSTOMER.customer_id` |
| `address_type` | string | `REGISTERED` or `DELIVERY` |
| `house_street` | string | House number and street |
| `sub_district` | string | ตำบล (Tambon) for provincial addresses / แขวง (Khwaeng) for Bangkok |
| `district` | string | อำเภอ (Amphoe) / เขต (Khet) |
| `province` | string | Province name |
| `postal_code` | string | 5-digit postal code |
| `same_as_registered` | boolean | If `true` on a `DELIVERY` row, delivery address mirrors the registered address |

**Relationship:** `CUSTOMER ||--|{ ADDRESS` — each customer must have at least one registered address; optionally a separate delivery address.

---

### 1.2 เก็บข้อมูลการสมัครใช้บริการต่างๆ ของลูกค้า เช่น แพ็กเกจ, รายละเอียดของแพ็กเกจ, วันที่สมัคร, วันที่สิ้นสุด เป็นต้น
(Store customer service subscription data such as packages — example: "5G Next Speed", monthly fee 599 Baht, calls to all networks 300 minutes, continuous 5G internet 20GB — package details, subscription date, end date, etc.)

**Entities:** `SERVICE_PACKAGE`, `SUBSCRIPTION`

| Entity | Description |
|---|---|
| **SERVICE_PACKAGE** | Catalogue of available service packages with pricing and inclusions (e.g. 5G Next Speed, 599 ฿/month, 300 call minutes, 20 GB data). `is_active` controls which packages are available for enrollment. |
| **SUBSCRIPTION** | Links a customer to a service package for a defined period; `subscription_id` is system-generated on successful enrollment (pattern: `SUB-XXXXXX`). Records the start date, end date, and the CS Staff who performed the enrollment. |

**Key fields — SERVICE_PACKAGE:**

| Field | Type | Description |
|---|---|---|
| `package_id` | int (PK) | Auto-generated package ID |
| `package_name` | string | Package name (e.g. `5G Next Speed`) |
| `monthly_fee` | decimal | Monthly fee in Baht (e.g. `599.00`) |
| `call_minutes` | int | Included call minutes to all networks (e.g. `300`) |
| `data_gb` | int | Included data in GB (e.g. `20`) |
| `speed_tier` | string | Network speed tier (e.g. `5G`, `4G`) |
| `is_active` | boolean | Controls package availability in the enrollment form |

**Key fields — SUBSCRIPTION:**

| Field | Type | Description |
|---|---|---|
| `subscription_id` | string (PK) | Auto-generated unique subscription ID (pattern: `SUB-XXXXXX`) |
| `customer_id` | string (FK) | References `CUSTOMER.customer_id` |
| `package_id` | int (FK) | References `SERVICE_PACKAGE.package_id` |
| `start_date` | date | Subscription start date |
| `end_date` | date | Subscription end date |
| `enrolled_by` | int (FK) | References `USER.user_id` — CS Staff who performed the enrollment |
| `created_at` | datetime | Timestamp of enrollment record creation |

**Relationships:**
- `CUSTOMER ||--o{ SUBSCRIPTION` — a customer can have multiple service subscriptions over time.
- `SERVICE_PACKAGE ||--o{ SUBSCRIPTION` — a package can be subscribed to by many customers.

---

## 2. จากฐานข้อมูลที่ออกแบบในข้อ 1 ให้เขียน Query สำหรับดึงรายงาน
(From the database designed in item 1, write Queries to pull the following reports)

### 2.1 รายงานลูกค้าใหม่แต่ละวัน
(New customer report per day)

**Tables used:** `CUSTOMER` (alias `c`), `USER` (alias `u`)

#### Query 1 — Main Report Query

Returns one row per customer registered on the target date, ordered by registration time ascending.

```sql
-- ============================================================
-- US-08: Daily New Customer Registration Report
-- SR-2.1: New customer report per day
--
-- PARAMETER: Replace the date value below with the target date
--            Format: 'YYYY-MM-DD'
-- ============================================================

SELECT
    ROW_NUMBER() OVER (ORDER BY c.created_at ASC)  AS row_num,
    c.customer_id,
    c.full_name                                     AS customer_name,
    c.phone_number,
    c.email_address,
    c.created_at                                    AS registration_datetime,
    u.full_name                                     AS registered_by
FROM
    CUSTOMER c
    INNER JOIN USER u ON c.created_by = u.user_id
WHERE
    DATE(c.created_at) = '2026-02-21'   -- << CHANGE THIS DATE
ORDER BY
    c.created_at ASC;
```

#### Query 2 — Total Count Query

Returns the total number of new registrations for the target date.

```sql
-- ============================================================
-- US-08: Daily New Customer Registration — Total Count
-- SR-2.1: New customer report per day
--
-- PARAMETER: Replace the date value below with the target date
--            Format: 'YYYY-MM-DD'
-- ============================================================

SELECT
    COUNT(*) AS total_new_customers
FROM
    CUSTOMER c
WHERE
    DATE(c.created_at) = '2026-02-21';  -- << CHANGE THIS DATE
```

#### Expected Output — Query 1

| Column | Data Type | Example |
|---|---|---|
| `row_num` | integer | `1` |
| `customer_id` | string | `CUS-000042` |
| `customer_name` | string | `สมชาย ใจดี` |
| `phone_number` | string | `081-234-5678` |
| `email_address` | string | `somchai@email.com` |
| `registration_datetime` | datetime | `2026-02-21 09:14:03` |
| `registered_by` | string | `นางสาว อรุณ พนักงาน` |

#### Expected Output — Query 2

| Column | Data Type | Example |
|---|---|---|
| `total_new_customers` | integer | `7` |

---

### 2.2 รายงานประวัติการสมัครใช้บริการของลูกค้าแต่ละคน ในแต่ละวัน
(Report on service subscription history for each customer, per day)

**Tables used:** `SUBSCRIPTION` (alias `s`), `CUSTOMER` (alias `c`), `SERVICE_PACKAGE` (alias `sp`), `USER` (alias `u`)

#### Query 1 — Main Report Query

Returns one row per subscription enrolled on the target date, ordered by enrollment time ascending.

```sql
-- ============================================================
-- US-09: Daily Subscription History Report
-- SR-2.2: Service subscription history report per customer per day
--
-- PARAMETER: Replace the date value below with the target date
--            Format: 'YYYY-MM-DD'
-- ============================================================

SELECT
    ROW_NUMBER() OVER (ORDER BY s.created_at ASC)  AS row_num,
    c.customer_id,
    c.full_name                                     AS customer_name,
    s.subscription_id,
    sp.package_name,
    s.start_date,
    s.end_date,
    u.full_name                                     AS enrolled_by,
    s.created_at                                    AS enrolled_datetime
FROM
    SUBSCRIPTION s
    INNER JOIN CUSTOMER       c  ON s.customer_id = c.customer_id
    INNER JOIN SERVICE_PACKAGE sp ON s.package_id  = sp.package_id
    INNER JOIN USER            u  ON s.created_by  = u.user_id
WHERE
    DATE(s.created_at) = '2026-02-21'   -- << CHANGE THIS DATE
ORDER BY
    s.created_at ASC;
```

#### Query 2 — Total Count Query

Returns the total number of subscriptions enrolled on the target date.

```sql
-- ============================================================
-- US-09: Daily Subscription History Report — Total Count
-- SR-2.2: Service subscription history report per customer per day
--
-- PARAMETER: Replace the date value below with the target date
--            Format: 'YYYY-MM-DD'
-- ============================================================

SELECT
    COUNT(*) AS total_subscriptions
FROM
    SUBSCRIPTION s
WHERE
    DATE(s.created_at) = '2026-02-21';  -- << CHANGE THIS DATE
```

#### Expected Output — Query 1

| Column | Data Type | Example |
|---|---|---|
| `row_num` | integer | `1` |
| `customer_id` | string | `CUS-000042` |
| `customer_name` | string | `สมชาย ใจดี` |
| `subscription_id` | string | `SUB-000087` |
| `package_name` | string | `5G Next Speed` |
| `start_date` | date | `2026-02-21` |
| `end_date` | date | `2027-02-20` |
| `enrolled_by` | string | `นางสาว อรุณ พนักงาน` |
| `enrolled_datetime` | datetime | `2026-02-21 10:32:15` |

#### Expected Output — Query 2

| Column | Data Type | Example |
|---|---|---|
| `total_subscriptions` | integer | `12` |

---

## 3. จากข้อ 1 ออกแบบหน้าจอ และ RESTful API และ Parameter API สำหรับจัดเก็บข้อมูลลูกค้าที่สมัครใช้บริการเครือข่ายมือถือ ABC
(From item 1, design the screen, RESTful API, and API parameters for storing customer data for ABC mobile network service subscriptions)

---

### Screen Design

#### US-01 — Register New Customer

Flow: Login → Customer Management → Register Form → Validation Errors → Success Confirmation → (View Customer Profile / Register Another Customer)

![US-01 Wireframes](US-01/us-01-wireframes.drawio.svg)

#### US-02 — Enroll Customer into Service Package

Flow: Login → Customer Management → Customer Profile → Service Enrollment Form → Validation Errors → Success Confirmation → (View Profile / Add Another Enrollment)

![US-02 Wireframes](US-02/us-02-wireframes.drawio.svg)

---

### RESTful API and API Parameters

#### US-01 — Register New Customer

| Item | Detail |
|---|---|
| **Endpoint** | `POST /customers` |
| **Purpose** | Register a new customer with personal information and addresses |
| **Auth Required** | Yes — Bearer token (CS Staff session) |
| **Content-Type** | `application/json` |

##### Request Headers

| Header | Value | Required |
|---|---|---|
| `Content-Type` | `application/json` | Yes |
| `Authorization` | `Bearer <token>` | Yes |

##### Request Body Parameters

**Personal Information**

| Field | Type | Required | Constraints | Description |
|---|---|---|---|---|
| `full_name` | string | Yes | 1–150 chars | Customer's full name |
| `date_of_birth` | string | Yes | Format: `YYYY-MM-DD` | Customer's date of birth |
| `id_card_number` | string | Yes | Exactly 13 digits, numeric only, unique | National ID card number |
| `phone_number` | string | Yes | 9–10 digits, numeric only | Customer's contact phone number |
| `email_address` | string | Yes | Valid email format, max 255 chars | Customer's email address |

**Registered Address (`registered_address`)**

| Field | Type | Required | Constraints | Description |
|---|---|---|---|---|
| `registered_address.house_street` | string | Yes | 1–255 chars | House number and street |
| `registered_address.sub_district` | string | Yes | 1–100 chars | Sub-district name (ตำบล / แขวง) |
| `registered_address.district` | string | Yes | 1–100 chars | District name (อำเภอ / เขต) |
| `registered_address.province` | string | Yes | 1–100 chars | Province name (จังหวัด) |
| `registered_address.postal_code` | string | Yes | Exactly 5 digits, numeric only | Postal code |

**Document Delivery Address (`delivery_address`)**

| Field | Type | Required | Constraints | Description |
|---|---|---|---|---|
| `delivery_address.same_as_registered` | boolean | Yes | `true` or `false` | If `true`, delivery address mirrors registered address; address fields below are ignored |
| `delivery_address.house_street` | string | Conditional | Required if `same_as_registered` is `false`; 1–255 chars | House number and street |
| `delivery_address.sub_district` | string | Conditional | Required if `same_as_registered` is `false`; 1–100 chars | Sub-district name (ตำบล / แขวง) |
| `delivery_address.district` | string | Conditional | Required if `same_as_registered` is `false`; 1–100 chars | District name (อำเภอ / เขต) |
| `delivery_address.province` | string | Conditional | Required if `same_as_registered` is `false`; 1–100 chars | Province name (จังหวัด) |
| `delivery_address.postal_code` | string | Conditional | Required if `same_as_registered` is `false`; exactly 5 digits | Postal code |

##### Response

**Success — `201 Created`**

| Field | Type | Description |
|---|---|---|
| `status` | string | `"success"` |
| `customer_id` | string | System-generated unique Customer ID (pattern: `CUS-XXXXXX`) |
| `message` | string | Human-readable confirmation message |

**Error — `400 Bad Request`**

| Field | Type | Description |
|---|---|---|
| `status` | string | `"error"` |
| `code` | string | Machine-readable error code |
| `message` | string | Human-readable summary of the error |
| `errors` | array | List of field-level error objects |
| `errors[].field` | string | Dot-notation field path that failed |
| `errors[].message` | string | Description of why the field failed |

**Error — `409 Conflict`**

| Field | Type | Description |
|---|---|---|
| `status` | string | `"error"` |
| `code` | string | `"DUPLICATE_CUSTOMER"` |
| `message` | string | Human-readable conflict description |

**Error — `401 Unauthorized`** — Returned when the request is missing a valid Bearer token.

**Error Codes**

| Code | HTTP Status | Description |
|---|---|---|
| `VALIDATION_ERROR` | 400 | One or more fields are missing or contain invalid values |
| `DUPLICATE_CUSTOMER` | 409 | A customer with the same `id_card_number` already exists |
| `UNAUTHORIZED` | 401 | Missing or invalid authentication token |

##### Sample Payloads

**Sample Request — Delivery address differs from registered address**

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

**Sample Request — Delivery address same as registered address**

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

**Sample Response — `201 Created`**

```json
{
  "status": "success",
  "customer_id": "CUS-000001",
  "message": "Customer registered successfully."
}
```

**Sample Response — `400 Bad Request`**

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

**Sample Response — `409 Conflict`**

```json
{
  "status": "error",
  "code": "DUPLICATE_CUSTOMER",
  "message": "A customer with ID card number '1234567890123' already exists."
}
```

---

#### US-02 — Enroll Customer into Service Package

| Item | Detail |
|---|---|
| **Endpoint** | `POST /subscriptions` |
| **Purpose** | Enroll an existing customer into a service package for a defined subscription period |
| **Auth Required** | Yes — Bearer token (CS Staff session) |
| **Content-Type** | `application/json` |

##### Request Headers

| Header | Value | Required |
|---|---|---|
| `Content-Type` | `application/json` | Yes |
| `Authorization` | `Bearer <token>` | Yes |

##### Request Body Parameters

| Field | Type | Required | Constraints | Description |
|---|---|---|---|---|
| `customer_id` | string | Yes | Must exist in the CUSTOMER table | Customer ID of the customer being enrolled (pattern: `CUS-XXXXXX`) |
| `package_id` | integer | Yes | Must exist in SERVICE_PACKAGE and `is_active = true` | ID of the service package to enroll the customer into |
| `start_date` | string | Yes | Format: `YYYY-MM-DD`; must be a valid calendar date | Subscription start date |
| `end_date` | string | Yes | Format: `YYYY-MM-DD`; must be strictly after `start_date` | Subscription end date |

##### Response

**Success — `201 Created`**

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

**Error — `400 Bad Request`**

| Field | Type | Description |
|---|---|---|
| `status` | string | `"error"` |
| `code` | string | Machine-readable error code |
| `message` | string | Human-readable summary of the error |
| `errors` | array | List of field-level error objects |
| `errors[].field` | string | Field name that caused the error |
| `errors[].message` | string | Description of why the field failed |

**Error — `401 Unauthorized`** — Returned when the request is missing a valid Bearer token.

**Error Codes**

| Code | HTTP Status | Description |
|---|---|---|
| `VALIDATION_ERROR` | 400 | One or more fields are missing or contain invalid values |
| `CUSTOMER_NOT_FOUND` | 400 | The supplied `customer_id` does not exist in the system |
| `PACKAGE_NOT_FOUND` | 400 | The supplied `package_id` does not exist or is not active |
| `INVALID_DATE_RANGE` | 400 | `end_date` is on or before `start_date` |
| `UNAUTHORIZED` | 401 | Missing or invalid authentication token |

##### Sample Payloads

**Sample Request**

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

**Sample Response — `201 Created`**

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

**Sample Response — `400 Bad Request` (missing field)**

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

**Sample Response — `400 Bad Request` (invalid date range)**

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

**Sample Response — `400 Bad Request` (customer not found)**

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

##### Validation Rules Summary

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

> For a full view of how all artifacts, user stories, acceptance criteria, and stakeholder requirements connect across the entire system, refer to the traceability matrix at [traceability-matrix.md](traceability-matrix.md).
