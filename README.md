# LabBook: Computer Lab Slot Booking & Quota Management System
## B.Tech CSE Semester III — Case Study No. 150 (Complete Deliverables Repository)

---

## 📖 PROJECT OVERVIEW
**LabBook** is an enterprise-grade academic resource allocation platform built for an engineering college with **14 computer laboratories** shared among **6 academic departments**. It eliminates physical register clashes, enforces transparent departmental quotas, provides real-time availability visibility, enables student self-booking, supports lab assistant check-ins, reclaims unattended slots, and ingests master semester timetables.

---

## 📂 COMPLETE DELIVERABLES REPOSITORY STRUCTURE

```
/Users/vijaykarthik/Programming/sem-5/sprint-1/lab-book/
├── README.md                                  # Repository Index & Executive Overview
├── LabBook_SEPM_Case_Study_Report.md          # Complete 36-Section Master Submission Report
│
├── docs/                                      # Formal Engineering Specifications (Markdown)
│   ├── 01_SRS_IEEE830.md                      # Complete IEEE 830 SRS (11 FRs, 6 NFRs, MoSCoW)
│   ├── 02_Architecture_Design.md              # System Architecture & Component Design
│   ├── 03_Capacity_Analysis.md                # Mathematical Capacity Sizing & Calculations
│   ├── 04_Project_Management_CPM.md           # WBS, Network Analysis, Forward/Backward Passes
│   ├── 05_Test_Plan_and_QA.md                 # Test Strategy, BVA, EP, Decision Tables, Metrics
│   ├── 06_Risk_Management_RMMM.md             # Quantitative Risk Register & Detailed RMMM Plans
│   └── 07_Project_Closure_Note.md             # Formal Project Closure & Handover Protocol
│
├── uml/                                       # PlantUML Source Design Models (.puml)
│   ├── use_case_diagram.puml                  # Use Case Model (5 Actors, include/extend)
│   ├── class_diagram.puml                     # Domain Model Class Diagram with Multiplicities
│   ├── sequence_diagram.puml                  # "Book a Free Slot and Check In" Sequence Flow
│   ├── activity_diagram.puml                  # End-to-End Booking Activity Flow with Decisions
│   └── state_diagram.puml                     # Booking Lifecycle State Machine (7 States)
│
├── schedules/                                 # Project Scheduling & Resource Artifacts
│   ├── gantt_chart.mmd                        # Visual Gantt Chart in Mermaid Format
│   ├── cpm_schedule_table.csv                 # CPM Schedule (ES, EF, LS, LF, Float, Critical)
│   └── resource_allocation.csv                # Resource Allocation Table & RACI Matrix
│
├── testing/                                   # QA Test Suites & Execution Artifacts (.csv)
│   ├── test_cases.csv                         # Structured Test Cases (TC-01 through TC-11)
│   ├── traceability_matrix.csv                # Requirements Traceability Matrix (FR -> UC -> TC)
│   ├── boundary_value_analysis.csv            # Boundary Value Analysis Test Cases (Quota & Slots)
│   ├── equivalence_partitioning.csv           # Equivalence Classes (Valid & Invalid Partitions)
│   ├── decision_table.csv                     # Cause-Effect Decision Table (Quota + Clash Rules)
│   └── illustrative_defect_log.csv            # Pre-Release Confirmed Defect Execution Log
│
└── metrics/                                   # Quality, Monitoring & Risk Metric Spreadsheets (.csv)
    ├── defect_metrics.csv                     # Defect Density (7.5/KLOC) & DRE (92.86%)
    ├── monitoring_and_control.csv             # Operational Monitoring Metrics & Variances
    └── risk_register.csv                      # Quantitative Risk Ranking by Exposure (P x I)
```

---

## 📊 KEY PROJECT RESULTS SUMMARY

### 1. Capacity Derivation (Given: 14 Labs, 8 Slots/Day, 6 Days/Week, 62% Curricular)
- **Gross Weekly Institutional Capacity:** $14 \times 8 \times 6 = \mathbf{672\ total\ weekly\ slots}$
- **Curricular Class Consumption (62%):** $672 \times 0.62 = \mathbf{416.64\ slots}$
- **Net Weekly Free Slots Available:** $672 - 416.64 = \mathbf{255.36\ slots}$ ($\mathbf{38.0\%}$ free capacity)
- **Discrete Weekly Capacity:** **255 guaranteed integer booking slots** (or **42 slots/dept/week** across 6 departments with an institutional floating buffer pool of 3.36 slots).

### 2. CPM Network & Project Scheduling
- **Total Project Duration:** **30 Working Days**
- **Dual Parallel Critical Paths:**
  - $\mathbf{Path\ 1:}\ Requirements\ (4d) \rightarrow Timetable\ Import\ (6d) \rightarrow Booking\ (10d) \rightarrow Check\text{-}In\ (5d) \rightarrow Testing\ (5d) = \mathbf{30\ Days}$
  - $\mathbf{Path\ 2:}\ Requirements\ (4d) \rightarrow Timetable\ Import\ (6d) \rightarrow Booking\ (10d) \rightarrow Reports\ (5d) \rightarrow Testing\ (5d) = \mathbf{30\ Days}$
- **Non-Critical Activity:** *Quota policy agreement* (Task B) has **1 Day Total Float** and **1 Day Free Float**.

### 3. Quantitative Risk Exposure Ranking ($RE = P \times I$)
1. **Departments disagree on quotas:** $P = 0.5$, $I = 7$ $\rightarrow$ **Exposure = 3.50** (Rank 1)
2. **Timetable import errors:** $P = 0.4$, $I = 6$ $\rightarrow$ **Exposure = 2.40** (Rank 2 — Higher impact & Critical Path task)
3. **Students book and do not show:** $P = 0.6$, $I = 4$ $\rightarrow$ **Exposure = 2.40** (Rank 3 — Operational frequency)
4. **Lab assistants skip check-in:** $P = 0.4$, $I = 5$ $\rightarrow$ **Exposure = 2.00** (Rank 4)

### 4. Software Quality Metrics
- **Software Size Assumption:** 5.2 KLOC (5,200 logical source lines of code)
- **Defect Density:** $\frac{39\text{ defects}}{5.2\text{ KLOC}} = \mathbf{7.50\ defects/KLOC}$
- **Defect Removal Efficiency (DRE):** $\left( \frac{39}{39 + 3} \right) \times 100\% = \mathbf{92.86\%}$

---

## 🛠️ HOW TO VIEW AND COMPILE DELIVERABLES

### 1. Markdown Documentation
All `.md` files can be opened in any markdown viewer (VS Code, GitHub, or Obsidian) with full GitHub Flavored Markdown styling, mathematical equations (KaTeX / MathJax), and tabular formatting.

### 2. PlantUML Diagrams (`/uml/*.puml`)
Render the PlantUML diagrams using the PlantUML CLI or VS Code PlantUML extension:
```bash
# Render all diagrams to PNG
plantuml uml/*.puml

# Render all diagrams to SVG
plantuml -tsvg uml/*.puml
```

### 3. Mermaid Gantt Chart (`/schedules/*.mmd`)
Render using Mermaid CLI (`mmdc`):
```bash
mmdc -i schedules/gantt_chart.mmd -o schedules/gantt_chart.png
```

### 4. CSV Spreadsheets (`/schedules/*.csv`, `/testing/*.csv`, `/metrics/*.csv`)
All tabular data is stored in standard RFC 4180 CSV format and can be directly imported into Microsoft Excel, Google Sheets, LibreOffice Calc, Jira, or TestRail.
