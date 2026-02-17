# QM — Quality Management

The Quality Management module provides natural language access to inspection lots, quality notifications, and quality-related data.

## Scope

| Area | Read | Create | Modify |
|------|:----:|:------:|:------:|
| Inspection Lots | :white_check_mark: | — | — |
| Quality Notifications | :white_check_mark: | — | — |
| Inspection Results | :white_check_mark: | — | — |
| Usage Decisions | :white_check_mark: | — | — |
| Certificates | :material-clock-outline: | — | — |

---

## Use Cases

### Inspection Lots

!!! example "User prompt"
    *"Show me all open inspection lots for plant 1000"*

**What happens:** Queries inspection lots without usage decision, showing material, batch, and inspection type.

---

### Quality Notifications

!!! example "User prompt"
    *"What quality notifications were raised this month for material group 001?"*

**What happens:** Retrieves quality notifications by date and material group, with defect codes and status.

---

### Inspection Results / Vendor Quality

!!! example "User prompt"
    *"What's the rejection rate for vendor Bosch in the last 6 months?"*

**What happens:** Analyzes inspection results for goods receipts from the vendor and calculates acceptance vs. rejection rates.

---

### Usage Decisions

!!! example "User prompt"
    *"List inspection lots pending usage decision"*

**What happens:** Returns inspection lots awaiting usage decision with key data.

---

## OData Services Used

| Service | Description | SAP Version |
|---------|-------------|-------------|
| `API_INSPECTIONLOT_SRV` | Inspection lots | S/4 1909+ |
| `API_QUALITYNOTIFICATION` | Quality notifications | S/4 1909+ |

---

## Limitations

!!! warning "Current Limitations"
    - Read-only. Inspection plan/catalog access is planned. SPC integration is on the roadmap.
