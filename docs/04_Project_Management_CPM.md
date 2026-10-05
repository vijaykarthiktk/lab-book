# PROJECT MANAGEMENT, CPM NETWORK ANALYSIS & SCHEDULING
## LabBook: Computer Lab Slot Booking & Quota Management System

---

## 1. GIVEN PROJECT TASKS & DEPENDENCY MATRIX

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

## 2. FORWARD PASS CALCULATIONS (EARLIEST TIMES)

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

## 3. BACKWARD PASS CALCULATIONS (LATEST TIMES)

- Formula: $LS_i = LF_i - \text{Duration}_i$
- For predecessor task $i$: $LF_i = \min(LS_{\text{successors}})$
- With project completion bounded at Day 30 ($LF(G) = 30$):

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

## 4. FLOAT CALCULATION & SLACK ANALYSIS

$$TF_i = LS_i - ES_i = LF_i - EF_i$$
$$FF_i = \min(ES_{\text{successors}}) - EF_i$$

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

---

## 5. CRITICAL PATH PROOF

A critical path has $TF = 0$ along its entire length. Evaluating all network paths:
- **Path 1:** $A \rightarrow B \rightarrow D \rightarrow E \rightarrow G = 4 + 5 + 10 + 5 + 5 = 29\text{ days}$
- **Path 2:** $A \rightarrow B \rightarrow D \rightarrow F \rightarrow G = 4 + 5 + 10 + 5 + 5 = 29\text{ days}$
- **Path 3:** $A \rightarrow C \rightarrow D \rightarrow E \rightarrow G = 4 + 6 + 10 + 5 + 5 = \mathbf{30\ days}$
- **Path 4:** $A \rightarrow C \rightarrow D \rightarrow F \rightarrow G = 4 + 6 + 10 + 5 + 5 = \mathbf{30\ days}$

**Mathematical Finding:** The project has **two concurrent Critical Paths**:
$$\mathbf{Critical\ Path\ 1:\ A \rightarrow C \rightarrow D \rightarrow E \rightarrow G\ (30\ Days)}$$
$$\mathbf{Critical\ Path\ 2:\ A \rightarrow C \rightarrow D \rightarrow F \rightarrow G\ (30\ Days)}$$

---

## 6. PROJECT MILESTONES

- **M-01: Requirements Baseline Approved** (End of Day 4)
- **M-02: Quota Policy Agreement Ratified** (End of Day 9)
- **M-03: Curricular Timetable Ingested** (End of Day 10)
- **M-04: Booking Core Engine Integrated** (End of Day 20)
- **M-05: Subsystems Development Complete** (End of Day 25)
- **M-06: Quality Assurance Sign-Off Passed** (End of Day 30)
- **M-07: Operational Commissioning & Handover** (End of Day 30)
