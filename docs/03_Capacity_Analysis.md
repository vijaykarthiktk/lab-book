# CAPACITY ANALYSIS & FREE-SLOT ESTIMATION REPORT
## LabBook: Computer Lab Slot Booking & Quota Management System

---

## 1. CAPACITY DERIVATION STEP-BY-STEP

All calculations use strictly the institutional figures provided without alteration:
- Number of Computer Laboratories ($N_{labs}$) = **14 labs**
- Daily Operating Slots ($S_{day}$) = **8 slots/day**
- Operational Days per Academic Week ($D_{week}$) = **6 days/week** (Monday through Saturday)
- Curricular Timetable Occupancy Ratio ($R_{classes}$) = **62%** ($0.62$)

### Step 1: Gross Weekly Institutional Lab Slot Capacity ($C_{gross}$)
$$C_{gross} = N_{labs} \times S_{day} \times D_{week}$$
$$C_{gross} = 14 \times 8 \times 6$$
$$C_{gross} = 112 \times 6 = \mathbf{672\ total\ weekly\ slots}$$

### Step 2: Capacity Consumed by Timetabled Classes ($C_{timetabled}$)
$$C_{timetabled} = C_{gross} \times R_{classes}$$
$$C_{timetabled} = 672 \times 0.62 = \mathbf{416.64\ slots}$$

### Step 3: Net Weekly Free Slots Available for Booking ($C_{free}$)
$$C_{free} = C_{gross} - C_{timetabled}$$
$$C_{free} = 672 - 416.64 = \mathbf{255.36\ slots}$$

### Step 4: Free-Slot Percentage of Total Capacity ($P_{free}$)
$$P_{free} = 100\% - 62\% = \mathbf{38.0\%}$$
$$\text{Verification: } \frac{255.36}{672} \times 100\% = 38.0\%$$

```
+=======================================================================================+
| Capacity Metric                   | Mathematical Formula    | Resulting Value         |
+=======================================================================================+
| Gross Weekly Operating Slots      | 14 labs * 8 slots * 6 d | 672.00 slots            |
| Curricular Timetabled Usage (62%) | 672 slots * 0.62        | 416.64 slots            |
| Net Unallocated Free Slots        | 672 slots - 416.64      | 255.36 slots            |
| Departmental Share (6 Depts)      | 255.36 slots / 6 depts  | 42.56 slots/dept/week   |
| Facility Average (14 Labs)        | 255.36 slots / 14 labs  | 18.24 slots/lab/week    |
+=======================================================================================+
```

---

## 2. ROUNDING CONVENTION & DISCRETE OPERATIONAL CAPACITY

In physical laboratory scheduling, slots cannot be transacted in fractional increments. A lab slot is an indivisible scheduling quantum.
- The calculated figure of **255.36 slots** is a statistical aggregate expectation derived from applying an aggregate percentage ($62\%$) across an entire semester.
- **Conservative Integer Capacity:** Applying the floor function ($\lfloor 255.36 \rfloor$) yields **255 guaranteed discrete booking slots** per week across the 14 facilities.
- **Weekly Departmental Quota Ceiling:** Apportioned across the 6 academic departments, each department receives a baseline quota of:
  $$\lfloor 42.56 \rfloor = \mathbf{42\ slots/week}$$
  with the fractional remainder ($0.56 \times 6 \approx 3.36$ slots) held in an institutional buffer pool managed by the System Administrator for ad-hoc campus recruitment drives and emergency examinations.

---

## 3. WHY CAPACITY IS AN ESTIMATE AND NOT A RIGID GUARANTEE

The figure of 255.36 weekly slots represents an **operational baseline estimate**, not an immutable contractual guarantee, due to unavoidable operational variations:
1. **Mid-Semester Timetable Adjustments:** Accreditation audits, guest lectures, and faculty rescheduling alter room assignments dynamically.
2. **Preventative & Emergency Hardware Maintenance:** Workstation re-imaging, network switch upgrades, operating system patching, and HVAC failures periodically take entire laboratories offline.
3. **Institutional Holidays & Academic Breaks:** Gazetted holidays falling on weekdays compress the active operational week below 6 days.
4. **University Semester Examinations & Placements:** High-stakes certification exams (e.g., GATE, NPTEL) and multinational placement recruitment drives commandeer entire lab clusters for multi-day windows.
5. **Slot Granularity Mismatches:** Certain advanced laboratory sessions (e.g., Compiler Design, VLSI Simulation) require contiguous 2-slot or 3-slot blocks, fragmenting single isolated free slots and making them unviable for standard bookings.
