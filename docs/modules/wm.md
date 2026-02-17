# WM/EWM — Warehouse Management

The Warehouse Management module provides natural language access to warehouse stock, transfer orders, and bin management.

## Scope

| Area | Read | Create | Modify |
|------|:----:|:------:|:------:|
| Warehouse Stock | :white_check_mark: | — | — |
| Transfer Orders | :white_check_mark: | — | — |
| Storage Bins | :white_check_mark: | — | — |
| Inbound/Outbound | :white_check_mark: | — | — |
| Wave Management | :material-clock-outline: | — | — |

---

## Use Cases

### Warehouse Stock

!!! example "User prompt"
    *"Where is material 100-200 stored in warehouse 01?"*

**What happens:** Queries warehouse stock by storage type and bin and returns available quantity.

---

### Transfer Orders

!!! example "User prompt"
    *"Show me all open transfer orders for warehouse 01"*

**What happens:** Lists pending transfer orders with source/destination bins and materials.

---

### Storage Bins / Utilization

!!! example "User prompt"
    *"What's the storage utilization for warehouse 01?"*

**What happens:** Returns occupied vs. available capacity by storage type.

---

### Inbound/Outbound

!!! example "User prompt"
    *"Pending putaway tasks for today"*

**What happens:** Queries open inbound/putaway tasks for the warehouse and date.

---

## OData Services Used

| Service | Description | SAP Version |
|---------|-------------|-------------|
| `API_WAREHOUSE_STOCK` | Warehouse stock | S/4 1909+ |
| WM Transfer order services | Transfer orders | ECC 6.0+ |

---

## Limitations

!!! warning "Current Limitations"
    - Read-only. EWM-specific features may require additional configuration. RF/scanner integration is out of scope.
