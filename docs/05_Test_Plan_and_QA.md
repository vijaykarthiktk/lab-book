# COMPREHENSIVE TEST PLAN & QUALITY ASSURANCE SPECIFICATION
## LabBook: Computer Lab Slot Booking & Quota Management System

---

## 1. QUALITY ASSURANCE STRATEGY

The verification strategy follows a multi-tier testing pyramid:
1. **Unit Testing:** JUnit 5 / Jest tests verifying isolated business rules (quota logic, parser utilities, token generation). Target coverage: $\ge 85\%$.
2. **Integration Testing:** Verification of transaction boundaries between `BookingService`, `QuotaService`, `PostgreSQL`, and `Redis`.
3. **System Testing:** End-to-end verification covering all 11 Functional Requirements and 6 Non-Functional Requirements.
4. **Acceptance Testing (UAT):** Stakeholder sign-off drills involving sample Students, Lab Assistants, and Department Coordinators.
5. **Regression Testing:** Automated CI pipeline execution on every pull request to protect established functionality.
6. **Performance & Stress Testing:** JMeter concurrency suites simulating 200 concurrent active users and 50 simultaneous competing requests for a single slot.
7. **Security Testing:** OWASP ZAP and SonarQube automated audits checking for SQLi, XSS, BOLA, and JWT tampering.
8. **Usability Testing:** Evaluation with 20 representative users targeting a System Usability Scale (SUS) score $\ge 80$.

---

## 2. BOUNDARY VALUE ANALYSIS (BVA)

### 2.1 Department Weekly Quota Boundary Testing
*Design Assumption:* Based on Capacity Analysis, each department is allocated an integer quota of **42 slots per week**. Testing evaluates quota boundaries at $Min-1$, $Min$, $Min+1$, $Nominal$, $Max-1$, $Max$, and $Max+1$.

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

### 2.2 Slot Index & Advance Horizon Boundary Testing
*Design Assumption:* 8 slots per day (Indexed 1 to 8), single-slot booking quantum, 7-day advance booking window.

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

## 3. EQUIVALENCE PARTITIONING (EP)

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

## 4. CAUSE-EFFECT DECISION TABLE (QUOTA & CLASH RESOLUTION)

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

## 5. SOFTWARE QUALITY & DEFECT METRICS CALCULATIONS

> **Academic Evaluation Disclaimer:**  
> *"Illustrative test evidence used for metric calculation because an actual execution log was not supplied."*

### 5.1 Defect Density
$$\text{Defect Density} = \frac{\text{Confirmed Pre-Release Defects}}{\text{Software Size (KLOC)}} = \frac{39}{5.2} = \mathbf{7.50\ defects/KLOC}$$

### 5.2 Defect Removal Efficiency (DRE)
$$\text{DRE} = \left( \frac{D_{\text{before}}}{D_{\text{before}} + D_{\text{after}}} \right) \times 100\% = \left( \frac{39}{39 + 3} \right) \times 100\% = \left( \frac{39}{42} \right) \times 100\% = \mathbf{92.86\%}$$
