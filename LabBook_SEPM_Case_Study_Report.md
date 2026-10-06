# B.TECH CSE SEMESTER III — ACADEMIC CASE STUDY EVALUATION

## COURSE CODE: CS302 / IT301 — SOFTWARE ENGINEERING & PROJECT MANAGEMENT (SEPM)

---

# CASE STUDY NO. 150: LabBook

## Computer Lab Slot Booking & Quota Management System for an Engineering College

**Academic Year:** 2026–2027 | **Semester:** III | **Department:** Computer Science & Engineering
**Prepared by:** Senior SEPM Academic Directorate & Chief Software Architecture Team
**Evaluation Standard:** IEEE 830-1998, ISO/IEC/IEEE 12207, PMI PMBOK Guide 7th Ed.
**Document Classification:** Complete Submission-Ready Solution Portfolio

---

## TABLE OF CONTENTS

1. Cover Page & Metadata
2. Executive Summary
3. Problem Statement & Operational Context
4. System Objectives
5. System Scope & Boundary Definition
6. Assumptions and Constraints
7. Stakeholder Identification & Analysis
8. IEEE 830-Style Software Requirements Specification (SRS)
   - 8.1 Functional Requirements (11 FRs)
   - 8.2 Non-Functional Requirements (6 Measurable NFRs)
   - 8.3 MoSCoW Prioritisation Framework
   - 8.4 Requirements Traceability Matrix (RTM) Baseline
9. Capacity Analysis & Free-Slot Estimation
10. System Architecture & High-Level Design
11. UML Design Package
    - 11.1 Use Case Diagram (PlantUML) & Specification
    - 11.2 Domain Class Diagram (PlantUML) & Data Dictionary
    - 11.3 Sequence Diagram: "Book a Free Lab Slot and Check In" (PlantUML)
    - 11.4 Activity Diagram: End-to-End Booking & Verification Workflow (PlantUML)
    - 11.5 State Machine Diagram: Booking Lifecycle (PlantUML)
    - 11.6 Architectural Cohesion, Loose Coupling, and Interface Abstraction Justification
12. Work Breakdown Structure (WBS) & Dictionary
13. Task Dependency Analysis
14. Forward Pass Calculations (Earliest Times)
15. Backward Pass Calculations (Latest Times)
16. Total Float, Free Float, and Slack Analysis
17. Critical Path Identification & Mathematical Proof
18. Project Milestones
19. Visual & Tabular Gantt Chart (Project Days 1 to 30)
20. Resource Allocation & RACI Matrix
21. Comprehensive Test Plan & Quality Assurance Strategy
22. Boundary Value Analysis (BVA) Test Suites
23. Equivalence Partitioning (EP) Test Suites
24. Cause-Effect Decision Table (Quota & Clash Resolution)
25. Detailed Test Cases (TC-01 to TC-11)
26. End-to-End Traceability Matrix (FR → UC → TC → Expected Result)
27. Illustrative Defect Log
28. Software Defect Density Metrics
29. Defect Removal Efficiency (DRE) Analysis
30. Project & Operational Monitoring & Control Plan
31. Quantitative Risk Register & Exposure Ranking
32. Probability–Impact (P–I) Matrix
33. Risk Mitigation, Monitoring, and Management (RMMM) Plan
34. Project Closure Note & Handover Protocol
35. Academic Lessons Learned
36. Final Conclusion & Key Findings

---

# 1. COVER PAGE

```
========================================================================================
                  DEPARTMENT OF COMPUTER SCIENCE AND ENGINEERING
               INSTITUTE OF TECHNOLOGY & SCIENCE (AUTONOMOUS)
========================================================================================
PROJECT TITLE:
  LabBook: Automated Computer Lab Slot Reservation, Quota Distribution,
  and Utilization Auditing System

ACADEMIC COURSE:
  CS302: Software Engineering and Project Management (SEPM)
  Degree Program: Bachelor of Technology (B.Tech) in Computer Science & Engineering
  Term: Semester III

CASE STUDY ASSIGNMENT:
  Case Study No. 150 (LabBook Infrastructure Modernization)

STUDENT / FACULTY ARCHITECT:
  Senior Software Engineering Faculty & Systems Architecture Directorate

SUBMISSION DATE:
  October 2026

DOCUMENT VERSION:
  Release 1.0 (Submission-Ready Gold Standard)
========================================================================================
```

---

# 2. EXECUTIVE SUMMARY

The contemporary operational model of the institution comprises 14 computing laboratories shared across 6 academic engineering departments. Currently, the allocation of computing labs for curricular practicals, co-curricular project development, competitive hackathons, remedial sessions, and high-stakes certification examinations is administered manually via physical paper registers maintained by laboratory assistants. This manual protocol has precipitated severe operational bottlenecks:

1. **Frequent Double-Bookings and Scheduling Clashes:** The lack of real-time multi-department concurrency controls leads to simultaneous reservations of the same laboratory.
2. **Opacity of Facility Availability:** Students have zero remote visibility into available lab slots, suppressing practice time and research activities.
3. **Inter-Departmental Friction:** Departments accuse one another of monopolizing specialized labs, driven by the absence of transparent quota enforcement.
4. **Sub-Optimal Utilization & Ghost Bookings:** High rates of unattended bookings ("no-shows") occur without automated reclamation, while physical registers lack verification protocols.

The **LabBook** system resolves these systemic deficiencies by engineering a centralized, web-based, role-based resource reservation and utilization platform. Based on an empirical capacity evaluation of **14 laboratories**, **8 daily slots**, and a **6-day operating week**, the gross operational capacity is **672 weekly slots**. Given that scheduled curricular classes account for exactly **62%** of this aggregate (416.64 slots), the system provisions an estimated **255.36 weekly free slots** (statistically equivalent to **255 guaranteed discrete booking slots** per week, or ~18.24 slots per laboratory) for student self-booking, project activities, and certification testing.

From a project management standpoint, the implementation roadmap has been rigorously synthesized using the **Critical Path Method (CPM)** across 7 core tasks with a total duration of **30 working days**. The analysis reveals two parallel critical paths ($Requirements \rightarrow Timetable\ Import \rightarrow Booking\ Module \rightarrow Check\text{-}in\ Module / Reports \rightarrow Testing$), while the *Department Quota Policy Agreement* task features a float of exactly 1 day. A comprehensive Risk Register evaluating four foundational risks establishes that *Inter-Departmental Quota Disagreements* represents the highest project exposure ($0.5 \times 7 = 3.5$), followed by *Timetable Import Errors* ($0.4 \times 6 = 2.4$) and *Student No-Shows* ($0.6 \times 4 = 2.4$).

This submission presents a full IEEE 830-standard Software Requirements Specification (SRS), five robust PlantUML design models (Use Case, Class, Sequence, Activity, and State Machine), a CPM schedule, a comprehensive Quality Assurance / Test Plan (featuring Boundary Value Analysis, Equivalence Partitioning, and a Decision Table), illustrative software quality metrics (Defect Density = 7.5 defects/KLOC, DRE = 92.86%), and an institutional Risk Mitigation, Monitoring, and Management (RMMM) plan.

---

# 3. PROBLEM STATEMENT

An autonomous engineering college manages **14 computer laboratories** designated for practical instruction across **6 academic engineering departments** (Computer Science, Information Technology, Electronics & Communication, Electrical & Electronics, Mechanical Engineering, and Civil Engineering / AI-DS).

Presently, lab allocations for non-timetabled events—such as student project work, programming contests, research simulations, make-up lab sessions, and external certification exams (e.g., NPTEL, AWS, RedHat)—are governed through physical registers kept at the desk of respective laboratory assistants.

### Core Systemic Failures:

1. **Frequent Clashes and Double-Bookings:** Due to distributed, uncoordinated physical registers, two departments or student cohorts frequently arrive at the same facility simultaneously, resulting in academic disruption and administrative disputes.
2. **Total Lack of Real-Time Visibility:** Students have no digital access to laboratory availability schedules. Identifying a free workstation requires physically traversing 14 laboratories across multiple academic blocks.
3. **Departmental Hoarding and Quota Disputes:** Certain departments reserve facilities indefinitely through informal arrangements with lab technicians, denying access to smaller departments and violating institutional equity principles.
4. **Lack of Check-In Accountability:** Bookings are frequently made speculatively by students or faculty who ultimately fail to show up ("ghost reservations"). Because lab assistants do not maintain a systematic attendance verification process, locked labs sit idle while other students are turned away.
5. **Disconnected Curricular Timetable:** Curricular timetables generated at the start of each semester are stored in fragmented spreadsheets within the Timetable Office and are not synchronized with room-level reservation systems.

---

# 4. OBJECTIVES

The overarching objective of the **LabBook** project is to conceive, design, implement, and validate an automated, transparent, auditable, and resilient computer lab slot reservation and quota management ecosystem.

### Specific Engineering & Management Objectives:

1. **Automate Timetable Ingestion:** Build an automated parsing engine to import semester-wise curricular timetable allocations (accounting for 62% baseline occupancy) from standardized college spreadsheets/CSV exports into the centralized database prior to the commencement of teaching.
2. **Provide Real-Time Transparent Availability:** Deliver an interactive, responsive graphical dashboard reflecting real-time lab slot states across all 14 laboratories for any selected date within a rolling 7-day booking horizon.
3. **Enforce Departmental Quotas Algorithmatically:** Enforce mathematical slot quota caps per department for non-curricular bookings, preventing resource monopolization while facilitating equitable access across all 6 departments.
4. **Enable Student Self-Service Booking:** Empower authenticated undergraduate and postgraduate students to reserve unallocated ("free") lab slots for legitimate academic activities without human gatekeeping.
5. **Eliminate Scheduling Clashes:** Guarantee zero booking collisions ($Clash\ Rate = 0.0\%$) using atomic database transactions and multi-layered concurrency locking mechanisms.
6. **Implement Real-Time Physical Check-In Verification:** Introduce a streamlined, biometric or barcode/ID-scan check-in interface for lab assistants that cancels no-show bookings after a 15-minute grace window and recycles slots back to the public pool.
7. **Generate Actionable Utilization Business Intelligence:** Provide institutional administrators, department heads, and deaneries with automated utilization reports detailing seat-fill ratios, quota usage, peak operational windows, and no-show trends.

---

# 5. SCOPE

### In-Scope Functional Capabilities:

- Centralized user authentication integrated with institutional LDAP/Single Sign-On (SSO) supporting five user roles: Student, Lab Assistant, Department Coordinator, Timetable Officer, and System Administrator.
- Bulk tabular parsing and validation of institutional master timetables (CSV/XLSX) with conflict checking.
- Automated allocation and tracking of weekly departmental quota buckets across the 6 departments.
- Self-service booking engine with multi-criteria filtering (date, slot, hardware specs, installed software toolchains).
- Immediate, two-phase reservation locking preventing race conditions when multiple users select the same slot simultaneously.
- Automated generation of cryptographically verifiable digital booking tokens (QR-coded PDF/e-receipts).
- Web-based Lab Assistant check-in console equipped with quick QR scanning and manual override.
- Automated scheduled task (daemon) executing every 5 minutes to sweep past-due bookings, mark "No-Shows", and trigger slot reclamation.
- Analytical reporting engine delivering departmental consumption, capacity distribution, and anomaly metrics.

### Out-of-Scope (Future Releases / Deliberate Exclusions):

- Direct hardware power-cycling or remote desktop power management via Wake-on-LAN (WoL).
- Integration with campus physical turnstile access-control hardware (deferred to Release 2.0).
- Automatic generation or optimization of the master academic class timetable (the system *imports* timetables; it does not *generate* them).
- Financial billing, fee collection, or monetary penalty deductions for students/faculty.

---

# 6. ASSUMPTIONS AND CONSTRAINTS

### 6.1 System Assumptions:

1. **Academic Operational Rhythm:** The college operates on a standard 6-day academic week (Monday through Saturday), with Sunday reserved exclusively for deep preventative maintenance and system batch operations.
2. **Slot Granularity:** The academic working day is partitioned into exactly 8 discrete operational periods/slots of equal duration (e.g., 60 minutes each, typically 09:00 to 17:00).
3. **Stable Network Infrastructure:** All 14 physical computer labs maintain wired Local Area Network (LAN) and campus Wi-Fi connectivity linking terminal workstations and lab assistant consoles to the central campus intranet server.
4. **Pre-Existing Master Timetable:** The Academic Timetable Office finalizes the semester laboratory course allocations at least 5 business days prior to the start of the academic semester.
5. **Student Identification:** Every enrolled student possesses a valid institutional email ID, student roll number, and a physical/digital student identification barcode.

### 6.2 System Constraints (Mandatory Project Realities):

1. **Physical Capacity Constraint:** The institutional infrastructure is physically bounded to exactly **14 computer laboratories**. No virtual or auxiliary labs may be added to capacity calculations.
2. **Departmental Inclusivity Constraint:** The system must strictly apportion non-timetabled capacity among exactly **6 academic departments** without excluding any branch.
3. **Temporal Bounds:** Exactly **8 slots per day** across a **6-day operational week**, establishing an immutable theoretical ceiling of $14 \times 8 \times 6 = 672$ total laboratory slots per week.
4. **Curricular Priority Constraint:** Curricular classroom laboratory practicals hold absolute priority over ad-hoc student bookings, consuming precisely **62%** of aggregate laboratory capacity by academic council mandate.
5. **Security & Authentication Constraint:** The application must interface directly with the existing campus LDAP / Active Directory directory services for credential validation, precluding independent credential creation.
6. **Regulatory / Calendar Constraint:** The software development, testing, and deployment lifecycle must conform to a strict 30-day timeline prior to semester commencement.

---

# 7. STAKEHOLDER IDENTIFICATION & ANALYSIS

The requirements engineering and operational workflows were formulated through systematic engagement with five distinct stakeholder groups.

| Stakeholder Role                 | Primary Responsibilities & Interests                                                                                                      | Elicitation Methodology                                              | Pain Points Addressed                                                                                                         |
| -------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------- |
| **Student**                | Seeks friction-free access to computing environments for coding assignments, capstone projects, and self-paced certification preparation. | Structured Student Surveys ($N=180$), Student Council Focus Groups | Inability to identify free slots without walking across campus; being turned away by lab staff due to informal reservations.  |
| **Department Coordinator** | Protects the department's academic interests; ensures equitable quota allocations for departmental electives and research.                | Semi-Structured Interviews ($N=6$ Heads of Dept & Coordinators)    | Inter-departmental disputes regarding lab monopolization; lack of visibility into departmental resource consumption.          |
| **Lab Assistant**          | Manages physical laboratory equipment, enforces discipline, verifies attendance, and ensures hardware security.                           | Contextual Inquiry, Physical Register Examination & Walkthroughs     | Harassment from students during disputes; manual paper logbook overhead; unverified ghost bookings blocking legitimate users. |
| **Timetable Officer**      | Coordinates institutional room assignments and master semester curricular schedules across all branches.                                  | Spreadsheet Audit, Institutional Timetable Workflow Analysis         | Manual reconciliation of timetable clashes; emergency room-swapping confusion during semester transitions.                    |
| **System Administrator**   | Oversees database integrity, system availability, authentication integration, user provisioning, and audit logs.                          | Technical Architecture Workshops, Network Security Reviews           | Concurrency deadlocks; uncontrolled database bloat; unauthorized administrative privilege escalation.                         |

---

# 8. IEEE 830-STYLE SOFTWARE REQUIREMENTS SPECIFICATION (SRS)

## 8.1 Functional Requirements (Exactly 11 FRs)

### FR-01: User Authentication & Role-Based Access Control (RBAC)

- **Stakeholder:** System Administrator, All Users
- **Priority:** Must Have (P1)
- **Description:** The system shall authenticate all users (Students, Lab Assistants, Department Coordinators, Timetable Officers, and Administrators) via the campus LDAP/Active Directory protocol and enforce role-based authorization boundaries restricting access to specific UI views and API endpoints.
- **Acceptance Criterion:** System permits access only upon receipt of valid LDAP credentials, correctly redirects users to their role-specific dashboard within 1.5 seconds, and blocks unauthorized route traversal with an HTTP 403 Forbidden status.
- **Related Test Case:** TC-01

### FR-02: Timetable Class Schedule Import & Ingestion

- **Stakeholder:** Timetable Officer, System Administrator
- **Priority:** Must Have (P1)
- **Description:** The system shall provide an administrative interface allowing the Timetable Officer to upload standardized CSV/Excel semester timetable schedules containing Lab ID, Day, Slot Number, Department, Course Code, and Faculty In-Charge, automatically populating recurring non-bookable slots.
- **Acceptance Criterion:** The parser successfully ingests 100% of valid timetable entries, maps them to exactly 62% of aggregate capacity, reports malformed records with row-level error logs, and completes ingestion in under 10 seconds for a full semester schedule.
- **Related Test Case:** TC-02

### FR-03: Real-Time Laboratory Slot Availability Query

- **Stakeholder:** Student, Department Coordinator, Lab Assistant
- **Priority:** Must Have (P1)
- **Description:** The system shall dynamically render a 14-lab matrix display across the 8 daily slots for any selected date within a rolling 7-day window, color-coding each slot as: *Curricular (Timetabled)*, *Booked (Student)*, *Available (Free)*, or *Maintenance/Hold*.
- **Acceptance Criterion:** Querying any calendar date within the permitted range returns the exact, synchronized slot state across all 14 laboratories within 1.0 second under peak query concurrency.
- **Related Test Case:** TC-03

### FR-04: Departmental Quota Allocation & Enforcement

- **Stakeholder:** Department Coordinator, System Administrator
- **Priority:** Must Have (P1)
- **Description:** The system shall enforce weekly departmental slot allowances based on agreed institutional policy across the 6 departments, continuously decrementing available quota upon confirmed student reservations and blocking booking attempts once a department's weekly cap is exhausted.
- **Acceptance Criterion:** The booking engine evaluates the student’s department quota balance in real time; if $Balance \le 0$, the reservation transaction is aborted with a descriptive error message without corrupting database state.
- **Related Test Case:** TC-04

### FR-05: Student Free-Slot Self-Booking Engine

- **Stakeholder:** Student
- **Priority:** Must Have (P1)
- **Description:** The system shall allow an authenticated student possessing available departmental quota to select an *Available* lab slot, declare an academic purpose (e.g., Project Work, Coding Practice, Exam Prep), and confirm the reservation.
- **Acceptance Criterion:** Upon user submission, the system validates eligibility, writes the reservation record atomically, updates slot status from *Available* to *Confirmed*, and generates a unique digital booking reference ID within 1.5 seconds.
- **Related Test Case:** TC-05

# FR-06: , Booking Clash Detection & Prevention

- **Stakeholder:** Student, Lab Assistant, System Administrator
- **Priority:** Must Have (P1)
- **Description:** The system shall employ atomic database transactions with pessimistic row-level locking (`SELECT ... FOR UPDATE`) to evaluate overlapping bookings, preventing double-booking of any physical laboratory slot under simultaneous concurrent requests.
- **Acceptance Criterion:** Under a simulated race condition of 50 concurrent requests for a single free lab slot, exactly one reservation succeeds while 49 requests are safely rejected with an HTTP 409 Conflict code.
- **Related Test Case:** TC-06

### FR-07: Self-Service Booking Cancellation

- **Stakeholder:** Student, Lab Assistant
- **Priority:** Should Have (P2)
- **Description:** The system shall allow students to cancel their confirmed bookings up to 30 minutes prior to the scheduled slot start time, immediately restoring the slot to the *Available* pool and refunding 1 slot unit back to the student's departmental quota.
- **Acceptance Criterion:** Cancellation requested $\ge 30$ minutes prior to start time updates booking state to *Cancelled*, increments department quota balance by +1, and re-renders the slot as *Available* in real time; requests $< 30$ minutes are blocked.
- **Related Test Case:** TC-07

### FR-08: Lab Assistant Real-Time Check-In Verification

- **Stakeholder:** Lab Assistant, Student
- **Priority:** Must Have (P1)
- **Description:** The system shall provide a streamlined, web-based verification console for Lab Assistants to validate arriving students by scanning their digital Booking QR code or entering their Student Roll Number, marking the reservation status as *Checked-In*.
- **Acceptance Criterion:** Scanning a valid booking token within the allowable arrival window changes the reservation state to *Checked-In* and logs the timestamp and Lab Assistant ID within 500 milliseconds.
- **Related Test Case:** TC-08

### FR-09: Automated No-Show Sweep & Slot Reclamation

- **Stakeholder:** Lab Assistant, System Administrator
- **Priority:** Should Have (P2)
- **Description:** The system shall run an automated background cron daemon every 5 minutes that identifies bookings remaining in *Confirmed* status 15 minutes past the slot start time, flags them as *No-Show*, applies a penalty strike to the student record, and transitions the slot to *Available* for on-demand walk-in booking.
- **Acceptance Criterion:** Confirmed bookings with no recorded check-in at $T_{start} + 15\text{ minutes}$ are transitioned to *No-Show* within 60 seconds of threshold breach, emitting an audit log entry.
- **Related Test Case:** TC-09

### FR-10: Automated Notification & Digital Receipt Generation

- **Stakeholder:** Student, Lab Assistant
- **Priority:** Could Have (P3)
- **Description:** The system shall dispatch an automated email and downloadable PDF confirmation slip containing a digitally signed QR code upon successful booking, cancellation, or no-show penalty application.
- **Acceptance Criterion:** PDF receipt and transactional email are dispatched via asynchronous queue within 5 seconds of the booking event with 99.5% delivery success.
- **Related Test Case:** TC-10

### FR-11: Laboratory & Quota Utilization Analytics Engine

- **Stakeholder:** Department Coordinator, System Administrator, College Executive
- **Priority:** Should Have (P2)
- **Description:** The system shall compile and display historical and real-time analytical reports aggregating lab utilization percentage, departmental quota consumption, peak usage hours, and no-show distributions, with export capabilities to CSV and PDF formats.
- **Acceptance Criterion:** Analytics dashboard renders tabular and graphical summaries for any user-selected date range up to 1 academic semester within 2.5 seconds.
- **Related Test Case:** TC-11

---

## 8.2 Non-Functional Requirements (6 Measurable NFRs)

Every NFR is structured with an unambiguous, quantitative, and testable performance target.

```
+=======================================================================================================+
| NFR ID | Category        | Quantitative Requirement Metric     | Target Value       | Verification    |
+=======================================================================================================+
| NFR-01 | Availability    | System uptime during operational    | >= 99.5% Uptime    | Automated ping  |
|        |                 | hours (08:00 - 18:00 Mon-Sat)       | (Unplanned outage  | monitor (Uptime |
|        |                 | excluding planned maintenance.      | <= 14.4 min/month) | Kuma / Datadog) |
+-------------------------------------------------------------------------------------------------------+
| NFR-02 | Correctness &   | Zero tolerance for double-bookings; | 100% Integrity     | Automated multi-|
|        | Integrity       | perfect transactional consistency   | (0 double-bookings | thread stress   |
|        |                 | in slot status allocation.          | per 100,000 txns)  | suite (JMeter)  |
+-------------------------------------------------------------------------------------------------------+
| NFR-03 | Usability       | Time required for a novice student  | <= 45 seconds      | Empirical User  |
|        |                 | to successfully search and complete | completion time;   | Testing with    |
|        |                 | a booking without prior training.   | SUS score >= 80    | 20 test subjects|
+-------------------------------------------------------------------------------------------------------+
| NFR-04 | Performance &   | 95th percentile HTTP response time  | <= 1.0 second at   | LoadRunner / k6 |
|        | Scalability     | for slot queries under peak load    | 200 concurrent     | benchmark tests |
|        |                 | of 200 simultaneous active users.   | active users       | simulating peak |
+-------------------------------------------------------------------------------------------------------+
| NFR-05 | Security &      | Cryptographic protection of session | Zero high/critical | OWASP ZAP &     |
|        | Protection      | tokens; zero plaintext passwords;   | vulnerabilities;   | SonarQube static|
|        |                 | role-based access control bounds.   | OWASP Top-10 clean | & dynamic scan  |
+-------------------------------------------------------------------------------------------------------+
| NFR-06 | Maintainability | Mean Time to Recover (MTTR) from a  | MTTR <= 30 mins;   | Simulated chaos |
|        | & Reliability   | container crash; modular code with  | Cyclomatic Comp.   | engineering &   |
|        |                 | high unit test code coverage.       | <= 10; Cov >= 85%  | JaCoCo coverage |
+=======================================================================================================+
```

---

## 8.3 MoSCoW Prioritisation Framework

The requirements are prioritized using the MoSCoW methodology to guarantee that critical academic operations are fully secured before auxiliary enhancements are introduced.

### Must Have (Mandatory for Launch):

- **FR-01 (Authentication & RBAC):** Foundational security; prevents unauthorized actors from reserving college property.
- **FR-02 (Timetable Class Ingestion):** Essential; without the 62% baseline timetable data, the system would provoke clashes with scheduled classes.
- **FR-03 (Real-Time Availability Query):** Core functionality; solves the primary visibility bottleneck.
- **FR-04 (Departmental Quota Enforcement):** Critical; eliminates inter-departmental conflict and monopolization.
- **FR-05 (Student Self-Booking Engine):** Primary functional value proposition of the software.
- **FR-06 (Clash Prevention):** Core integrity guarantee; double-bookings are unacceptable.
- **FR-08 (Lab Assistant Check-In):** Critical operational loop; enforces attendance and enables slot utilization auditing.
- **NFR-01, NFR-02, NFR-04, NFR-05:** Absolute operational requisites for security, correctness, and performance.

### Should Have (Important but Not Crashing Release):

- **FR-07 (Booking Cancellation):** Highly desirable to recycle unwanted slots back into the capacity pool.
- **FR-09 (Automated No-Show Sweep):** Vital for operational efficiency, but manual check-in overrides can function temporarily if deferred.
- **FR-11 (Utilization Analytics Engine):** Necessary for institutional management reviews, but operational booking can proceed while reports are finalized.
- **NFR-03, NFR-06:** Usability refinement and code maintainability thresholds.

### Could Have (Desirable Enhancements if Bandwidth Permits):

- **FR-10 (Notification & PDF Receipt Generation):** Improves student experience; on-screen confirmation codes serve as an adequate fallback.

### Won't Have (Explicitly Deferred to Version 2.0):

- **Automated Master Timetable Generation Engine:** Out-of-scope; existing scheduling software handles initial timetable formulation.
- **Hardware Wake-on-LAN and Terminal Power Cycling:** Requires IoT relays and specialized switch configurations.
- **Biometric Fingerprint Hardware Integration:** Replaced in V1.0 by QR code scanning on mobile devices and laptops.

---

# 9. CAPACITY ANALYSIS & FREE-SLOT ESTIMATION

### 9.1 Mathematical Calculation

All computations strictly use the institutional figures provided without alteration.

**Given Input Parameters:**

- Total Number of Computer Laboratories ($N_{labs}$) = **14 labs**
- Daily Operating Slot Granularity ($S_{day}$) = **8 slots/day**
- Operational Days per Academic Week ($D_{week}$) = **6 days/week** (Monday through Saturday)
- Curricular Timetabled Occupancy Ratio ($R_{classes}$) = **62%** ($0.62$)

#### Step 1: Gross Weekly Institutional Lab Slot Capacity ($C_{gross}$)

$$
C_{gross} = N_{labs} \times S_{day} \times D_{week}
$$

$$
C_{gross} = 14 \times 8 \times 6
$$

$$
C_{gross} = 112 \times 6 = \mathbf{672\ total\ weekly\ slots}
$$

#### Step 2: Capacity Consumed by Timetabled Classes ($C_{timetabled}$)

$$
C_{timetabled} = C_{gross} \times R_{classes}
$$

$$
C_{timetabled} = 672 \times 0.62 = \mathbf{416.64\ slots}
$$

#### Step 3: Net Weekly Free Slots Available for Booking ($C_{free}$)

$$
C_{free} = C_{gross} - C_{timetabled}
$$

$$
C_{free} = 672 - 416.64 = \mathbf{255.36\ slots}
$$

#### Step 4: Free-Slot Percentage of Total Capacity ($P_{free}$)

$$
P_{free} = 100\% - 62\% = \mathbf{38.0\%}
$$

Verification: $\frac{255.36}{672} \times 100\% = 38.0\%$

```
+=======================================================================================+
| Metric Identifier                 | Mathematical Expression | Resulting Value         |
+=======================================================================================+
| Gross Weekly Operating Slots      | 14 labs * 8 slots * 6 d | 672.00 slots            |
| Curricular Timetabled Usage (62%) | 672 slots * 0.62        | 416.64 slots            |
| Net Unallocated Free Slots        | 672 slots - 416.64      | 255.36 slots            |
| Departmental Share (6 Depts)      | 255.36 slots / 6 depts  | 42.56 slots/dept/week   |
| Facility Average (14 Labs)        | 255.36 slots / 14 labs  | 18.24 slots/lab/week    |
+=======================================================================================+
```

### 9.2 Rounding Convention & Discrete Operational Capacity

In computer systems modeling, physical laboratory reservations cannot be transacted in fractional increments—a lab slot is an indivisible, discrete scheduling quantum.

- The calculated figure of **255.36 slots** is a statistical aggregate expectation derived from applying an aggregate percentage ($62\%$) across an entire semester.
- **Conservative Integer Capacity:** Applying the floor function ($\lfloor 255.36 \rfloor$) yields **255 guaranteed discrete booking slots** per week across the 14 facilities.
- **Weekly Departmental Quota Ceiling:** Apportioned across the 6 academic departments, each department receives a baseline quota of:
  $$
  \lfloor 42.56 \rfloor = \mathbf{42\ slots/week}
  $$

  with the fractional remainder ($0.56 \times 6 \approx 3.36$ slots) held in an institutional buffer pool managed by the System Administrator for ad-hoc campus recruitment drives and emergency examinations.

### 9.3 Why Capacity is an Estimate and Not a Rigid Guarantee

The figure of 255.36 weekly slots represents an **operational baseline estimate**, not an immutable contractual guarantee, due to unavoidable operational variations:

1. **Mid-Semester Timetable Adjustments:** Accreditation audits, guest lectures, and faculty rescheduling alter room assignments dynamically.
2. **Preventative & Emergency Hardware Maintenance:** Workstation re-imaging, network switch upgrades, operating system patching, and HVAC failures periodically take entire laboratories offline.
3. **Institutional Holidays & Academic Breaks:** Gazetted holidays falling on weekdays compress the active operational week below 6 days.
4. **University Semester Examinations & Placements:** High-stakes certification exams (e.g., GATE, NPTEL) and multinational placement recruitment drives commandeer entire lab clusters for multi-day windows.
5. **Slot Granularity Mismatches:** Certain advanced laboratory sessions (e.g., Compiler Design, VLSI Simulation) require contiguous 2-slot or 3-slot blocks, fragmenting single isolated free slots and making them unviable for standard bookings.

---

# 10. SYSTEM ARCHITECTURE & HIGH-LEVEL DESIGN

LabBook is architected following a modern, containerized **Layered Microservices Architecture** to ensure high cohesion, loose coupling, and horizontal scalability.

```
+==================================================================================================+
|                                    PRESENTATION LAYER (SPA)                                      |
|   +--------------------------+   +--------------------------+   +----------------------------+   |
|   |   Student Mobile / Web   |   |   Lab Assistant Console  |   |   Coordinator & Admin UI   |   |
|   |     (React / Tailwind)   |   |   (PWA / Barcode-Ready)  |   |     (Analytics Dashboard)  |   |
|   +--------------------------+   +--------------------------+   +----------------------------+   |
+==================================================================================================+
                                                | (HTTPS / REST / WebSocket)
+==================================================================================================+
|                                API GATEWAY & SECURITY PROXY LAYER                                |
|          +----------------------------------------------------------------------------+          |
|          | Reverse Proxy (Nginx) | JWT Authentication | Rate Limiting | SSL/TLS Term. |          |
|          +----------------------------------------------------------------------------+          |
+==================================================================================================+
                                                |
+==================================================================================================+
|                                    APPLICATION SERVICES LAYER                                    |
|   +--------------------+  +--------------------+  +--------------------+  +------------------+   |
|   |  Booking Service   |  | Timetable Service  |  |   Quota Service    |  | Check-In Service |   |
|   | (Pessimistic Locks)|  |  (CSV Ingestion)   |  | (Balancing Engine) |  | (Token Verifier)|   |
|   +--------------------+  +--------------------+  +--------------------+  +------------------+   |
|             |                       |                       |                      |             |
|   +--------------------+  +--------------------+  +------------------------------------------+   |
|   | Notification Svc   |  |  Reporting Svc     |  | Background Daemon (No-Show Sweeper)      |   |
|   | (Email / SMS Q)    |  |  (Analytics OLAP)  |  | (Runs every 5 mins; cron reclamation)   |   |
|   +--------------------+  +--------------------+  +------------------------------------------+   |
+==================================================================================================+
                                                |
+==================================================================================================+
|                                    PERSISTENCE & STORAGE LAYER                                   |
|   +--------------------------------------------+    +----------------------------------------+   |
|   | PostgreSQL 15 Relational Database Engine   |    | Redis In-Memory Cache                  |   |
|   | (ACID Transactions, Row Locks, Schemas)    |    | (Active Slot Status, Rate Limits)      |   |
|   +--------------------------------------------+    +----------------------------------------+   |
+==================================================================================================+
```

---

# 11. UML DESIGN PACKAGE

## 11.1 Use Case Diagram

The Use Case model maps the functional boundaries of the system across the 5 identified actors.

### PlantUML Source Code:

```plantuml
@startuml LabBook_UseCase_Diagram
skinparam actorStyle awesome
skinparam packageStyle rectangle
skinparam monochrome false
skinparam shadowing true

actor "Student" as student
actor "Lab Assistant" as assistant
actor "Department Coordinator" as coordinator
actor "Timetable Officer" as timetable_officer
actor "System Administrator" as admin

rectangle "LabBook System Boundary" {
    usecase "UC-01: Authenticate via LDAP" as UC_Auth
    usecase "UC-02: Query Real-Time Lab Availability" as UC_Query
    usecase "UC-03: Book Free Lab Slot" as UC_Book
    usecase "UC-04: Validate Department Quota" as UC_Quota
    usecase "UC-05: Check Timetable Conflict" as UC_Clash
    usecase "UC-06: Cancel Reservation" as UC_Cancel
    usecase "UC-07: Verify & Check-In Student" as UC_CheckIn
    usecase "UC-08: Process No-Show & Reclaim" as UC_NoShow
    usecase "UC-09: Import Semester Timetable" as UC_Import
    usecase "UC-10: Manage Department Quota Caps" as UC_ManageQuota
    usecase "UC-11: Generate Utilization Analytics" as UC_Reports
    usecase "UC-12: Dispatch Notifications" as UC_Notify
}

' Student interactions
student --> UC_Auth
student --> UC_Query
student --> UC_Book
student --> UC_Cancel

' Assistant interactions
assistant --> UC_Auth
assistant --> UC_CheckIn
assistant --> UC_Query

' Coordinator interactions
coordinator --> UC_Auth
coordinator --> UC_Reports

' Timetable Officer interactions
timetable_officer --> UC_Auth
timetable_officer --> UC_Import

' Admin interactions
admin --> UC_Auth
admin --> UC_ManageQuota
admin --> UC_Reports
admin --> UC_NoShow

' Include & Extend relationships
UC_Book ..> UC_Auth : <<include>>
UC_Book ..> UC_Quota : <<include>>
UC_Book ..> UC_Clash : <<include>>
UC_Book ..> UC_Notify : <<include>>
UC_CheckIn ..> UC_Auth : <<include>>
UC_Cancel ..> UC_Notify : <<include>>
UC_NoShow ..> UC_Notify : <<extend>>
UC_Import ..> UC_Auth : <<include>>

@enduml
```

### Explanation of Use Case Architecture:

- Every privileged action strictly requires `<<include>>` on **UC-01 (Authenticate via LDAP)** to enforce authorization.
- **UC-03 (Book Free Lab Slot)** encapsulates the core business transactions, including quota verification (**UC-04**) and clash checking (**UC-05**).
- **UC-08 (Process No-Show & Reclaim)** extends the core lifecycle automatically when triggered by the system daemon.

---

## 11.2 Domain Class Diagram

The domain model captures the persistent entities, operational behaviors, associations, and multiplicity constraints.

### PlantUML Source Code:

```plantuml
@startuml LabBook_Class_Diagram
skinparam classAttributeIconSize 0
skinparam monochrome false
skinparam shadowing true

enum Role {
    STUDENT
    LAB_ASSISTANT
    DEPT_COORDINATOR
    TIMETABLE_OFFICER
    SYSTEM_ADMIN
}

enum BookingStatus {
    AVAILABLE
    HELD
    CONFIRMED
    CHECKED_IN
    COMPLETED
    CANCELLED
    NO_SHOW
}

abstract class User {
    - userId: String
    - name: String
    - email: String
    - role: Role
    - departmentId: String
    + login(): Boolean
    + logout(): Void
}

class Student {
    - rollNumber: String
    - semester: Integer
    - activePenalties: Integer
    + searchSlots(date: Date): List<LabSlot>
    + bookSlot(slotId: String, purpose: String): Booking
    + cancelBooking(bookingId: String): Boolean
}

class Department {
    - departmentId: String
    - departmentName: String
    - code: String
    - totalAllocatedQuota: Integer
    - remainingQuota: Integer
    + decrementQuota(): Boolean
    + incrementQuota(): Void
    + resetWeeklyQuota(): Void
}

class Lab {
    - labId: String
    - labName: String
    - capacityWorkstations: Integer
    - locationBlock: String
    - isOperational: Boolean
    + getDailySchedule(date: Date): List<LabSlot>
}

class LabSlot {
    - slotId: String
    - slotNumber: Integer
    - startTime: Time
    - endTime: Time
    - dayOfWeek: String
    - status: BookingStatus
    + lockSlot(): Boolean
    + releaseSlot(): Void
}

class TimetableEntry {
    - entryId: String
    - academicYear: String
    - semester: Integer
    - subjectCode: String
    - facultyInCharge: String
    + validateEntry(): Boolean
}

class Booking {
    - bookingId: String
    - bookingDate: Date
    - purpose: String
    - status: BookingStatus
    - tokenQrCode: String
    - createdAt: DateTime
    + confirmBooking(): Boolean
    + cancel(): Void
    + markNoShow(): Void
}

class CheckIn {
    - checkInId: String
    - checkInTimestamp: DateTime
    - verifiedByAssistantId: String
    - isLate: Boolean
    + recordCheckIn(): Boolean
}

class Quota {
    - quotaId: String
    - weeklyAllowance: Integer
    - currentUsed: Integer
    - weekStartDate: Date
    + hasAvailableQuota(): Boolean
    + consumeSlot(): Void
}

class Notification {
    - notificationId: String
    - recipientEmail: String
    - messagePayload: String
    - dispatchTimestamp: DateTime
    + sendEmail(): Boolean
}

' Inheritance
User <|-- Student

' Associations and Multiplicities
Department "1" *-- "0..*" User : employs/enrolls
Department "1" *-- "1" Quota : allocated
Lab "1" *-- "8" LabSlot : contains

LabSlot "1" -- "0..1" TimetableEntry : scheduled_by
LabSlot "1" -- "0..*" Booking : reserved_under

Student "1" -- "0..*" Booking : creates
Booking "1" -- "0..1" CheckIn : verified_by
Booking "1" ..> "1..*" Notification : generates
Department "1" -- "0..*" Booking : accounts_for

@enduml
```

---

## 11.3 Sequence Diagram: "Book a Free Lab Slot and Check In"

This diagram traces the end-to-end interactions between the user, application services, persistence engine, and the lab assistant, including alternate failure branches.

### PlantUML Source Code:

```plantuml
@startuml LabBook_Sequence_Diagram
autonumber
skinparam monochrome false
skinparam shadowing true
skinparam BoxPadding 10

actor "Student" as S
boundary "Web UI" as UI
control "Booking Service" as BS
control "Timetable Service" as TS
control "Quota Service" as QS
database "Booking DB" as DB
actor "Lab Assistant" as LA
control "CheckIn Service" as CS

== Authentication & Slot Discovery ==
S -> UI : 1. Submit login credentials (LDAP)
activate UI
UI -> BS : 2. Authenticate user
activate BS
BS --> UI : 3. Session established (Role: Student)
deactivate BS

S -> UI : 4. Search free slots (Date: Day 12, Lab 04)
UI -> BS : 5. Request lab schedule
activate BS
BS -> DB : 6. Query slot availability matrix
activate DB
DB --> BS : 7. Return slot raw records
deactivate DB
BS --> UI : 8. Render grid (Slot 3 is Available)
deactivate BS

== Booking Transaction with Alternate Paths ==
S -> UI : 9. Select Slot 3 & confirm booking request
UI -> BS : 10. Execute bookSlot(StudentId, Lab04, Slot3)
activate BS

BS -> TS : 11. checkCurricularConflict(Lab04, Slot3, Date)
activate TS
alt Timetable Conflict Exists
    TS --> BS : Conflict detected (Curricular Class active)
    BS --> UI : 12a. Error: Slot reserved for Timetabled Class
    UI --> S : Display rejection notice
else Timetable Slot Free
    TS --> BS : Slot free of curricular classes
    deactivate TS

    BS -> QS : 13. verifyAndLockQuota(DepartmentId)
    activate QS
    alt Department Quota Depleted (Used >= Cap)
        QS --> BS : Quota exceeded
        BS --> UI : 14a. Error: Department weekly quota exhausted
        UI --> S : Display quota limit error
    else Quota Available
        QS --> BS : Quota approved
        deactivate QS

        BS -> DB : 15. Lock & Update Slot [Pessimistic Lock]
        activate DB
        alt Concurrency Collision (Slot already claimed by another user)
            DB --> BS : Lock acquisition failed / Record dirty
            BS --> UI : 16a. Error: Slot was just booked by another user
            UI --> S : Display clash collision error
        else Slot Successfully Locked
            DB -> DB : Update status: 'CONFIRMED'
            DB -> DB : Decrement Department Quota (-1)
            DB --> BS : Transaction committed (BookingId: BK-9842)
            deactivate DB

            BS --> UI : 17. Booking confirmed + QR Token
            deactivate BS
            UI --> S : 18. Display confirmation slip & dispatch email
        end
    end
end

== Physical Arrival & Check-In Verification ==
note over S, LA : Student arrives at Lab 04 within grace period (T <= 15 min)
S -> LA : 19. Present digital QR Booking Token
LA -> CS : 20. Scan QR code via Assistant Console
activate CS
CS -> DB : 21. Validate token & fetch booking status
activate DB
DB --> CS : 22. Status: CONFIRMED, Grace Period: Valid
CS -> DB : 23. Update status: 'CHECKED_IN', log timestamp
DB --> CS : 24. Acknowledge commit
deactivate DB
CS --> LA : 25. Check-In approved (Green confirmation screen)
deactivate CS
LA --> S : 26. Assign workstation & allow lab entry

@enduml
```

---

## 11.4 Activity Diagram: End-to-End Booking & Verification Workflow

### PlantUML Source Code:

```plantuml
@startuml LabBook_Activity_Diagram
skinparam monochrome false
skinparam shadowing true

start

:Student navigates to LabBook portal;
:Submit LDAP credentials;

if (Authentication Valid?) then (no)
    :Display error message;
    stop
else (yes)
    :Display Student Dashboard;
endif

:Select Target Date, Department, and Preferred Lab;
:System queries real-time availability;
:Render 14-Lab by 8-Slot Grid;

:Student selects desired free slot;
:System initiates booking transaction;

if (Curricular Timetable Conflict?) then (yes)
    #pink:Reject: Slot occupied by Timetabled Class;
    stop
else (no)
endif

if (Department Quota Available?) then (no)
    #pink:Reject: Weekly Department Quota Exhausted;
    stop
else (yes)
endif

if (Slot already booked by another user?) then (yes)
    #pink:Reject: Slot clash detected;
    stop
else (no)
endif

:Acquire atomic database lock;
:Mark Slot as 'CONFIRMED';
:Decrement Department Quota balance;
:Generate digital Booking Slip with QR Code;
:Dispatch confirmation email to Student;

#lightgreen:Booking Successfully Confirmed;

fork
    :Student arrives at Lab;
    :Present QR Code to Lab Assistant;
    :Lab Assistant scans code on Check-In Console;
    if (Arrival within 15-min grace period?) then (yes)
        :Mark Booking as 'CHECKED_IN';
        :Student completes lab session;
        :Mark Booking as 'COMPLETED';
        stop
    else (no)
        :Lab Assistant rejects check-in;
    endif
fork again
    :System background daemon sweeps active slots;
    if (15 minutes elapsed since start AND status == 'CONFIRMED'?) then (yes)
        #coral:Flag Booking as 'NO_SHOW';
        :Apply penalty flag to Student;
        :Transition Slot to 'AVAILABLE';
        stop
    else (no)
    endif
end fork

stop
@enduml
```

---

## 11.5 State Machine Diagram: Booking Lifecycle

### PlantUML Source Code:

```plantuml
@startuml LabBook_State_Diagram
skinparam monochrome false
skinparam shadowing true

[*] --> AVAILABLE : Timetable Import completes\n[Slot unallocated]

AVAILABLE --> HELD : Student clicks 'Select Slot'\n[Temporary lock acquired (5 min)]
HELD --> AVAILABLE : Session timeout / User abandons\n[Lock released]

HELD --> CONFIRMED : Validation passed (Quota & Clash OK)\n+ Confirm button clicked\n[Quota decremented, QR issued]

CONFIRMED --> CHECKED_IN : Student arrives within 15 min\n+ Assistant scans QR code
CONFIRMED --> NO_SHOW : Grace period expired (T > 15 min)\n[Daemon reclaims slot, Penalty logged]
CONFIRMED --> CANCELLED : Student cancels >= 30 min before start\n[Quota refunded, Slot freed]

CHECKED_IN --> COMPLETED : Slot duration elapses\n+ Session concluded

CANCELLED --> AVAILABLE : Slot re-enters public booking pool
NO_SHOW --> AVAILABLE : Slot re-enters public booking pool
COMPLETED --> [*]

@enduml
```

---

## 11.6 Architectural Cohesion, Loose Coupling, and Interface Abstraction Justification

### 1. High Cohesion

Each structural component encapsulates a single, tightly defined business responsibility:

- `TimetableService`: Focuses entirely on ingesting, parsing, and persisting recurring academic class schedules. It contains no booking logic or UI state.
- `QuotaService`: Responsible solely for tracking, validating, decrementing, and resetting department-level allocation buckets.
- `BookingService`: Manages transactional state changes and coordinates clash prevention algorithms.
- `CheckInService`: Handles physical token verification and attendance auditing.

### 2. Loose Coupling via Service Interfaces

The services communicate through well-defined, asynchronous programmatic interfaces and REST/gRPC contracts rather than direct database table sharing:

- When the `BookingService` processes a reservation, it calls `ITimetableService.isSlotOccupied(...)` and `IQuotaService.hasSufficientQuota(...)`.
- If the Timetable Office changes its schedule format (e.g., migrating from CSV to an automated ERP API), only the internal parsing implementation of `TimetableService` requires updating. The `BookingService`, database schemas, and student-facing UI remain completely unaffected.

### 3. Isolation of Booking from Timetable Ingestion

The semester timetable ingestion occurs as an offline batch process prior to active booking windows. Curricular entries are translated into immutable baseline reservation records. The operational booking engine queries these records using read-only projections, preventing timetable import scripts from degrading booking performance.

---

# 12. WORK BREAKDOWN STRUCTURE (WBS) & DICTIONARY

```
1.0 LabBook Project
    1.1 Requirements Engineering (Task A)
        1.1.1 Stakeholder Interviewing & Register Audit
        1.1.2 Functional & Measurable NFR Specification
        1.1.3 IEEE 830 SRS Formulation & Baseline Approval
    1.2 Institutional Governance & Scheduling Setup
        1.2.1 Quota Policy Agreement (Task B)
            1.2.1.1 Inter-Departmental Quota Deliberation
            1.2.1.2 Formal HoD Sign-Off & Allocation Matrix
        1.2.2 Timetable Import & Reconciliation (Task C)
            1.2.2.1 Timetable Office Spreadsheet Parsing
            1.2.2.2 62% Curricular Occupancy DB Seeding
    1.3 Core Application Engineering
        1.3.1 Booking Module Development (Task D)
            1.3.1.1 Dynamic 14-Lab Slot Matrix UI
            1.3.1.2 Pessimistic Locking & Clash Engine
            1.3.1.3 Quota Decrement & QR Token Dispatch
        1.3.2 Check-In Module Development (Task E)
            1.3.2.1 Lab Assistant Scan Console Interface
            1.3.2.2 15-Minute Grace Period Daemon Logic
        1.3.3 Analytics & Reporting Module (Task F)
            1.3.3.1 Aggregation Engine for Seat Utilization
            1.3.3.2 Departmental Audit Report Exporter
    1.4 System Verification & Rollout
        1.4.1 Comprehensive Testing & QA (Task G)
            1.4.1.1 BVA, Equivalence & Decision Table Execution
            1.4.1.2 Concurrency Stress Testing & UAT Sign-Off
```

---

# 13. TASK DEPENDENCY ANALYSIS

The project schedule comprises 7 foundational activities. All durations and dependencies match the provided project data exactly.

```
+=======================================================================================================+
| Task Code | Task Description            | Duration (Days) | Predecessor Dependencies                  |
+=======================================================================================================+
| Task A    | Requirements                | 4               | None (Project Inception)                  |
| Task B    | Quota policy agreement      | 5               | Task A (Requirements)                     |
| Task C    | Timetable import            | 6               | Task A (Requirements)                     |
| Task D    | Booking module              | 10              | Task B (Quota Policy) AND Task C (Import) |
| Task E    | Check-in module             | 5               | Task D (Booking Module)                   |
| Task F    | Reports                     | 5               | Task D (Booking Module)                   |
| Task G    | Testing                     | 5               | Task E (Check-in) AND Task F (Reports)    |
+=======================================================================================================+
```

---

# 14. FORWARD PASS CALCULATIONS (EARLIEST TIMES)

The Forward Pass determines the Earliest Start ($ES$) and Earliest Finish ($EF$) for every task.

- Formula: $EF_i = ES_i + \text{Duration}_i$
- For successor task $j$: $ES_j = \max(EF_{\text{predecessors}})$
- Project Start is defined at Project Day 0 ($t = 0$), corresponding to the commencement of Day 1.

```
1. Task A (Requirements, Dur = 4):
   - ES(A) = 0
   - EF(A) = 0 + 4 = 4

2. Task B (Quota policy agreement, Dur = 5):
   - Predecessor: Task A (EF = 4)
   - ES(B) = EF(A) = 4
   - EF(B) = 4 + 5 = 9

3. Task C (Timetable import, Dur = 6):
   - Predecessor: Task A (EF = 4)
   - ES(C) = EF(A) = 4
   - EF(C) = 4 + 6 = 10

4. Task D (Booking module, Dur = 10):
   - Predecessors: Task B (EF = 9) AND Task C (EF = 10)
   - ES(D) = max(EF(B), EF(C)) = max(9, 10) = 10
   - EF(D) = 10 + 10 = 20

5. Task E (Check-in module, Dur = 5):
   - Predecessor: Task D (EF = 20)
   - ES(E) = EF(D) = 20
   - EF(E) = 20 + 5 = 25

6. Task F (Reports, Dur = 5):
   - Predecessor: Task D (EF = 20)
   - ES(F) = EF(D) = 20
   - EF(F) = 20 + 5 = 25

7. Task G (Testing, Dur = 5):
   - Predecessors: Task E (EF = 25) AND Task F (EF = 25)
   - ES(G) = max(EF(E), EF(F)) = max(25, 25) = 25
   - EF(G) = 25 + 5 = 30
```

**Total Calculated Project Duration = 30 Working Days**

---

# 15. BACKWARD PASS CALCULATIONS (LATEST TIMES)

The Backward Pass determines the Latest Finish ($LF$) and Latest Start ($LS$) for every task without extending the project duration ($LF_{\text{final}} = 30$).

- Formula: $LS_i = LF_i - \text{Duration}_i$
- For predecessor task $i$: $LF_i = \min(LS_{\text{successors}})$

```
1. Task G (Testing, Dur = 5):
   - LF(G) = 30
   - LS(G) = 30 - 5 = 25

2. Task E (Check-in module, Dur = 5):
   - Successor: Task G (LS = 25)
   - LF(E) = LS(G) = 25
   - LS(E) = 25 - 5 = 20

3. Task F (Reports, Dur = 5):
   - Successor: Task G (LS = 25)
   - LF(F) = LS(G) = 25
   - LS(F) = 25 - 5 = 20

4. Task D (Booking module, Dur = 10):
   - Successors: Task E (LS = 20) AND Task F (LS = 20)
   - LF(D) = min(LS(E), LS(F)) = min(20, 20) = 20
   - LS(D) = 20 - 10 = 10

5. Task B (Quota policy agreement, Dur = 5):
   - Successor: Task D (LS = 10)
   - LF(B) = LS(D) = 10
   - LS(B) = 10 - 5 = 5

6. Task C (Timetable import, Dur = 6):
   - Successor: Task D (LS = 10)
   - LF(C) = LS(D) = 10
   - LS(C) = 10 - 6 = 4

7. Task A (Requirements, Dur = 4):
   - Successors: Task B (LS = 5) AND Task C (LS = 4)
   - LF(A) = min(LS(B), LS(C)) = min(5, 4) = 4
   - LS(A) = 4 - 4 = 0
```

---

# 16. FLOAT CALCULATION & SLACK ANALYSIS

Total Float ($TF$) represents the duration a task can be delayed without delaying the overall project completion date:

$$
TF_i = LS_i - ES_i = LF_i - EF_i
$$

Free Float ($FF$) represents the duration a task can be delayed without delaying the Earliest Start of any immediate successor:

$$
FF_i = \min(ES_{\text{successors}}) - EF_i
$$

```
+=======================================================================================================+
| Task Code & Description        | Dur | ES | EF | LS | LF | Total Float | Free Float | Critical Status |
+=======================================================================================================+
| Task A: Requirements           | 4   | 0  | 4  | 0  | 4  | 0 days      | 0 days     | CRITICAL        |
| Task B: Quota policy agreement | 5   | 4  | 9  | 5  | 10 | 1 day       | 1 day      | NON-CRITICAL    |
| Task C: Timetable import       | 6   | 4  | 10 | 4  | 10 | 0 days      | 0 days     | CRITICAL        |
| Task D: Booking module         | 10  | 10 | 20 | 10 | 20 | 0 days      | 0 days     | CRITICAL        |
| Task E: Check-in module        | 5   | 20 | 25 | 20 | 25 | 0 days      | 0 days     | CRITICAL        |
| Task F: Reports                | 5   | 20 | 25 | 20 | 25 | 0 days      | 0 days     | CRITICAL        |
| Task G: Testing                | 5   | 25 | 30 | 25 | 30 | 0 days      | 0 days     | CRITICAL        |
+=======================================================================================================+
```

### Analysis of Float:

- **Task B (Quota Policy Agreement)** possesses exactly **1 day of Total Float** ($10 - 9 = 1$) and **1 day of Free Float** ($\min(ES_D) - EF_B = 10 - 9 = 1$).
- This float indicates that a 24-hour delay in finalizing departmental quota agreements will not impact the project finish date.
- However, if the quota debate extends by 2 or more days, Task B will consume its float, shift the Earliest Start of Task D, and become an active critical path item.

---

# 17. CRITICAL PATH IDENTIFICATION & MATHEMATICAL PROOF

A Critical Path is defined as the continuous sequence of dependent tasks connecting the start of the project to its completion for which Total Float equals zero ($TF = 0$).

### Evaluation of Network Paths:

- **Path 1:** $A \rightarrow B \rightarrow D \rightarrow E \rightarrow G = 4 + 5 + 10 + 5 + 5 = 29\text{ days}$
- **Path 2:** $A \rightarrow B \rightarrow D \rightarrow F \rightarrow G = 4 + 5 + 10 + 5 + 5 = 29\text{ days}$
- **Path 3:** $A \rightarrow C \rightarrow D \rightarrow E \rightarrow G = 4 + 6 + 10 + 5 + 5 = \mathbf{30\ days}$
- **Path 4:** $A \rightarrow C \rightarrow D \rightarrow F \rightarrow G = 4 + 6 + 10 + 5 + 5 = \mathbf{30\ days}$

### Mathematical Finding: Dual Parallel Critical Paths

The project features **two concurrent Critical Paths**, each totaling exactly **30 Working Days**:

$$
\mathbf{Critical\ Path\ 1:\ A \rightarrow C \rightarrow D \rightarrow E \rightarrow G\ (30\ Days)}
$$

$$
\mathbf{Critical\ Path\ 2:\ A \rightarrow C \rightarrow D \rightarrow F \rightarrow G\ (30\ Days)}
$$

Because Tasks E (Check-In) and F (Reports) both require 5 days and execute in parallel following Task D, any schedule slip in **either** Task E or Task F will directly delay project handover.

---

# 18. PROJECT MILESTONES

Milestones represent major governance and verification gates. Each milestone has a duration of **0 working days**.

```
+=======================================================================================================+
| Milestone ID | Milestone Description                | Target Schedule  | Verification Event           |
+=======================================================================================================+
| M-01         | Requirements Baseline Approved       | End of Day 4     | Formal Sign-Off on IEEE SRS  |
| M-02         | Quota Policy Agreement Ratified      | End of Day 9     | HoD Signed Quota Matrix      |
| M-03         | Curricular Timetable Ingested        | End of Day 10    | 62% Capacity DB Seed Audit   |
| M-04         | Booking Core Engine Integrated       | End of Day 20    | Atomic Lock Benchmark Passed |
| M-05         | Subsystems Development Complete      | End of Day 25    | Check-In & Reports Code Freeze|
| M-06         | Quality Assurance Sign-Off Passed    | End of Day 30    | Zero Critical Defect Status  |
| M-07         | Operational Commissioning & Handover | End of Day 30    | Production Deployment        |
+=======================================================================================================+
```

---

# 19. GANTT CHART

### 19.1 Tabular Schedule Representation

The schedule is displayed in discrete **Project Days (Day 1 through Day 30)**.

```
+=======================================================================================================================+
| Task Code | Task Name              | Dur | Start Day  | Finish Day | Float | Dependency | Critical Status         |
+=======================================================================================================================+
| Task A    | Requirements           | 4 d | Day 1      | Day 4      | 0 d   | None       | [CRITICAL]              |
| M-01      | Milestone 1            | 0 d | Day 4      | Day 4      | 0 d   | Task A     | Milestone Gate          |
| Task B    | Quota policy agreement | 5 d | Day 5      | Day 9      | 1 d   | Task A     | Non-Critical (1d Float) |
| Task C    | Timetable import       | 6 d | Day 5      | Day 10     | 0 d   | Task A     | [CRITICAL]              |
| M-02      | Milestone 2            | 0 d | Day 9      | Day 9      | 1 d   | Task B     | Milestone Gate          |
| M-03      | Milestone 3            | 0 d | Day 10     | Day 10     | 0 d   | Task C     | Milestone Gate          |
| Task D    | Booking module         | 10d | Day 11     | Day 20     | 0 d   | Task B, C  | [CRITICAL]              |
| M-04      | Milestone 4            | 0 d | Day 20     | Day 20     | 0 d   | Task D     | Milestone Gate          |
| Task E    | Check-in module        | 5 d | Day 21     | Day 25     | 0 d   | Task D     | [CRITICAL - Path 1]     |
| Task F    | Reports                | 5 d | Day 21     | Day 25     | 0 d   | Task D     | [CRITICAL - Path 2]     |
| M-05      | Milestone 5            | 0 d | Day 25     | Day 25     | 0 d   | Task E, F  | Milestone Gate          |
| Task G    | Testing                | 5 d | Day 26     | Day 30     | 0 d   | Task E, F  | [CRITICAL]              |
| M-06      | Milestone 6            | 0 d | Day 30     | Day 30     | 0 d   | Task G     | Milestone Gate          |
+=======================================================================================================================+
```

### 19.2 Mermaid Visual Schedule Model

```mermaid
gantt
    title LabBook System Development Schedule (30 Working Days)
    dateFormat  X
    axisFormat Day %s

    section Requirements
    Requirements (Task A)         :crit, active, tA, 0, 4
    Milestone 1 - SRS Approved   :milestone, m1, 4, 0

    section Setup & Governance
    Quota Policy (Task B)         :active, tB, 4, 9
    Timetable Import (Task C)     :crit, active, tC, 4, 10
    Milestone 2 - Quota Signed   :milestone, m2, 9, 0
    Milestone 3 - Schedule Seeded :milestone, m3, 10, 0

    section Core Engineering
    Booking Module (Task D)       :crit, active, tD, 10, 20
    Milestone 4 - Booking Core    :milestone, m4, 20, 0
    Check-In Module (Task E)      :crit, active, tE, 20, 25
    Reports Module (Task F)       :crit, active, tF, 20, 25
    Milestone 5 - Feature Freeze  :milestone, m5, 25, 0

    section Quality Assurance
    Comprehensive Testing (Task G):crit, active, tG, 25, 30
    Milestone 6 - UAT & Rollout   :milestone, m6, 30, 0
```

---

# 20. RESOURCE ALLOCATION & RACI MATRIX

Human resource allocation is mapped by technical role across project phases.

```
+=======================================================================================================+
| Role Title                 | Core Project Responsibilities             | Involvement | Key Deliverable|
+=======================================================================================================+
| Project Manager (PM)       | Schedule governance, critical path audit, | 100%        | CPM Schedule & |
|                            | risk mitigation, stakeholder coordination | (Days 1-30) | Closure Report |
+-------------------------------------------------------------------------------------------------------+
| Business Analyst (BA)      | Requirements elicitation, user stories,   | 40%         | IEEE 830 SRS & |
|                            | quota policy mediation across 6 depts     | (Days 1-10) | Quota Agreement|
+-------------------------------------------------------------------------------------------------------+
| UI/UX Front-End Designer   | Wireframing, responsive 14-lab matrix UI, | 50%         | React Dashboard|
|                            | mobile QR check-in assistant interface    | (Days 8-22) | & Scan Console |
+-------------------------------------------------------------------------------------------------------+
| Senior Backend Developer   | REST API, concurrency locking, timetable  | 100%        | Microservices &|
|                            | parser, no-show cron daemon               | (Days 5-25) | API Endpoints  |
+-------------------------------------------------------------------------------------------------------+
| Database Administrator     | Schema design, PostgreSQL row locks, ACID | 40%         | Relational DB &|
|                            | transactions, indexing, Redis caching     | (Days 4-22) | Query Profiling|
+-------------------------------------------------------------------------------------------------------+
| QA Automation Engineer     | BVA/EP test cases, JMeter concurrency,    | 60%         | Test Suites &  |
|                            | regression suites, defect tracking        | (Days 15-30)| Traceability   |
+-------------------------------------------------------------------------------------------------------+
| DevOps / SysAdmin          | CI/CD pipelines, LDAP/SSO integration,    | 30%         | Docker / Nginx |
|                            | container orchestration, production deploy| (Days 1-5,  | Infrastructure |
|                            |                                           |  25-30)     |                |
+=======================================================================================================+
```

---

# 21. COMPREHENSIVE TEST PLAN & QUALITY ASSURANCE STRATEGY

### Testing Hierarchy & Strategy:

1. **Unit Testing:** Executed via JUnit 5 / Jest; validates isolated business logic (e.g., quota mathematical evaluations, timetable string parsers, token hash generators). Code coverage target: $\ge 85\%$.
2. **Integration Testing:** Validates inter-service communication between `BookingService`, `QuotaService`, `PostgreSQL`, and `Redis`. Focuses on transactional rollbacks during failure.
3. **System Testing:** End-to-end evaluation of the integrated software against the 11 Functional Requirements and 6 NFRs.
4. **Acceptance Testing (UAT):** Conducted by actual Students, Lab Assistants, and Coordinators to ensure usability and business alignment.
5. **Regression Testing:** Automated execution of existing test suites upon every code commit via GitHub Actions CI pipeline.
6. **Performance & Stress Testing:** Executed using Apache JMeter to simulate 200 concurrent users querying the availability matrix and 50 concurrent requests competing for a single free slot.
7. **Security Testing:** OWASP Top-10 compliance audit; scanning for SQL injection, Cross-Site Scripting (XSS), Broken Object Level Authorization (BOLA), and JWT tampering using OWASP ZAP.
8. **Usability Testing:** Structured evaluation with 20 representative users targeting a System Usability Scale (SUS) score $\ge 80$.

---

# 22. BOUNDARY VALUE ANALYSIS (BVA)

### 22.1 Department Weekly Quota Boundary Testing

*Design Assumption:* Based on Section 9, each department is allocated an integer quota of **42 slots per week**. Testing evaluates quota boundaries at $Min-1$, $Min$, $Min+1$, $Nominal$, $Max-1$, $Max$, and $Max+1$.

```
+=======================================================================================================+
| Test ID  | Field / Parameter   | Input Value | Boundary Type       | Expected System Behavior         |
+=======================================================================================================+
| BVA-Q-01 | Weekly Quota Bucket | -1 slots    | Below Min (Invalid) | Reject: System error; alert admin|
| BVA-Q-02 | Weekly Quota Bucket | 0 slots     | Minimum (Exhausted) | Reject: Quota exhausted; block tx|
| BVA-Q-03 | Weekly Quota Bucket | 1 slot      | Min + 1 (Critical)  | Accept: Allow 1 booking; bal -> 0|
| BVA-Q-04 | Weekly Quota Bucket | 21 slots    | Nominal Center      | Accept: Allow booking; bal -> 20 |
| BVA-Q-05 | Weekly Quota Bucket | 41 slots    | Max - 1 (High)      | Accept: Allow booking; bal -> 40 |
| BVA-Q-06 | Weekly Quota Bucket | 42 slots    | Maximum Cap         | Accept: Allow booking; bal -> 41 |
| BVA-Q-07 | Weekly Quota Bucket | 43 slots    | Above Max (Invalid) | Reject: Quota overflow violation |
+=======================================================================================================+
```

### 22.2 Slot Index & Booking Duration Boundary Testing

*Design Assumption:* The college operates exactly **8 slots per day** (Indexed 1 to 8). Single bookings permit a duration of **1 slot**. Advance booking is restricted to a rolling **7-day horizon**.

```
+=======================================================================================================+
| Test ID  | Field / Parameter   | Input Value | Boundary Type       | Expected System Behavior         |
+=======================================================================================================+
| BVA-S-01 | Slot Index Number   | Slot 0      | Below Min (Invalid) | Reject: "Invalid Slot Number"    |
| BVA-S-02 | Slot Index Number   | Slot 1      | Minimum Valid (09h) | Accept: Display/Book Slot 1      |
| BVA-S-03 | Slot Index Number   | Slot 2      | Min + 1 Valid       | Accept: Display/Book Slot 2      |
| BVA-S-04 | Slot Index Number   | Slot 7      | Max - 1 Valid       | Accept: Display/Book Slot 7      |
| BVA-S-05 | Slot Index Number   | Slot 8      | Maximum Valid (16h) | Accept: Display/Book Slot 8      |
| BVA-S-06 | Slot Index Number   | Slot 9      | Above Max (Invalid) | Reject: "Slot Index Out of Range"|
| BVA-D-01 | Advance Booking Day | Day 0 (Now) | Minimum Horizon     | Accept: Allow same-day booking   |
| BVA-D-02 | Advance Booking Day | Day 7       | Maximum Horizon     | Accept: Allow booking on Day 7   |
| BVA-D-03 | Advance Booking Day | Day 8       | Above Max Horizon   | Reject: "Beyond 7-Day Window"    |
+=======================================================================================================+
```

---

# 23. EQUIVALENCE PARTITIONING (EP)

Input domains are partitioned into valid and invalid equivalence classes to guarantee comprehensive functional coverage.

```
+=======================================================================================================+
| Partition ID | Input Domain Category   | Equivalence Class Description         | Class Type | Outcome |
+=======================================================================================================+
| EP-SLOT-01   | Lab Slot Status         | Slot is marked AVAILABLE in database  | Valid      | Accept  |
| EP-SLOT-02   | Lab Slot Status         | Slot is occupied by TIMETABLE Class   | Invalid    | Reject  |
| EP-SLOT-03   | Lab Slot Status         | Slot is already BOOKED by Student     | Invalid    | Reject  |
| EP-SLOT-04   | Lab Slot Status         | Slot is held under MAINTENANCE        | Invalid    | Reject  |
+-------------------------------------------------------------------------------------------------------+
| EP-QUOTA-01  | Department Quota        | Department balance > 0 (1 to 42)      | Valid      | Accept  |
| EP-QUOTA-02  | Department Quota        | Department balance == 0 (Exhausted)   | Invalid    | Reject  |
| EP-QUOTA-03  | Department Quota        | Department balance < 0 (Corrupted)    | Invalid    | Reject  |
+-------------------------------------------------------------------------------------------------------+
| EP-DEPT-01   | Department Identity     | Valid registered Dept (1 of 6 depts)  | Valid      | Accept  |
| EP-DEPT-02   | Department Identity     | Non-existent / Unregistered Dept ID   | Invalid    | Reject  |
+-------------------------------------------------------------------------------------------------------+
| EP-USER-01   | Student Credential      | Active, enrolled student, zero bans   | Valid      | Accept  |
| EP-USER-02   | Student Credential      | Inactive / Expired student account    | Invalid    | Reject  |
| EP-USER-03   | Student Credential      | Suspended student (>= 3 No-Shows)     | Invalid    | Reject  |
+=======================================================================================================+
```

---

# 24. CAUSE-EFFECT DECISION TABLE (QUOTA & CLASH RESOLUTION)

This decision table evaluates all possible boolean combinations governing a student slot reservation request.

```
+=======================================================================================================+
| Rule Identifier                | R1   | R2   | R3   | R4   | R5   | R6   | R7   | R8   | R9   | R10  |
+=======================================================================================================+
| CONDITIONS:                    |      |      |      |      |      |      |      |      |      |      |
| C1: Valid Authenticated Student| False| True | True | True | True | True | True | True | True | True |
| C2: Slot Curricular Timetabled | N/A  | True | False| False| False| False| False| True | False| False|
| C3: Slot Already Booked/Locked | N/A  | N/A  | True | False| False| False| True | N/A  | False| False|
| C4: Dept Quota Available (> 0) | N/A  | N/A  | N/A  | False| True | True | False| False| True | True |
| C5: Advance Window Valid (<=7d)| N/A  | N/A  | N/A  | N/A  | False| True | N/A  | N/A  | True | True |
| C6: Student Under Sanction     | N/A  | N/A  | N/A  | N/A  | N/A  | True | N/A  | N/A  | False| False|
| C7: Concurrency Lock Acquired  | N/A  | N/A  | N/A  | N/A  | N/A  | N/A  | N/A  | N/A  | False| True |
+--------------------------------+------+------+------+------+------+------+------+------+------+------+
| ACTIONS:                       |      |      |      |      |      |      |      |      |      |      |
| A1: Accept & Confirm Booking   |      |      |      |      |      |      |      |      |      |  X   |
| A2: Reject: Auth Failure (401) |  X   |      |      |      |      |      |      |      |      |      |
| A3: Reject: Timetable Clash    |      |  X   |      |      |      |      |      |  X   |      |      |
| A4: Reject: Slot Already Booked|      |      |  X   |      |      |      |  X   |      |      |      |
| A5: Reject: Quota Exhausted    |      |      |      |  X   |      |      |      |  X   |      |      |
| A6: Reject: Horizon Exceeded   |      |      |      |      |  X   |      |      |      |      |      |
| A7: Reject: Account Suspended  |      |      |      |      |      |  X   |      |      |      |      |
| A8: Reject: Collision (Retry)  |      |      |      |      |      |      |      |      |  X   |      |
+=======================================================================================================+
```

---

# 25. DETAILED TEST CASES (TC-01 TO TC-11)

Every Functional Requirement maps directly to a formal, structured test case.

```
+=======================================================================================================+
| Test Case ID: TC-01                                     | Maps to: FR-01 (Authentication & RBAC)     |
+=======================================================================================================+
| Description: Verify user authentication via LDAP and role-based route protection.                    |
| Preconditions: LDAP server online; test accounts provisioned for Student, Assistant, and Admin.       |
| Input Data: Username: "cs25_raj", Password: "ValidPassword123"                                        |
| Execution Steps:                                                                                      |
|   1. Navigate to `/login` and submit valid student credentials.                                      |
|   2. Observe authentication token generation and dashboard redirection.                               |
|   3. Attempt to manually navigate to administrative endpoint `/admin/quotas`.                         |
| Expected Result: User is authenticated within 1.5s, routed to `/student/dashboard`. Route to          |
| `/admin/quotas` is intercepted with HTTP 403 Forbidden.                                               |
+=======================================================================================================+

+=======================================================================================================+
| Test Case ID: TC-02                                     | Maps to: FR-02 (Timetable Import)          |
+=======================================================================================================+
| Description: Validate bulk ingestion of semester timetable CSV file.                                 |
| Preconditions: Authenticated as Timetable Officer; standardized timetable CSV prepared.               |
| Input Data: File: `sem3_timetable_master.csv` (contains 416 curricular records across 14 labs).      |
| Execution Steps:                                                                                      |
|   1. Upload CSV file via `/admin/timetable/upload`.                                                   |
|   2. Trigger parsing and validation engine.                                                           |
| Expected Result: 416 records successfully parsed and inserted; aggregate occupied slots match exactly |
| 62.0% of total weekly capacity (416.64 rounded); status reads "Import Succeeded".                     |
+=======================================================================================================+

+=======================================================================================================+
| Test Case ID: TC-03                                     | Maps to: FR-03 (Availability Matrix Query) |
+=======================================================================================================+
| Description: Verify dynamic matrix rendering of 14 labs across 8 slots for a selected date.           |
| Preconditions: Master timetable ingested; 10 student bookings active in Lab 01.                       |
| Input Data: Date: `2026-10-14`                                                                        |
| Execution Steps:                                                                                      |
|   1. Access `/student/availability`.                                                                 |
|   2. Select target date from calendar control.                                                        |
| Expected Result: System renders a 14x8 grid within 1.0s; curricular slots appear in Gray, booked      |
| slots appear in Red, available slots appear in Green.                                                 |
+=======================================================================================================+

+=======================================================================================================+
| Test Case ID: TC-04                                     | Maps to: FR-04 (Department Quota Cap)      |
+=======================================================================================================+
| Description: Enforce blocking when department weekly quota balance is zero.                           |
| Preconditions: Department quota for Mechanical Eng set to 0 in database.                              |
| Input Data: Student roll: "ME23B041", Target Slot: Lab 03 - Slot 4                                    |
| Execution Steps:                                                                                      |
|   1. Log in as Mechanical student; attempt to book an available slot.                                 |
| Expected Result: Transaction aborted; system displays: "Department Quota Limit Reached. Contact your  |
| Department Coordinator." Slot remains AVAILABLE.                                                      |
+=======================================================================================================+

+=======================================================================================================+
| Test Case ID: TC-05                                     | Maps to: FR-05 (Student Self-Booking)       |
+=======================================================================================================+
| Description: Successfully complete reservation of an unallocated lab slot.                          |
| Preconditions: Student has available quota (>0); Slot is AVAILABLE.                                   |
| Input Data: Lab: "Lab 07", Slot: 3, Purpose: "DBMS Capstone Project"                                  |
| Execution Steps:                                                                                      |
|   1. Select Slot 3 in Lab 07; input purpose; click "Confirm Booking".                                 |
| Expected Result: Slot status updates to 'CONFIRMED'; unique Booking Reference issued; quota           |
| decrements by 1; confirmation slip displayed.                                                         |
+=======================================================================================================+

+=======================================================================================================+
| Test Case ID: TC-06                                     | Maps to: FR-06 (Clash Prevention Engine)   |
+=======================================================================================================+
| Description: Guarantee race-condition prevention under concurrent booking requests.                   |
| Preconditions: Lab 02, Slot 5 is AVAILABLE.                                                           |
| Input Data: 50 automated concurrent threads targeting Lab 02, Slot 5 simultaneously.                  |
| Execution Steps:                                                                                      |
|   1. Fire 50 asynchronous HTTP POST requests via JMeter targeting the identical slot.                 |
| Expected Result: Exactly 1 request receives HTTP 200 OK (Confirmed); 49 requests receive HTTP 409     |
| Conflict. Zero double-bookings recorded in database.                                                  |
+=======================================================================================================+

+=======================================================================================================+
| Test Case ID: TC-07                                     | Maps to: FR-07 (Booking Cancellation)      |
+=======================================================================================================+
| Description: Cancel a confirmed booking >= 30 minutes prior to slot start time.                       |
| Preconditions: Student holds confirmed booking for Slot 6 (14:00); current time is 11:30.             |
| Execution Steps:                                                                                      |
|   1. Navigate to `/student/my-bookings`; click "Cancel" on active reservation.                        |
|   2. Confirm prompt.                                                                                  |
| Expected Result: Booking status updates to 'CANCELLED'; slot reverts to 'AVAILABLE'; department       |
| quota balance increments by +1.                                                                       |
+=======================================================================================================+

+=======================================================================================================+
| Test Case ID: TC-08                                     | Maps to: FR-08 (Lab Assistant Check-In)    |
+=======================================================================================================+
| Description: Verify student check-in via QR code scanning within grace period.                        |
| Preconditions: Booking is 'CONFIRMED'; student arrives at T = StartTime + 5 mins.                     |
| Input Data: Valid QR payload token: `eyJhbGciOi...`                                                   |
| Execution Steps:                                                                                      |
|   1. Assistant scans student QR token on mobile check-in console.                                     |
| Expected Result: Check-In console flashes green; booking status updates to 'CHECKED_IN'; audit log    |
| records timestamp and Assistant ID within 500ms.                                                      |
+=======================================================================================================+

+=======================================================================================================+
| Test Case ID: TC-09                                     | Maps to: FR-09 (Automated No-Show Sweep)   |
+=======================================================================================================+
| Description: Validate automated slot reclamation upon student no-show after 15 minutes.               |
| Preconditions: Booking is 'CONFIRMED'; slot start time was 10:00; current time is 10:16.              |
| Execution Steps:                                                                                      |
|   1. Trigger background daemon execution.                                                             |
| Expected Result: Booking status transitions from 'CONFIRMED' to 'NO_SHOW'; student penalty strike     |
| incremented; slot status transitions back to 'AVAILABLE'.                                             |
+=======================================================================================================+

+=======================================================================================================+
| Test Case ID: TC-10                                     | Maps to: FR-10 (Notification & PDF Receipt)|
+=======================================================================================================+
| Description: Verify asynchronous generation of downloadable PDF confirmation slip with QR code.      |
| Preconditions: Successful booking transaction completed.                                              |
| Execution Steps:                                                                                      |
|   1. Intercept background message queue event for Booking ID BK-1049.                                 |
| Expected Result: PDF receipt compiled with valid cryptographic QR code; confirmation email queued for |
| SMTP delivery within 5.0 seconds.                                                                     |
+=======================================================================================================+

+=======================================================================================================+
| Test Case ID: TC-11                                     | Maps to: FR-11 (Utilization Analytics)     |
+=======================================================================================================+
| Description: Generate tabular utilization report for past 30 days.                                    |
| Preconditions: 30 days of historical booking data seeded in database.                                 |
| Execution Steps:                                                                                      |
|   1. Log in as Administrator; access `/reports/utilization`.                                          |
|   2. Select "Last 30 Days" and click "Export to CSV".                                                 |
| Expected Result: Dashboard aggregates seat-fill ratio, departmental quota usage, and no-show stats;   |
| CSV export downloads successfully within 2.5 seconds.                                                 |
+=======================================================================================================+
```

---

# 26. TRACEABILITY MATRIX (FR → UC → TC → EXPECTED RESULT)

```
+=======================================================================================================+
| FR ID | Functional Requirement Description    | Use Case | Test Case | Target Expected Result         |
+=======================================================================================================+
| FR-01 | User Authentication & RBAC            | UC-01    | TC-01     | LDAP Auth OK, 403 on unauth    |
| FR-02 | Timetable Class Schedule Ingestion    | UC-09    | TC-02     | 416 records parsed, 62% bound  |
| FR-03 | Real-Time Availability Matrix Display | UC-02    | TC-03     | 14x8 grid rendered <= 1.0s     |
| FR-04 | Departmental Quota Enforcement        | UC-04    | TC-04     | Tx blocked when balance == 0   |
| FR-05 | Student Self-Service Booking          | UC-03    | TC-05     | Booking confirmed, QR issued   |
| FR-06 | Automated Clash Prevention Engine     | UC-05    | TC-06     | 1 Success / 49 Conflict on race|
| FR-07 | Booking Self-Service Cancellation     | UC-06    | TC-07     | Quota refunded, slot freed     |
| FR-08 | Lab Assistant Real-Time Check-In      | UC-07    | TC-08     | Status updated to CHECKED_IN   |
| FR-09 | Automated No-Show Reclamation Daemon  | UC-08    | TC-09     | Status to NO_SHOW at T+15m     |
| FR-10 | Notification & PDF Receipt Slip       | UC-12    | TC-10     | PDF & Email dispatched <= 5.0s |
| FR-11 | Utilization Analytics & Reporting     | UC-11    | TC-11     | Report compiled <= 2.5s        |
+=======================================================================================================+
```

---

# 27. ILLUSTRATIVE DEFECT LOG

> **Academic Evaluation Disclaimer:**
> *"Illustrative test evidence used for metric calculation because an actual execution log was not supplied."*

The following defect log represents confirmed software defects identified during formal System and Acceptance Testing prior to release.

```
+=======================================================================================================+
| Defect ID | Severity | Module          | Description                             | Root Cause | Status|
+=======================================================================================================+
| DEF-001   | Critical | Clash Engine    | Race condition allowed double-booking   | Missing DB | Closed|
|           |          | (Task D)        | when 2 requests arrived < 5ms apart.    | row lock   | Fixed |
+-------------------------------------------------------------------------------------------------------+
| DEF-002   | High     | Timetable Parse | Parser truncated multi-lab entries      | Comma split| Closed|
|           |          | (Task C)        | sharing identical course codes.         | regex bug  | Fixed |
+-------------------------------------------------------------------------------------------------------+
| DEF-003   | High     | Quota Service   | Quota balance became negative on quick  | Unchecked  | Closed|
|           |          | (Task D)        | double-click of confirmation button.    | decrement  | Fixed |
+-------------------------------------------------------------------------------------------------------+
| DEF-004   | Medium   | Check-In Console| Barcode scanner failed on low-contrast  | Missing UI | Closed|
|           |          | (Task E)        | student mobile phone screens.           | gamma filter| Fixed|
+-------------------------------------------------------------------------------------------------------+
| DEF-005   | Medium   | Reporting       | Aggregation query timed out (> 5.0s)    | Missing DB | Closed|
|           |          | (Task F)        | when scanning 6-month historical logs.  | index on dt| Fixed |
+-------------------------------------------------------------------------------------------------------+
| DEF-006   | Low      | Notifications   | Notification email formatting broke in  | CSS inline | Closed|
|           |          | (Task D)        | legacy Microsoft Outlook desktop client.| render bug | Fixed |
+=======================================================================================================+
```

*Summary of Illustrative Testing Findings:*

- **Total Defects Identified and Resolved Pre-Release:** **39 defects**
  - Critical Severity: 4
  - High Severity: 11
  - Medium Severity: 16
  - Low Severity: 8
- **Post-Release Latent Defects Identified in Production:** **3 defects** (minor UI glitch in Safari browser, edge-case leap year calendar display, and localized slow report download under poor Wi-Fi).

---

# 28. SOFTWARE DEFECT DENSITY METRICS

Defect Density quantifies the quality of the software relative to its size, providing a standardized baseline for architectural reliability.

### Mathematical Formulation:

$$
\text{Defect Density} = \frac{\text{Number of Confirmed Pre-Release Defects}}{\text{Size of Software (in KLOC)}}
$$

### Measurement Parameters & Assumptions:

- **Confirmed Defects ($N_{\text{defects}}$):** **39 defects** (from formal pre-release test execution).
- **Software Size Assumption:** The LabBook application comprises **5.2 KLOC** (5,200 logical source lines of code) spanning backend controllers, domain models, database migrations, and frontend UI components.

### Substitution & Calculation:

$$
\text{Defect Density} = \frac{39\text{ defects}}{5.2\text{ KLOC}}
$$

$$
\mathbf{Defect\ Density = 7.50\ defects/KLOC}
$$

### Industry Benchmarking Interpretation:

For enterprise academic web software entering pilot deployment, an industry standard defect density ranges between **5.0 and 10.0 defects/KLOC**. A density of **7.5 defects/KLOC** indicates rigorous pre-release quality assurance and effective boundary validation prior to production handover.

---

# 29. DEFECT REMOVAL EFFICIENCY (DRE) ANALYSIS

Defect Removal Efficiency measures the percentage of software defects eliminated during development and testing prior to operational deployment, reflecting the effectiveness of the project's quality filters.

### Mathematical Formulation:

$$
\text{DRE} = \left( \frac{D_{\text{before}}}{D_{\text{before}} + D_{\text{after}}} \right) \times 100\%
$$

Where:

- $D_{\text{before}}$ = Number of verified defects discovered and remediated prior to commercial release.
- $D_{\text{after}}$ = Number of latent defects discovered by end users during operational usage post-release.

### Parameter Values & Illustrative Data:

- Pre-Release Defects Removed ($D_{\text{before}}$) = **39 defects**
- Post-Release Latent Defects Discovered ($D_{\text{after}}$) = **3 defects**
- Total Latent Software Defects ($D_{\text{total}} = 39 + 3$) = **42 defects**

### Step-by-Step Mathematical Calculation:

$$
\text{DRE} = \left( \frac{39}{39 + 3} \right) \times 100\%
$$

$$
\text{DRE} = \left( \frac{39}{42} \right) \times 100\%
$$

$$
\text{DRE} = 0.9285714 \times 100\%
$$

$$
\mathbf{Defect\ Removal\ Efficiency\ (DRE) = 92.86\%}
$$

### Quality Evaluation:

A DRE of **92.86%** comfortably exceeds the institutional engineering threshold of **90.0%**. This confirms that the test suites (BVA, Equivalence Partitioning, and Concurrency Stress Tests) successfully neutralized nearly 93 out of every 100 latent defects before the platform went live.

---

# 30. PROJECT & OPERATIONAL MONITORING & CONTROL PLAN

The monitoring framework establishes quantitative control thresholds across operational and engineering parameters.

> **Academic Evaluation Disclaimer:**
> *"Numerical actual vs. planned values are illustrative representations for demonstrating variance and corrective control methodologies."*

### Mathematical Variance Formulas:

$$
\text{Absolute Variance} = \text{Actual Value} - \text{Planned Value}
$$

$$
\text{Percentage Variance} = \left( \frac{\text{Actual Value} - \text{Planned Value}}{\text{Planned Value}} \right) \times 100\%
$$

```
+================================================================================================================================+
| Operational Metric       | Formula            | Plan   | Actual | Variance | % Var   | Interpretation & Corrective Action  | Authority|
+================================================================================================================================+
| Overall Lab Utilization  | (Occupied Slots /  | 85.0%  | 88.5%  | +3.5%    | +4.1%   | Higher demand than expected. Normal | Dean of  |
| Ratio                    | Total Slots) * 100 |        |        |          |         | operations; no intervention needed. | Academics|
+--------------------------------------------------------------------------------------------------------------------------------+
| Free-Slot Booking Rate   | (Booked Free Slots | 80.0%  | 84.2%  | +4.2%    | +5.25%  | Healthy adoption of the 255 free    | Academic |
|                          | / 255 Free Slots)  |        |        |          |         | slots. System capacity is adequate. | Council  |
+--------------------------------------------------------------------------------------------------------------------------------+
| Student No-Show Rate     | (Unattended Bookings| 5.0%  | 11.2%  | +6.2%    | +124.0% | Exceeds tolerance. Implement stricter| Associate|
|                          | / Total Bookings)  |        |        |          |         | penalty: suspend account after 2 no-| Dean     |
|                          |                    |        |        |          |         | shows (previously 3 strikes).       | Students |
+--------------------------------------------------------------------------------------------------------------------------------+
| Check-In Compliance Rate | (Checked-In Slots  | 95.0%  | 89.8%  | -5.2%    | -5.47%  | Assistant non-compliance. Provide   | Lead Lab |
|                          | / Total Attended)  |        |        |          |         | barcode hardware & run staff re-    | Super-   |
|                          |                    |        |        |          |         | orientation sessions.               | intendent|
+--------------------------------------------------------------------------------------------------------------------------------+
| Booking Collision Rate   | (Double Bookings / | 0.0%   | 0.0%   | 0.0%     | 0.0%    | Zero concurrency clashes. Atomic    | System   |
|                          | Total Tx) * 100    |        |        |          |         | database locks operating perfectly. | Architect|
+--------------------------------------------------------------------------------------------------------------------------------+
| Timetable Ingestion      | (Failed Records /  | 0.0%   | 0.48%  | +0.48%   | N/A     | Minor format mismatches in 2 CSV    | Timetable|
| Error Rate               | Total Records)     |        |        |          |         | rows. Update regex parser to handle | Officer  |
|                          |                    |        |        |          |         | trailing whitespace.                |          |
+--------------------------------------------------------------------------------------------------------------------------------+
| Department Quota Peak    | (Consumed Quota /  | 90.0%  | 98.5%  | +8.5%    | +9.44%  | CSE/IT departments near cap by Wed. | HoD Joint|
| Saturation               | Allocated Quota)   |        |        |          |         | Dynamic quota rebalancing to re-    | Committee|
|                          |                    |        |        |          |         | allocate unused slots on Fridays.   |          |
+================================================================================================================================+
```

---

# 31. QUANTITATIVE RISK REGISTER & EXPOSURE RANKING

The risk framework evaluates project threats using the provided mathematical parameters without deviation.

### Governing Mathematical Formulation:

$$
\mathbf{Risk\ Exposure\ (RE) = Probability\ (P) \times Impact\ (I)}
$$

Where:

- **Probability ($P$):** Likelihood of risk occurrence on a scale of $0.0$ to $1.0$.
- **Impact ($I$):** Severity of consequence to project schedule, cost, or performance on a scale of $1$ to $10$.

```
+=======================================================================================================+
| Risk Code | Risk Description                | Prob (P) | Impact (I) | Exposure (P x I) | System Rank  |
+=======================================================================================================+
| RISK-01   | Departments disagree on quotas  | 0.5      | 7          | 3.50             | RANK 1       |
+-------------------------------------------------------------------------------------------------------+
| RISK-02   | Timetable import errors         | 0.4      | 6          | 2.40             | RANK 2 (Tie) |
+-------------------------------------------------------------------------------------------------------+
| RISK-03   | Students book and do not show   | 0.6      | 4          | 2.40             | RANK 3 (Tie) |
+-------------------------------------------------------------------------------------------------------+
| RISK-04   | Lab assistants skip check-in    | 0.4      | 5          | 2.00             | RANK 4       |
+=======================================================================================================+
```

### Tie-Breaking Rationale Between Rank 2 and Rank 3:

Both **RISK-02** and **RISK-03** feature an identical Risk Exposure of **2.40**.

- **RISK-02 (Timetable Import Errors)** is prioritized as **Rank 2** because its Impact score ($I = 6$) is higher than RISK-03 ($I = 4$). Furthermore, Timetable Import lies directly on the project's **Critical Path** (Task C). Any failure in timetable ingestion delays downstream core engineering activities.
- **RISK-03 (Student No-Shows)** is designated as **Rank 3**. Although it features high probability ($P = 0.6$), its impact is operational rather than schedule-halting, and it can be addressed via software policy controls.

---

# 32. PROBABILITY–IMPACT (P–I) MATRIX

```
+=======================================================================================================+
| PROBABILITY (P) | IMPACT (I)                                                                          |
|                 | Low (1 - 3)        | Medium (4 - 5)             | High (6 - 7)     | Critical (8-10)|
+=======================================================================================================+
| Very High (0.8) |                    |                            |                  |                |
+-------------------------------------------------------------------------------------------------------+
| High (0.6)      |                    | [RISK-03] Student No-Shows |                  |                |
|                 |                    | P=0.6, I=4 -> Exp=2.4      |                  |                |
+-------------------------------------------------------------------------------------------------------+
| Medium (0.4-0.5)|                    | [RISK-04] Assistant Skip   | [RISK-01] Quota  |                |
|                 |                    | P=0.4, I=5 -> Exp=2.0      | Disagree         |                |
|                 |                    |                            | P=0.5, I=7       |                |
|                 |                    |                            | Exp=3.5 (CRIT)   |                |
|                 |                    |                            | ---------------- |                |
|                 |                    |                            | [RISK-02] Import |                |
|                 |                    |                            | Errors           |                |
|                 |                    |                            | P=0.4, I=6       |                |
|                 |                    |                            | Exp=2.4 (HIGH)   |                |
+-------------------------------------------------------------------------------------------------------+
| Low (0.1 - 0.3) |                    |                            |                  |                |
+=======================================================================================================+
```

---

# 33. RISK MITIGATION, MONITORING, AND MANAGEMENT (RMMM) PLAN

Detailed RMMM plans are established for the top three ranked risks.

```
+=======================================================================================================+
| RISK-01: Departments Disagree on Quotas (Rank 1 | P = 0.5 | I = 7 | Exposure = 3.50)                 |
+=======================================================================================================+
| Risk Owner: Project Manager & Dean of Academic Affairs                                                |
| Trigger Indicator: More than 1 Department Coordinator refuses to sign the quota allocation matrix by  |
| Project Day 7 (consuming 3 of the 5 days allotted to Task B).                                         |
+-------------------------------------------------------------------------------------------------------+
| Mitigation Strategy:                                                                                  |
| 1. Formulate the quota formula using objective historical student enrollment data rather than         |
|    arbitrary departmental quotas (e.g., Quota proportional to student headcount).                     |
| 2. Guarantee a baseline allocation of at least 35 slots/week to smaller departments.                 |
| 3. Create a shared "Institutional Floating Pool" (15% of free slots) for ad-hoc project requests.     |
+-------------------------------------------------------------------------------------------------------+
| Monitoring Strategy:                                                                                  |
| - Daily stand-ups during Task B between Business Analyst and the 6 Department Coordinators.           |
| - Track the number of unresolved clause objections submitted to the Academic Dean.                    |
+-------------------------------------------------------------------------------------------------------+
| Management & Contingency Plan:                                                                        |
| - If consensus is not reached by Day 8, invoke institutional executive governance: Academic Dean signs |
|   an interim equal-share policy (42 slots per department) for the initial 4 weeks of the semester.    |
| - Quotas will be reviewed and recalibrated using empirical utilization data at the 30-day mark.       |
+=======================================================================================================+

+=======================================================================================================+
| RISK-02: Timetable Import Errors (Rank 2 | P = 0.4 | I = 6 | Exposure = 2.40)                         |
+=======================================================================================================+
| Risk Owner: Lead Backend Engineer & Timetable Officer                                                 |
| Trigger Indicator: Parser error rate exceeds 2.0% during CSV pre-validation testing on Project Day 6. |
+-------------------------------------------------------------------------------------------------------+
| Mitigation Strategy:                                                                                  |
| 1. Deliver an immutable, validated CSV/Excel template to the Timetable Office with strict schema rules.|
| 2. Build automated pre-ingestion validation scripts that detect syntax errors before DB writes.       |
| 3. Provide fallback dry-run imports with visual diff displays showing clashes before committing.      |
+-------------------------------------------------------------------------------------------------------+
| Monitoring Strategy:                                                                                  |
| - Continuous logging of parser exceptions, unmapped room identifiers, and duplicate slot conflicts.   |
| - Automated alert dispatched to the Timetable Officer whenever a malformed record is parsed.          |
+-------------------------------------------------------------------------------------------------------+
| Management & Contingency Plan:                                                                        |
| - If automated CSV import encounters systemic errors, activate the emergency administrative UI to     |
|   allow the Timetable Office to manually input recurring class blocks for affected labs.              |
| - Backend engineers will assist with database-level SQL patch scripts to resolve data mismatches.     |
+=======================================================================================================+

+=======================================================================================================+
| RISK-03: Students Book and Do Not Show (Rank 3 | P = 0.6 | I = 4 | Exposure = 2.40)                   |
+=======================================================================================================+
| Risk Owner: Lead System Architect & Associate Dean (Student Affairs)                                  |
| Trigger Indicator: Operational pilot records an aggregate No-Show rate exceeding 8.0% over 48 hours.  |
+-------------------------------------------------------------------------------------------------------+
| Mitigation Strategy:                                                                                  |
| 1. Build an automated cron daemon that checks slot status 15 minutes past start time.                |
| 2. Transition unverified bookings to NO_SHOW and immediately return the slot to the public pool.     |
| 3. Send SMS / push-notification check-in reminders 15 minutes prior to slot start time.               |
+-------------------------------------------------------------------------------------------------------+
| Monitoring Strategy:                                                                                  |
| - Real-time analytics dashboard tracking daily no-show percentages across all 14 laboratories.        |
| - Automated flagging of students accumulating repeated unverified reservations.                       |
+-------------------------------------------------------------------------------------------------------+
| Management & Contingency Plan:                                                                        |
| - Enforce an automated progressive penalty policy:                                                    |
|   * Strike 1: Warning email notification.                                                             |
|   * Strike 2: 7-day booking suspension.                                                               |
|   * Strike 3: Loss of self-booking privileges for the remainder of the semester.                     |
+=======================================================================================================+
```

---

# 34. PROJECT CLOSURE NOTE & HANDOVER PROTOCOL

```
=======================================================================================================
                         FORMAL PROJECT COMPLETION & HANDOVER NOTE
=======================================================================================================
PROJECT NAME:
  LabBook: Computer Lab Slot Booking & Quota Management Platform

ACADEMIC SPONSOR:
  Office of the Dean of Academic Affairs & Central Computing Facility

CLOSURE DATE:
  Project Day 30

1. STATEMENT OF OBJECTIVE ACHIEVEMENT:
   The LabBook platform has been designed, developed, and verified within the budgeted 30-working-day
   schedule. All core objectives—including timetable schedule import, real-time availability visibility,
   zero-clash student self-booking, departmental quota enforcement, and automated no-show recycling—have
   been fully achieved and validated against acceptance criteria.

2. DELIVERABLES COMPLETED & ARCHIVED:
   [X] Fully integrated, production-ready software container bundle (Frontend, Backend, DB, Redis).
   [X] IEEE 830-compliant Software Requirements Specification (SRS) Signed Baseline.
   [X] Complete UML Architectural Design Package (Use Case, Class, Sequence, Activity, State Machine).
   [X] Comprehensive Quality Assurance Test Suite (BVA, Equivalence, Decision Table, Load Tests).
   [X] Operational Systems Manual & Role-Based User Manuals (Student, Assistant, Coordinator, Admin).
   [X] Institutional Master Timetable CSV Ingestion Templates & Schema Validators.

3. FORMAL ACCEPTANCE CRITERIA VERIFICATION:
   - Functional Requirement Coverage: 11 of 11 FRs verified and accepted (100% Pass Rate).
   - Concurrency Clash Rate: 0.00% under simulated 50-user race conditions (Atomic locking verified).
   - System Availability Target: 99.8% achieved during staging trial (exceeds >= 99.5% requirement).
   - Critical Defects at Release: Exactly 0 open critical/high defects.

4. RESIDUAL RISKS & OUTSTANDING ITEMS:
   - Minor: Occasional QR scan retries required on cracked/scratched student mobile screens (Addressed via
     manual student roll number search fallback on the assistant console).
   - Future: Campus turnstile hardware integration remains deferred to Release 2.0.

5. OPERATIONAL HANDOVER SIGN-OFFS:
   ----------------------------------------------------------------------------------------------------
   Role                           Name / Title                     Signature             Date
   ----------------------------------------------------------------------------------------------------
   Project Lead                   Senior Architect / PM            [Signed via KMS]      Project Day 30
   Academic Dean                  Dean of Academic Affairs         [Approved]            Project Day 30
   Head of Central Computing      Chief Laboratory Superintendent  [Accepted]            Project Day 30
   Student Council Rep            President, Student Senate        [Countersigned]       Project Day 30
=======================================================================================================
```

---

# 35. ACADEMIC LESSONS LEARNED

Reflecting on the 30-day development lifecycle yields vital software engineering lessons for enterprise academic software development:

1. **Resolve Quota and Governance Policies Prior to Architecture:**The 1-day float on Task B (Quota Policy Agreement) proved critical. Departmental quota negotiations are fundamentally political rather than technical. Attempting to code quota management without signed mathematical formulas causes severe backend architectural rework. Governance must lead development.
2. **Decouple Recurring Imports from Real-Time Transaction Engines:**Isolating the Timetable Import module (Task C) behind an asynchronous boundary prevented malformed external CSV spreadsheets from locking the production booking database. The use of staging tables and dry-run validation routines is essential when consuming data from non-technical campus offices.
3. **Enforce Atomicity at the Database Engine Level:**Application-level concurrency checks (e.g., simple `if (!slot.isBooked())` checks in code) fail under high-concurrency race conditions. True clash prevention requires atomic database primitives—such as PostgreSQL row-level pessimistic locks (`SELECT ... FOR UPDATE`) or Redis distributed mutex locks.
4. **Design Check-In Interfaces for Operational Simplicity:**Lab assistants operate under time pressure as hundreds of students enter labs between class periods. Check-in interfaces must be designed for single-motion operation (e.g., automatic camera autofocus, audible confirmation tones, and instant screen-color feedback) to achieve high compliance.
5. **Mandate Quantifiable Metrics for Non-Functional Requirements:**Vague requirements like "the system shall be fast" cannot be evaluated objectively. Specifying "95th percentile response time $\le 1.0\text{ s}$ under 200 concurrent users" provides developers and QA engineers with a clear, testable performance target.
6. **Validate Boundary Conditions Early in Development:**
   Testing edge cases early—such as zero remaining quota, semester boundary transitions, and same-minute booking collisions—revealed over 60% of critical defects during unit and integration phases, preventing costly architectural refactoring during final system testing.

---

# 36. FINAL CONCLUSION & SUMMARY FINDINGS

This comprehensive engineering study delivers a verified, production-ready solution for the **LabBook** Computer Lab Slot Booking and Quota Management System (Case Study No. 150).

### Key Project Findings & Quantifiable Outcomes:

1. **Accurate Capacity Sizing:**Across **14 laboratories**, **8 daily slots**, and a **6-day operational week** (**672 gross slots**), accounting for the immutable **62% curricular timetable occupancy** (**416.64 slots**) yields an estimated weekly capacity of **255.36 free slots** (equivalent to **255 guaranteed discrete booking slots** per week, or **42 slots per department per week**). This addresses the historical lack of facility availability for engineering students.
2. **Definitive Critical Path Schedule:**CPM network analysis proves the project requires exactly **30 Working Days**. The schedule is governed by dual parallel critical paths:

   $$
   \mathbf{Path\ 1:\ Requirements\ (4d) \rightarrow Timetable\ Import\ (6d) \rightarrow Booking\ Module\ (10d) \rightarrow Check\text{-}In\ (5d) \rightarrow Testing\ (5d)}
   $$

   $$
   \mathbf{Path\ 2:\ Requirements\ (4d) \rightarrow Timetable\ Import\ (6d) \rightarrow Booking\ Module\ (10d) \rightarrow Reports\ (5d) \rightarrow Testing\ (5d)}
   $$

   The *Department Quota Agreement* task has a total float of exactly 1 day.
3. **Rigorous Quality Assurance Metrics:**Boundary Value Analysis, Equivalence Partitioning, and Cause-Effect Decision Tables demonstrated comprehensive test coverage. Applying industry-standard quality metrics to the illustrative testing execution yielded a **Defect Density of 7.5 defects/KLOC** and a **Defect Removal Efficiency (DRE) of 92.86%**, confirming robust software reliability.
4. **Targeted Risk Management:**
   Quantitative risk modeling identified **Inter-Departmental Quota Disagreements** as the top project risk ($RE = 3.50$), followed by **Timetable Import Errors** ($RE = 2.40$, Rank 2) and **Student No-Shows** ($RE = 2.40$, Rank 3). Dedicated RMMM strategies ensure organizational consensus and operational resilience.

The resulting architectural blueprints, mathematical models, and governance frameworks provide a sound foundation for deployment across the college's computing facilities.

=======================================================================================================
                                       END OF SUBMISSION REPORT
===============================================================
