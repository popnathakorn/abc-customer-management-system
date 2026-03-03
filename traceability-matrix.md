# Traceability Matrix
## Stakeholder Requirement → User Story → User Journey

**Project:** ABC Mobile Network Customer System
**Source:** `stakeholder-requirements.md`
**Target:** `user-stories.md`, `user-stories/US-01/`, `user-stories/US-02/`, `user-stories/US-03/`, `user-stories/US-04/`, `user-stories/US-08/`, `user-stories/US-09/`

---

## Matrix

| SR ID | Stakeholder Requirement | User Story ID | User Story Summary | User Journey ID | User Journey File |
|---|---|---|---|---|---|
| SR-1 | Design a database to store customer data for ABC mobile network subscribers | US-07 | System Administrator requires a well-structured database for consistent and queryable data | — | — |
| SR-1.1 | Store customer personal information, registered address, and document delivery address | US-01 | Customer Service Staff registers customer personal info and addresses | UJ-01 | `user-stories/US-01/us-01-user-journey.md` |
| SR-1.1 | Store customer personal information, registered address, and document delivery address | US-06 | Subscriber's personal details and addresses are accurately recorded | — | — |
| SR-1.2 | Store customer service subscription data (package, details, subscription date, end date) | US-02 | Customer Service Staff enrolls customer into a service package with dates | UJ-02 | `user-stories/US-02/us-02-user-journey.md` |
| SR-1.2 | Store customer service subscription data (package, details, subscription date, end date) | US-06 | Subscriber's chosen service package is accurately recorded | — | — |
| SR-2.1 | New customer report per day | US-03 | Operations Staff views daily report of newly registered customers | UJ-03 | `user-stories/US-03/us-03-user-journey.md` |
| SR-2.1 | New customer report per day | US-08 | Database Administrator runs SQL query to retrieve daily new customer registration data | UJ-08 | `user-stories/US-08/us-08-user-journey.md` |
| SR-2.2 | Service subscription history report per customer per day | US-04 | Operations Staff views daily subscription history report per customer | UJ-04 | `user-stories/US-04/us-04-user-journey.md` |
| SR-2.2 | Service subscription history report per customer per day | US-09 | Database Administrator runs SQL query to retrieve daily subscription history per customer | UJ-09 | `user-stories/US-09/us-09-user-journey.md` |
| SR-3 | Design screen, RESTful API, and API parameters for storing customer subscription data | US-01 | Customer Service Staff registers customer via UI screen | UJ-01 | `user-stories/US-01/us-01-user-journey.md` |
| SR-3 | Design screen, RESTful API, and API parameters for storing customer subscription data | US-02 | Customer Service Staff enrolls customer into a service package via UI screen | UJ-02 | `user-stories/US-02/us-02-user-journey.md` |
| SR-3 | Design screen, RESTful API, and API parameters for storing customer subscription data | US-05 | System Administrator consumes RESTful API to submit and store subscription data | — | — |

---

## Coverage Summary

### Stakeholder Requirement Coverage
| SR ID | Stakeholder Requirement | Covered By (US) | Covered By (UJ) |
|---|---|---|---|
| SR-1 | Database design for customer data | US-07 | — |
| SR-1.1 | Customer personal info & addresses storage | US-01, US-06 | UJ-01 |
| SR-1.2 | Service subscription data storage | US-02, US-06 | UJ-02 |
| SR-2.1 | Daily new customer report | US-03, US-08 | UJ-03, UJ-08 |
| SR-2.2 | Daily subscription history report per customer | US-04, US-09 | UJ-04, UJ-09 |
| SR-3 | Screen, RESTful API & API parameters | US-01, US-02, US-05 | UJ-01, UJ-02 |

### User Story Coverage
| User Story ID | Summary | Traces To (SR) | User Journey |
|---|---|---|---|
| US-01 | Register customer personal info and addresses | SR-1.1, SR-3 | UJ-01 |
| US-02 | Enroll customer into service package | SR-1.2, SR-3 | UJ-02 |
| US-03 | View daily new customer report | SR-2.1 | UJ-03 |
| US-04 | View daily subscription history report | SR-2.2 | UJ-04 |
| US-05 | Consume RESTful API to store subscription data | SR-3 | — |
| US-06 | Subscriber's details and package accurately recorded | SR-1.1, SR-1.2 | — |
| US-07 | Well-structured database for consistent and queryable data | SR-1 | — |
| US-08 | Run SQL query for daily new customer registration report | SR-2.1 | UJ-08 |
| US-09 | Run SQL query for daily subscription history report per customer | SR-2.2 | UJ-09 |

### User Journey Coverage
| User Journey ID | File | Mapped User Story | Stakeholder Requirement |
|---|---|---|---|
| UJ-01 | `user-stories/US-01/us-01-user-journey.md` | US-01 | SR-1.1, SR-3 |
| UJ-02 | `user-stories/US-02/us-02-user-journey.md` | US-02 | SR-1.2, SR-3 |
| UJ-03 | `user-stories/US-03/us-03-user-journey.md` | US-03 | SR-2.1 |
| UJ-04 | `user-stories/US-04/us-04-user-journey.md` | US-04 | SR-2.2 |
| UJ-08 | `user-stories/US-08/us-08-user-journey.md` | US-08 | SR-2.1 |
| UJ-09 | `user-stories/US-09/us-09-user-journey.md` | US-09 | SR-2.2 |

### US-01 Artifacts
| Artifact | File |
|---|---|
| User Journey | `user-stories/US-01/us-01-user-journey.md` |
| Acceptance Criteria & Scope of Work | `user-stories/US-01/us-01-acceptance-criteria-and-scope.md` |
| API Specification | `user-stories/US-01/us-01-api-spec.md` |
| Wireframes | `user-stories/US-01/us-01-wireframes.md` |

### US-02 Artifacts
| Artifact | File |
|---|---|
| User Journey | `user-stories/US-02/us-02-user-journey.md` |
| Acceptance Criteria & Scope of Work | `user-stories/US-02/us-02-acceptance-criteria-and-scope.md` |
| API Specification | `user-stories/US-02/us-02-api-spec.md` |
| Wireframes | `user-stories/US-02/us-02-wireframes.md` |

### US-03 Artifacts
| Artifact | File |
|---|---|
| User Journey | `user-stories/US-03/us-03-user-journey.md` |
| Acceptance Criteria & Scope of Work | `user-stories/US-03/us-03-acceptance-criteria-and-scope.md` |
| API Specification | `user-stories/US-03/us-03-api-spec.md` |
| Wireframes | `user-stories/US-03/us-03-wireframes.md` |

### US-04 Artifacts
| Artifact | File |
|---|---|
| User Journey | `user-stories/US-04/us-04-user-journey.md` |
| Acceptance Criteria & Scope of Work | `user-stories/US-04/us-04-acceptance-criteria-and-scope.md` |
| API Specification | `user-stories/US-04/us-04-api-spec.md` |
| Wireframes | `user-stories/US-04/us-04-wireframes.md` |

### US-08 Artifacts
| Artifact | File |
|---|---|
| User Journey | `user-stories/US-08/us-08-user-journey.md` |
| Acceptance Criteria & Scope of Work | `user-stories/US-08/us-08-acceptance-criteria-and-scope.md` |
| SQL Script | `user-stories/US-08/us-08-sql-script.md` |

### US-09 Artifacts
| Artifact | File |
|---|---|
| User Journey | `user-stories/US-09/us-09-user-journey.md` |
| Acceptance Criteria & Scope of Work | `user-stories/US-09/us-09-acceptance-criteria-and-scope.md` |
| SQL Script | `user-stories/US-09/us-09-sql-script.md` |

---

## Coverage Status

| Metric | Count |
|---|---|
| Total Stakeholder Requirements | 6 |
| Stakeholder Requirements fully covered | 6 |
| Total User Stories | 9 |
| User Stories with traceability | 9 |
| Total User Journeys | 6 |
| User Stories with User Journey | 6 (US-01, US-02, US-03, US-04, US-08, US-09) |
| **Overall Coverage** | **100% (SR → US) / 67% (US → UJ)** |
