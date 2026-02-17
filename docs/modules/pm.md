# PM — Plant Maintenance

The Plant Maintenance module enables natural language access to maintenance notifications, orders, confirmations, preventive plans, equipment, functional locations, spare parts, and PM analytics (costs, KPIs).

## Scope

| Area | Read | Create | Modify |
|------|:----:|:------:|:------:|
| Maintenance Notifications | :white_check_mark: | :white_check_mark: | — |
| Maintenance Orders | :white_check_mark: | :white_check_mark: | — |
| Confirmations (operation) | :white_check_mark: | :white_check_mark: | — |
| Maintenance Plans (preventive) | :white_check_mark: | — | — |
| Equipment | :white_check_mark: | — | — |
| Functional Locations | :white_check_mark: | — | — |
| Spare Parts (stock) | :white_check_mark: | — | — |
| Reporting PM (costs, KPIs) | :white_check_mark: | — | — |

---

## Use Cases

### Notification — Create (M2 breakdown)

!!! example "User prompt"
    *"Create a maintenance notification type M2 (breakdown) for equipment PUMP-001 in plant 1100. High priority. Symptom: excessive vibration. Text: 'Main pump with abnormal vibrations since morning shift'."*

**What happens:** Creates the maintenance notification with equipment, priority, symptom, and long text; returns the notification number.

---

### Notification — Display with equipment history

!!! example "User prompt"
    *"Show notification 10001234 and maintenance history for equipment PUMP-001: last 5 orders, cumulative costs, and mean time between failures."*

**What happens:** Retrieves the notification and equipment history: last 5 orders, cumulative costs, and MTBF; supports root-cause analysis.

---

### Notification — Open notifications by priority

!!! example "User prompt"
    *"List all open maintenance notifications for plant 1100 sorted by priority. Show equipment, creation date, responsible, and days open."*

**What happens:** Returns open notifications for the plant sorted by priority with equipment, creation date, responsible, and days open; aligns with IW28.

---

### Maintenance Order — Create corrective from notification

!!! example "User prompt"
    *"Create a maintenance order type PM01 (corrective) from notification 10001234 for equipment PUMP-001. Add operation: disassembly and inspection, 4 hours, work center MECH-01."*

**What happens:** Creates the corrective order with reference to the notification and adds the operation (work center, planned hours); returns the order number. Requires technical data (work center, etc.).

---

### Maintenance Order — Display with planned vs actual costs

!!! example "User prompt"
    *"Show order 4001234: status, operations, reserved materials, planned vs actual costs and hours worked. Indicate if there is overrun."*

**What happens:** Returns order header, operations, reserved materials, planned vs actual costs and hours; flags overrun; aligns with IW33.

---

### Maintenance Order — Open orders by functional location

!!! example "User prompt"
    *"List all open maintenance orders for functional location FL-PROD-LINE01 in plant 1100. Show type, priority, planned date, and responsible."*

**What happens:** Returns open maintenance orders for the functional location with type, priority, planned date, and responsible; supports planning (IW38).

---

### Confirmations — Confirm operation

!!! example "User prompt"
    *"Confirm operation 0010 of order 4001234: 3.5 hours worked, technician TECH-01, status: work completed. Include text: 'Bearing replaced, pump operational'."*

**What happens:** Posts the confirmation for the operation (hours, technician, completion status, text); updates order progress. Order must be released.

---

### Maintenance Plan — Display plan and next dates

!!! example "User prompt"
    *"Show maintenance plan PREV-PUMP-001: strategy, cycle, last execution, next planned date, and pending generated orders."*

**What happens:** Returns maintenance plan strategy, cycle, last execution, next due date, and pending generated orders. Availability may depend on exposed APIs (IP03).

---

### Maintenance Plan — Overdue preventive

!!! example "User prompt"
    *"List all maintenance plans with overdue execution in plant 1100. Show equipment, days overdue, last maintenance, and associated risk."*

**What happens:** Returns preventive plans with overdue execution; shows equipment, days overdue, last maintenance, and risk; supports safety and compliance (IP24).

---

### Equipment — Display with measurement values

!!! example "User prompt"
    *"Show equipment card for PUMP-001: general data, functional location, cost center, classification, and last 10 measurement point readings (vibration, temperature, pressure)."*

**What happens:** Returns equipment master and last 10 measurement readings (vibration, temperature, pressure, etc.); aligns with IE03. Measurement data depends on configuration.

---

### Equipment — Top N by failures (reliability)

!!! example "User prompt"
    *"List the 10 equipment with most breakdown notifications in plant 1100 during the last year. Show MTBF, total maintenance cost, and trend (improving/worsening)."*

**What happens:** Returns ranking by failure count with MTBF, total maintenance cost, and trend; supports reliability analysis.

---

### Reporting PM — Costs by functional location

!!! example "User prompt"
    *"Generate a maintenance cost report by functional location in plant 1100 for fiscal year 2026. Break down into labor, materials, and external services. Compare with previous year."*

**What happens:** Returns maintenance costs by functional location with breakdown (labor, material, external services) and year-over-year comparison; totals align with CO (MCJB).

---

### Reporting PM — KPIs (MTBF, MTTR, availability)

!!! example "User prompt"
    *"Calculate maintenance KPIs for plant 1100 in 2026: MTBF (mean time between failures), MTTR (mean time to repair), equipment availability, and preventive/corrective ratio."*

**What happens:** Returns MTBF, MTTR, availability, and preventive/corrective ratio for the plant and period; supports dashboard and management reporting.

---

### Functional Locations — Hierarchy

!!! example "User prompt"
    *"Show the hierarchy of functional location FL-PROD-LINE01 with all its sublevels and equipment installed at each level. Include relevant technical data."*

**What happens:** Returns functional location hierarchy with sublevels and installed equipment per level; aligns with IL03.

---

### Spare Parts — Critical spare availability

!!! example "User prompt"
    *"Check availability of spare part REP-BEAR-001 (pump bearing) in plant 1100: unrestricted stock, reserved, on order, and reorder point. Alert if below safety stock."*

**What happens:** Returns stock (unrestricted, reserved, on order), reorder point, and safety stock; alerts if below minimum. Critical for production continuity (MMBE).

---

## OData Services Used

| Service | Description | SAP Version |
|---------|-------------|-------------|
| `API_MAINTNOTIFICATION` | Maintenance notifications | S/4 1909+ |
| `API_MAINTENANCEORDER` | Maintenance orders | S/4 1909+ |
| `API_EQUIPMENT_SRV` | Equipment master | S/4 1909+ |
| `API_FUNCTIONAL_LOCATION` | Functional locations | S/4 1909+ |
| `API_MATERIAL_STOCK_SRV` | Material stock (spare parts) | S/4 1909+ |

!!! note "Availability"
    Maintenance plans (IP03/IP24) and confirmations (IW41) may require additional or custom APIs depending on your S/4 version. Measurement points and IoT integration depend on configuration.

---

## Limitations

!!! warning "Current Limitations"
    - Order and notification creation depend on master data (work centers, task lists) and released processes. Confirmation posting requires released orders.
    - Maintenance plan scheduling and overdue lists depend on exposed plan/strategy data. Measurement document and IoT sensor integration are on the roadmap.
