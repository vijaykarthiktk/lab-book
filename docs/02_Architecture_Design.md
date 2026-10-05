# SYSTEM ARCHITECTURE & SOFTWARE DESIGN SPECIFICATION
## LabBook: Computer Lab Slot Booking & Quota Management System
**Document Classification:** Software Architecture Document (SAD)  
**Architectural Pattern:** Layered Microservices & Hexagonal Ports/Adapters

---

## 1. ARCHITECTURAL OVERVIEW

LabBook is architected to deliver high concurrency, strict data consistency, and loose coupling between scheduling ingestion and operational booking transactions.

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

## 2. COMPONENT DECOUPLING & HIGH COHESION JUSTIFICATION

### 2.1 High Cohesion
- **`TimetableService`:** Focuses exclusively on ingesting, parsing, validating, and persisting semester-long curricular schedules. Contains zero booking transaction logic or UI state.
- **`QuotaService`:** Encapsulates departmental quota bucket evaluations, balance decrements, and weekly reset routines.
- **`BookingService`:** Manages slot state transitions and transactional concurrency locks.
- **`CheckInService`:** Handles physical attendance verification and audit timestamps.

### 2.2 Loose Coupling via Service Interfaces
The services communicate through well-defined, asynchronous programmatic interfaces and REST/gRPC contracts rather than direct database table sharing:
- When the `BookingService` processes a reservation, it calls `ITimetableService.isSlotOccupied(...)` and `IQuotaService.hasSufficientQuota(...)`. 
- If the Timetable Office changes its schedule format (e.g., migrating from CSV to an automated ERP API), only the internal parsing implementation of `TimetableService` requires updating. The `BookingService`, database schemas, and student-facing UI remain completely unaffected.

### 2.3 Isolation of Booking from Timetable Ingestion
The semester timetable ingestion occurs as an offline batch process prior to active booking windows. Curricular entries are translated into immutable baseline reservation records. The operational booking engine queries these records using read-only projections, preventing timetable import scripts from degrading booking performance.

---

## 3. UML DESIGN PACKAGE MAPPING

The system's structural and behavioral dynamics are formally captured in the accompanying PlantUML source specifications located in the `/uml/` directory:
1. **Use Case Diagram:** [`uml/use_case_diagram.puml`](file:///Users/vijaykarthik/Programming/sem-5/sprint-1/lab-book/uml/use_case_diagram.puml)
2. **Domain Class Diagram:** [`uml/class_diagram.puml`](file:///Users/vijaykarthik/Programming/sem-5/sprint-1/lab-book/uml/class_diagram.puml)
3. **Sequence Diagram:** [`uml/sequence_diagram.puml`](file:///Users/vijaykarthik/Programming/sem-5/sprint-1/lab-book/uml/sequence_diagram.puml)
4. **Activity Diagram:** [`uml/activity_diagram.puml`](file:///Users/vijaykarthik/Programming/sem-5/sprint-1/lab-book/uml/activity_diagram.puml)
5. **State Machine Diagram:** [`uml/state_diagram.puml`](file:///Users/vijaykarthik/Programming/sem-5/sprint-1/lab-book/uml/state_diagram.puml)
