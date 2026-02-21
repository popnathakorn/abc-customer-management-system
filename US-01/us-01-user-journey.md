# US-01 User Journey — Register New Customer

**User Story:**
> As a **Customer Service Staff**, I want to register a new customer's personal information, registered address, and document delivery address into the system, so that the company has complete and accurate customer records for service and communication purposes.

---

## User Journey Diagram

```mermaid
journey
    title US-01: Register New Customer (Customer Service Staff)

    section Authentication
      Log in to system: 5: CS Staff
      Navigate to Customer Management: 4: CS Staff

    section Initiate Registration
      Click "Add New Customer": 5: CS Staff
      System displays registration form: 4: CS Staff, System

    section Enter Personal Information
      Fill in full name: 4: CS Staff
      Fill in date of birth: 4: CS Staff
      Fill in ID card number: 4: CS Staff
      Fill in phone number: 4: CS Staff
      Fill in email address: 4: CS Staff

    section Enter Registered Address
      Fill in house number / street: 3: CS Staff
      Select province and district (อำเภอ/เขต): 3: CS Staff
      Select sub-district (ตำบล/แขวง): 3: CS Staff
      Fill in postal code: 3: CS Staff

    section Enter Document Delivery Address
      Confirm same as registered address OR enter new address: 3: CS Staff
      Fill in delivery address details: 3: CS Staff

    section Validation & Submission
      System validates all required fields: 4: System
      System flags missing or invalid fields: 2: CS Staff, System
      CS Staff corrects errors: 2: CS Staff
      Re-validate form: 4: CS Staff, System
      Click "Submit / Save": 5: CS Staff

    section Confirmation
      System saves customer record: 5: System
      System displays success message with Customer ID: 5: CS Staff, System
      CS Staff records or shares Customer ID with customer: 4: CS Staff
```

---

## Journey Summary

| Step | Actor | Action | Satisfaction |
|---|---|---|---|
| 1 | CS Staff | Log in and navigate to Customer Management | High |
| 2 | CS Staff | Initiate new customer registration | High |
| 3 | CS Staff | Enter personal information | Medium–High |
| 4 | CS Staff | Enter registered address | Medium |
| 5 | CS Staff | Enter document delivery address | Medium |
| 6 | CS Staff + System | Validate form and correct errors | Low–High |
| 7 | System | Save record and return Customer ID | High |

---

## Notes

- **Address hierarchy**: Each address captures four levels — house number/street, sub-district (ตำบล/แขวง), district (อำเภอ/เขต), and province — plus postal code. Sub-district and district should be cascading dropdowns driven by the selected province to reduce input errors.
- **Same-address toggle**: If the document delivery address is the same as the registered address, a checkbox should pre-fill all five address fields to reduce data entry effort.
- **Validation**: Required fields should be highlighted in real-time before submission to reduce re-submission friction.
- **Customer ID**: Upon successful registration, the system should display a unique Customer ID for reference.
