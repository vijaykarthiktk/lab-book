# SOFTWARE REQUIREMENTS SPECIFICATION (SRS)
## LabBook: Computer Lab Slot Booking & Quota Management System
**Document Standard:** IEEE Std 830-1998  
**Project Case Study:** No. 150  
**Target Environment:** Engineering College (14 Labs, 6 Departments)

---

## 1. INTRODUCTION

### 1.1 Purpose
This Software Requirements Specification (SRS) establishes the formal functional, behavioral, and non-functional requirements for the **LabBook** computer lab slot reservation and quota management platform. It serves as the definitive engineering baseline for developers, quality assurance engineers, institutional administrators, and departmental coordinators.

### 1.2 Scope of the Software
LabBook is an enterprise-grade, web-based resource reservation and utilization auditing system designed to automate the allocation of **14 physical computer laboratories** across **6 academic engineering departments**. The software:
- Ingests recurring semester curricular timetable schedules (consuming 62% baseline capacity).
- Enforces weekly departmental quota caps for non-curricular bookings.
- Empowers authenticated students to discover and self-reserve unallocated ("free") lab slots.
- Prevents concurrent scheduling clashes via database-level transaction locks.
- Provides a real-time check-in console for laboratory assistants to eliminate "ghost bookings".
- Reclaims unattended reservations after a 15-minute grace period.
- Aggregates actionable business intelligence on laboratory seat-fill ratios and quota consumption.

### 1.3 Definitions, Acronyms, and Abbreviations
- **ACID:** Atomicity, Consistency, Isolation, Durability
- **BVA:** Boundary Value Analysis
- **CPM:** Critical Path Method
- **CSV:** Comma-Separated Values
- **DRE:** Defect Removal Efficiency
- **EP:** Equivalence Partitioning
- **FR:** Functional Requirement
- **IEEE:** Institute of Electrical and Electronics Engineers
- **KLOC:** Thousands of Lines of Code
- **LDAP:** Lightweight Directory Access Protocol
- **NFR:** Non-Functional Requirement
- **RBAC:** Role-Based Access Control
- **RMMM:** Risk Mitigation, Monitoring, and Management
- **SRS:** Software Requirements Specification
- **SUS:** System Usability Scale
- **UAT:** User Acceptance Testing
- **WBS:** Work Breakdown Structure

### 1.4 Elicitation Methodologies
Requirements were gathered across five institutional stakeholder tiers using:
1. **Semi-Structured Interviews:** Held with 6 Department Heads and the Dean of Academic Affairs.
2. **Contextual Inquiry & Physical Register Auditing:** Observed lab assistants managing physical paper logbooks across 4 campus buildings.
3. **Survey Questionnaires:** Distributed to 180 undergraduate and postgraduate students.
4. **Spreadsheet Workflow Analysis:** Audited master CSV/XLSX schedules from the Timetable Office.

---

## 2. GENERAL DESCRIPTION

### 2.1 Product Perspective
LabBook operates within the college campus intranet and interfaces with the institutional LDAP server for single sign-on (SSO). It replaces fragmented paper registers and spreadsheet-based booking requests with a synchronized, ACID-compliant database.

```
[Campus LDAP / Active Directory]
              ^
              | (LDAP Auth / Bind)
              v
     [LabBook Web Application] <---- (HTTPS) ---- [Students, Assistants, HoDs, Admin]
              |
              v
[PostgreSQL 15 Relational DB & Redis Cache]
```

### 2.2 User Characteristics & Personas
- **Student:** Technical competence: Moderate to High. Needs rapid, mobile-responsive slot discovery and one-click reservation without administrative bureaucracy.
- **Lab Assistant:** Technical competence: Basic to Moderate. Operates at a physical desk in the lab; requires an unambiguous check-in interface with minimal typing (e.g., barcode/QR scanning).
- **Department Coordinator:** Technical competence: Moderate. Requires real-time visibility into departmental quota consumption and authority to request quota balance adjustments.
- **Timetable Officer:** Technical competence: Moderate. Imports master semester schedules at the start of each term and reconciles classroom relocations.
- **System Administrator:** Technical competence: High. Configures system parameters, manages role permissions, audits database locks, and monitors background daemons.

### 2.3 General Constraints
1. **Physical Capacity:** Bounded to exactly 14 physical laboratories.
2. **Departmental Inclusivity:** Strictly apportioned among 6 academic departments.
3. **Temporal Boundary:** Exactly 8 slots per day across a 6-day academic operating week (672 weekly slots gross).
4. **Curricular Priority:** Curricular classes hold absolute priority, consuming exactly 62% of aggregate capacity (416.64 slots).
5. **Authentication Constraint:** All user identities must resolve through the campus LDAP infrastructure.

---

## 3. SPECIFIC REQUIREMENTS

### 3.1 Functional Requirements (11 FRs)

#### FR-01: User Authentication & Role-Based Access Control (RBAC)
- **Stakeholder:** System Administrator, All Users
- **Priority:** Must Have (P1)
- **Description:** The system shall authenticate all users via institutional LDAP/Active Directory protocols and enforce role-based access rules for Students, Lab Assistants, Coordinators, Timetable Officers, and Administrators.
- **Acceptance Criterion:** System permits access only upon receipt of valid LDAP credentials, correctly redirects users to their role-specific dashboard within 1.5 seconds, and blocks unauthorized route traversal with an HTTP 403 Forbidden status.
- **Related Test Case:** TC-01

#### FR-02: Timetable Class Schedule Ingestion
- **Stakeholder:** Timetable Officer, System Administrator
- **Priority:** Must Have (P1)
- **Description:** The system shall provide an administrative interface allowing the Timetable Officer to upload standardized CSV/Excel semester timetable schedules containing Lab ID, Day, Slot Number, Department, Course Code, and Faculty In-Charge, automatically populating recurring non-bookable slots.
- **Acceptance Criterion:** The parser successfully ingests 100% of valid timetable entries, maps them to exactly 62% of aggregate capacity, reports malformed records with row-level error logs, and completes ingestion in under 10 seconds for a full semester schedule.
- **Related Test Case:** TC-02

#### FR-03: Real-Time Laboratory Slot Availability Query
- **Stakeholder:** Student, Department Coordinator, Lab Assistant
- **Priority:** Must Have (P1)
- **Description:** The system shall dynamically render a 14-lab matrix display across the 8 daily slots for any selected date within a rolling 7-day window, color-coding each slot as: *Curricular (Timetabled)*, *Booked (Student)*, *Available (Free)*, or *Maintenance/Hold*.
- **Acceptance Criterion:** Querying any calendar date within the permitted range returns the exact, synchronized slot state across all 14 laboratories within 1.0 second under peak query concurrency.
- **Related Test Case:** TC-03

#### FR-04: Departmental Quota Allocation & Enforcement
- **Stakeholder:** Department Coordinator, System Administrator
- **Priority:** Must Have (P1)
- **Description:** The system shall enforce weekly departmental slot allowances based on agreed institutional policy across the 6 departments, continuously decrementing available quota upon confirmed student reservations and blocking booking attempts once a department's weekly cap is exhausted.
- **Acceptance Criterion:** The booking engine evaluates the student’s department quota balance in real time; if $Balance \le 0$, the reservation transaction is aborted with a descriptive error message without corrupting database state.
- **Related Test Case:** TC-04

#### FR-05: Student Free-Slot Self-Booking Engine
- **Stakeholder:** Student
- **Priority:** Must Have (P1)
- **Description:** The system shall allow an authenticated student possessing available departmental quota to select an *Available* lab slot, declare an academic purpose (e.g., Project Work, Coding Practice, Exam Prep), and confirm the reservation.
- **Acceptance Criterion:** Upon user submission, the system validates eligibility, writes the reservation record atomically, updates slot status from *Available* to *Confirmed*, and generates a unique digital booking reference ID within 1.5 seconds.
- **Related Test Case:** TC-05

#### FR-06: Automated Booking Clash Detection & Prevention
- **Stakeholder:** Student, Lab Assistant, System Administrator
- **Priority:** Must Have (P1)
- **Description:** The system shall employ atomic database transactions with pessimistic row-level locking (`SELECT ... FOR UPDATE`) to evaluate overlapping bookings, preventing double-booking of any physical laboratory slot under simultaneous concurrent requests.
- **Acceptance Criterion:** Under a simulated race condition of 50 concurrent requests for a single free lab slot, exactly one reservation succeeds while 49 requests are safely rejected with an HTTP 409 Conflict code.
- **Related Test Case:** TC-06

#### FR-07: Self-Service Booking Cancellation
- **Stakeholder:** Student, Lab Assistant
- **Priority:** Should Have (P2)
- **Description:** The system shall allow students to cancel their confirmed bookings up to 30 minutes prior to the scheduled slot start time, immediately restoring the slot to the *Available* pool and refunding 1 slot unit back to the student's departmental quota.
- **Acceptance Criterion:** Cancellation requested $\ge 30$ minutes prior to start time updates booking state to *Cancelled*, increments department quota balance by +1, and re-renders the slot as *Available* in real time; requests $< 30$ minutes are blocked.
- **Related Test Case:** TC-07

#### FR-08: Lab Assistant Real-Time Check-In Verification
- **Stakeholder:** Lab Assistant, Student
- **Priority:** Must Have (P1)
- **Description:** The system shall provide a streamlined, web-based verification console for Lab Assistants to validate arriving students by scanning their digital Booking QR code or entering their Student Roll Number, marking the reservation status as *Checked-In*.
- **Acceptance Criterion:** Scanning a valid booking token within the allowable arrival window changes the reservation state to *Checked-In* and logs the timestamp and Lab Assistant ID within 500 milliseconds.
- **Related Test Case:** TC-08

#### FR-09: Automated No-Show Sweep & Slot Reclamation
- **Stakeholder:** Lab Assistant, System Administrator
- **Priority:** Should Have (P2)
- **Description:** The system shall run an automated background cron daemon every 5 minutes that identifies bookings remaining in *Confirmed* status 15 minutes past the slot start time, flags them as *No-Show*, applies a penalty strike to the student record, and transitions the slot to *Available* for on-demand walk-in booking.
- **Acceptance Criterion:** Confirmed bookings with no recorded check-in at $T_{start} + 15\text{ minutes}$ are transitioned to *No-Show* within 60 seconds of threshold breach, emitting an audit log entry.
- **Related Test Case:** TC-09

#### FR-10: Automated Notification & Digital Receipt Generation
- **Stakeholder:** Student, Lab Assistant
- **Priority:** Could Have (P3)
- **Description:** The system shall dispatch an automated email and downloadable PDF confirmation slip containing a digitally signed QR code upon successful booking, cancellation, or no-show penalty application.
- **Acceptance Criterion:** PDF receipt and transactional email are dispatched via asynchronous queue within 5 seconds of the booking event with 99.5% delivery success.
- **Related Test Case:** TC-10

#### FR-11: Laboratory & Quota Utilization Analytics Engine
- **Stakeholder:** Department Coordinator, System Administrator, College Executive
- **Priority:** Should Have (P2)
- **Description:** The system shall compile and display historical and real-time analytical reports aggregating lab utilization percentage, departmental quota consumption, peak usage hours, and no-show distributions, with export capabilities to CSV and PDF formats.
- **Acceptance Criterion:** Analytics dashboard renders tabular and graphical summaries for any user-selected date range up to 1 academic semester within 2.5 seconds.
- **Related Test Case:** TC-11

---

### 3.2 Non-Functional Requirements (6 Measurable NFRs)

```
+=======================================================================================================================+
| NFR ID | Category       | Requirement Statement             | Target Metric           | Verification Method           |
+=======================================================================================================================+
| NFR-01 | Availability   | Platform uptime during operating  | >= 99.5% Uptime         | Automated ping monitoring     |
|        |                | hours (08:00 - 18:00 Mon-Sat)     | (Unplanned outage       | (Uptime Kuma / Prometheus)    |
|        |                | excluding scheduled maintenance.  | <= 14.4 min/month)      | over 30 continuous days       |
+-----------------------------------------------------------------------------------------------------------------------+
| NFR-02 | Correctness &  | Transactional consistency in slot | 100% Data Integrity     | Multi-threaded concurrency    |
|        | Integrity      | booking; zero double-booking rate.| (0 double-bookings per  | stress test (JMeter 50-thread |
|        |                |                                   | 100,000 transactions)   | race-condition suite)         |
+-----------------------------------------------------------------------------------------------------------------------+
| NFR-03 | Usability      | Intuitive interface allowing quick| <= 45 seconds completion| Empirical usability testing   |
|        |                | booking completion without train- | time for first-time use;| with 20 sample students;      |
|        |                | ing across mobile and desktop.    | SUS Score >= 80         | System Usability Scale survey |
+-----------------------------------------------------------------------------------------------------------------------+
| NFR-04 | Performance &  | Real-time availability query and  | 95th percentile HTTP    | k6 / Locust load testing at   |
|        | Scalability    | response time under peak campus   | response time <= 1.0s   | 200 concurrent active HTTP    |
|        |                | operational traffic.              | at 200 concurrent users | sessions                      |
+-----------------------------------------------------------------------------------------------------------------------+
| NFR-05 | Security &     | Protection against unauthorized   | Zero critical/high vulns| Automated static & dynamic    |
|        | Protection     | access, SQL injection, and token  | on OWASP Top-10 audit;  | security scan (SonarQube and  |
|        |                | manipulation across endpoints.    | RBAC 100% enforced      | OWASP ZAP penetration suites) |
+-----------------------------------------------------------------------------------------------------------------------+
| NFR-06 | Maintainability| System recovery speed after con-  | MTTR <= 30 minutes;     | Simulated container kill chaos|
|        | & Reliability  | tainer crash; high test coverage; | Test coverage >= 85%;   | drill; JaCoCo unit test code  |
|        |                | low cyclomatic complexity.        | Cyclomatic Complexity<=10| coverage audit in CI pipeline|
+=======================================================================================================================+
```

---

## 4. MOSCOW PRIORITISATION FRAMEWORK

- **Must Have (M):** FR-01, FR-02, FR-03, FR-04, FR-05, FR-06, FR-08, NFR-01, NFR-02, NFR-04, NFR-05  
  *Rationale:* Mandatory core requirements. Without authentication, timetable import, availability visualization, quota checks, clash prevention, and assistant check-in, the system cannot operate.
- **Should Have (S):** FR-07, FR-09, FR-11, NFR-03, NFR-06  
  *Rationale:* Highly important operational enhancements (cancellation, automated no-show reclamation, and analytics) that ensure facility recycling and transparency.
- **Could Have (C):** FR-10  
  *Rationale:* Transactional emails and PDF generation improve usability, but on-screen booking confirmation codes provide an effective fallback.
- **Won't Have (W):** Automatic master timetable generation, hardware-level Wake-on-LAN power control, and biometric physical turnstiles (explicitly deferred to Version 2.0).
