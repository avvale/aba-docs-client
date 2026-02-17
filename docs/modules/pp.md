# PP — Production Planning

The Production Planning module enables natural language access to production orders, planned orders, BOMs, routings, and manufacturing status.

## Scope

| Area | Read | Create | Modify |
|------|:----:|:------:|:------:|
| Production Orders | :white_check_mark: | — | — |
| Planned Orders | :white_check_mark: | — | — |
| Bill of Materials (BOM) | :white_check_mark: | — | — |
| Routings / Work Centers | :white_check_mark: | — | — |
| MRP Results | :white_check_mark: | — | — |

---

## Use Cases

### Production Orders

!!! example "User prompt"
    *"What's the status of production order 60001234?"*

**What happens:** Retrieves order header with status, quantities (planned vs. confirmed vs. delivered), and schedule dates.

---

### Planned Orders

!!! example "User prompt"
    *"Show me all production orders in progress for plant 1000"*

**What happens:** Queries orders with status released or partially confirmed, optionally grouped by work center or line.

---

### Bill of Materials (BOM)

!!! example "User prompt"
    *"What components are needed for material FG-1000?"*

**What happens:** Retrieves the BOM for the finished good with components, quantities, and availability.

---

### Routings / Work Centers

!!! example "User prompt"
    *"What's the load on work center ASSEMBLY-01 for next week?"*

**What happens:** Queries planned capacity requirements vs. available capacity for the work center and period.

---

### MRP Results

!!! example "User prompt"
    *"Show planned orders for material FG-2000 this month"*

**What happens:** Retrieves MRP results (planned orders, shortage elements) for the material and period.

---

## OData Services Used

| Service | Description | SAP Version |
|---------|-------------|-------------|
| `API_PRODUCTION_ORDER_SRV` | Production orders | S/4 1909+ |
| `API_PLANNED_ORDER_SRV` | Planned orders | S/4 1909+ |
| `API_BILL_OF_MATERIAL_SRV` | BOMs | S/4 1909+ |
| `API_WORK_CENTERS_SRV` | Work centers | S/4 1909+ |

---

## Limitations

!!! warning "Current Limitations"
    - Read-only. Production order creation/release not available. MRP run execution is out of scope (read results only).
    - Process orders (PP-PI) support is planned.
