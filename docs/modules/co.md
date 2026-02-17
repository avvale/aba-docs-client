# CO — Controlling

The Controlling module provides natural language access to cost accounting, internal orders, profit centers, and period-close checks.

## Scope

| Area | Read | Create | Modify |
|------|:----:|:------:|:------:|
| Cost Center Accounting | :white_check_mark: | — | — |
| Internal Orders | :white_check_mark: | — | — |
| Profit Center Accounting | :white_check_mark: | — | — |
| CO postings (imputaciones) | :white_check_mark: | — | — |
| Period close (cierre CO) | :white_check_mark: | — | — |
| Profitability Analysis (CO-PA) | :white_check_mark: | — | — |
| Product Costing | :white_check_mark: | — | — |
| Activity-Based Costing | :material-clock-outline: | — | — |

---

## Use Cases

### Cost Center — Actual vs plan

!!! example "User prompt"
    *"Show actual vs plan comparison for cost center 4200 (Marketing) in company code 1000 for Q1 2026. Break down by cost element and show absolute and percentage variance."*

**What happens:** Returns actual vs. plan by cost element with absolute and percentage variance; values align with KSB1.

---

### Cost Center — Top N by variance

!!! example "User prompt"
    *"List the 10 cost centers with highest negative variance (actual expense > plan) in company code 1000 for fiscal year 2026 YTD. Include % variance and responsible."*

**What happens:** Returns ranking of cost centers by negative variance with percentage and responsible; supports analysis and early warning.

---

### Internal Order — Status

!!! example "User prompt"
    *"Show the status of internal order 800100 in company code 1000: approved budget, committed, actual, and available. Indicate if there is risk of overrun."*

**What happens:** Returns budget, committed, actual, and available; flags risk of overrun; values align with KOB1.

---

### Internal Order — Budget consumption alerts

!!! example "User prompt"
    *"List all internal orders in company code 1000 with budget consumption above 90%. Show order, description, responsible, % consumption, and remaining available."*

**What happens:** Returns internal orders with budget consumption above 90% and remaining available; supports early warning.

---

### Profit Center — P&L

!!! example "User prompt"
    *"Generate the result (revenue - expenses) for profit center PC-1000 (North Division) in company code 1000 for fiscal year 2026 YTD. Compare with same period previous year."*

**What happens:** Returns P&L for the profit center and year-over-year comparison; totals align with standard reports.

---

### Profit Center — Benchmark

!!! example "User prompt"
    *"Compare profit centers PC-1000, PC-2000, and PC-3000 in company code 1000 by: total revenue, operating margin, and expense/revenue ratio for fiscal year 2026."*

**What happens:** Returns comparative table with revenues, operating margin, and expense/revenue ratio per profit center.

---

### CO postings — Cost center line items

!!! example "User prompt"
    *"Show the last 20 postings to cost center 4200 in company code 1000 for the current month. Include cost element, amount, text, and source document."*

**What happens:** Returns recent postings to the cost center; line items match KSB1.

---

### Period close — Checklist

!!! example "User prompt"
    *"Verify the CO period close status for company code 1000 period 01/2026: were cost center allocations executed? Order settlement? CO period close? List pending tasks."*

**What happens:** Returns status of CO period-close steps (allocations, order settlement, period close) and pending tasks.

---

### Profitability Analysis (CO-PA)

!!! example "User prompt"
    *"What's the contribution margin by product group for this quarter?"*

**What happens:** Pulls CO-PA data by product group and period and returns contribution margin summary. Depends on operating concern configuration.

---

### Product Costing

!!! example "User prompt"
    *"Show the standard cost estimate for material 100-200 in plant 1000"*

**What happens:** Retrieves the cost estimate (material, labor, overhead) for the product and plant.

---

## OData Services Used

| Service | Description | SAP Version |
|---------|-------------|-------------|
| `API_COSTCENTERACTUALDATA_SRV` | Cost center actuals | S/4 1909+ |
| `API_COSTCENTERPLANDATA_SRV` | Cost center plan | S/4 1909+ |
| `API_INTERNALORDER_SRV` | Internal orders | S/4 1909+ |
| `API_PROFITCENTERACTUALDATA_SRV` | Profit center actuals | S/4 1909+ |
| `BAPI_COSTCENTER_GETACTUALDATA` / `BAPI_INTERNALORDER_GETDETAIL` | Cost center / internal order (RFC) | ECC 6.0+ |

---

## Limitations

!!! warning "Current Limitations"
    - Read-only access. CO-PA depends on operating concern configuration. Plan/budget data depends on planning runs in SAP.
    - Period-close verification is informational; execution of close steps remains in SAP. Statistical key figures support is in development.
