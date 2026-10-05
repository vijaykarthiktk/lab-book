# QUANTITATIVE RISK REGISTER & RMMM SPECIFICATION
## LabBook: Computer Lab Slot Booking & Quota Management System

---

## 1. RISK EXPOSURE FORMULA & RANKING

$$\mathbf{Risk\ Exposure\ (RE) = Probability\ (P) \times Impact\ (I)}$$

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

## 2. PROBABILITY–IMPACT (P–I) MATRIX

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

## 3. RMMM PLANS FOR TOP THREE RISKS

```
+=======================================================================================================+
| RISK-01: Departments Disagree on Quotas (Rank 1 | P = 0.5 | I = 7 | Exposure = 3.50)                 |
+=======================================================================================================+
| Risk Owner: Project Manager & Dean of Academic Affairs                                                |
| Trigger: More than 1 Department Coordinator objects to quota matrix by Project Day 7.                  |
| Mitigation: Baseline 35 slots/week to smaller departments; quota tied to enrollment; 15% floating pool.|
| Monitoring: Daily stand-up meetings during Task B; formal clause objection logging.                   |
| Contingency: Dean mandates interim equal quota (42 slots/dept) for 4 weeks; recalibrate at 30 days.    |
+=======================================================================================================+

+=======================================================================================================+
| RISK-02: Timetable Import Errors (Rank 2 | P = 0.4 | I = 6 | Exposure = 2.40)                         |
+=======================================================================================================+
| Risk Owner: Lead Backend Engineer & Timetable Officer                                                 |
| Trigger: Parser error rate exceeds 2.0% during CSV pre-validation testing on Project Day 6.           |
| Mitigation: Issue immutable template; build pre-ingestion regex validator; provide visual diff tool.  |
| Monitoring: Exception logging and alert notification on malformed row detection.                      |
| Contingency: Fallback to manual administrative schedule entry UI; backend SQL patch assistance.       |
+=======================================================================================================+

+=======================================================================================================+
| RISK-03: Students Book and Do Not Show (Rank 3 | P = 0.6 | I = 4 | Exposure = 2.40)                   |
+=======================================================================================================+
| Risk Owner: Lead System Architect & Associate Dean (Student Affairs)                                  |
| Trigger: Aggregate operational No-Show rate exceeds 8.0% over 48 hours.                               |
| Mitigation: 15-minute auto-release daemon; slot recycling; SMS/Push notification reminders at T-15m.  |
| Monitoring: Real-time analytics dashboard tracking daily no-show rates per facility.                  |
| Contingency: Progressive penalties (Warning -> 7-day suspension -> semester suspension).              |
+=======================================================================================================+
```
