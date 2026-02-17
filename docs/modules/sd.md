# SD — Sales & Distribution

The Sales & Distribution module provides conversational access to sales orders, deliveries, billing, pricing, returns, credit management, and sales analytics (O2C).

## Scope

| Area | Read | Create | Modify |
|------|:----:|:------:|:------:|
| Sales Orders | :white_check_mark: | :white_check_mark: | :white_check_mark: |
| Outbound Delivery | :white_check_mark: | :white_check_mark: | :white_check_mark: |
| Billing | :white_check_mark: | :white_check_mark: | — |
| Pricing (conditions) | :white_check_mark: | — | — |
| Returns | :white_check_mark: | :white_check_mark: | — |
| Credit Management | :white_check_mark: | — | — |
| Reporting SD (sales analysis, OTIF) | :white_check_mark: | — | — |

---

## Use Cases

### Sales Order — Create

!!! example "User prompt"
    *"Create a sales order type OR for customer 200100, sales organization 1000, channel 10, material PROD-001, quantity 50, delivery date +7 days, company code 1000."*

**What happens:** Creates the sales order with the given header and item data; determines pricing and returns the SO number.

---

### Sales Order — Change (quantity / delivery date)

!!! example "User prompt"
    *"Change sales order 5000001234: change quantity of item 10 to 75 units and update delivery date to +14 days."*

**What happens:** Updates the specified item quantity and requested delivery date; saves and returns the updated SO.

---

### Sales Order — Display with document flow

!!! example "User prompt"
    *"Show sales order 5000001234 with all line items, prices, and complete document flow (deliveries, invoices, associated payments)."*

**What happens:** Retrieves SO header and items, prices, and full document flow (deliveries, billing documents, payments).

---

### Sales Order — Open orders by customer (backlog)

!!! example "User prompt"
    *"List all open sales orders for customer 200100 in sales org 1000. Sort by date and indicate the value still to deliver."*

**What happens:** Returns open sales orders for the customer and sales org; sorted by date with value still to deliver; aligns with VA05.

---

### Delivery — Create from sales order

!!! example "User prompt"
    *"Create outbound delivery for sales order 5000001234, item 10, full quantity. Shipping point 1100."*

**What happens:** Creates the outbound delivery with reference to the SO item; quantities and shipping point as specified. Depends on released SO and stock/ATP.

---

### Delivery — Post goods issue

!!! example "User prompt"
    *"Post goods issue for delivery 8000001234. Confirm that stock is reduced in warehouse 0001 of plant 1100."*

**What happens:** Posts goods issue for the delivery; reduces stock in the given warehouse and confirms the material document.

---

### Delivery — Pending GI today

!!! example "User prompt"
    *"Lista todas las entregas con salida de mercancías planificada para hoy en centro expedición 1100 que aún no tienen GI posteado. Muestra cliente, material y cantidad."*

**What happens:** Returns deliveries with planned GI date = today and GI not yet posted for the shipping point; includes customer, material, quantity. Supports daily operations (VL06O).

---

### Billing — Create from delivery

!!! example "User prompt"
    *"Create invoice for delivery 8000001234. Verify that the price matches the sales order and show the billing document created."*

**What happens:** Creates the billing document from the delivery; checks price against SO and returns the billing document number; creates FI document.

---

### Billing — Pending to bill (work list)

!!! example "User prompt"
    *"Show all deliveries with GI completed that do not yet have an invoice created for sales org 1000. Group by customer and indicate total value pending billing."*

**What happens:** Returns deliveries with GI completed but not yet billed; grouped by customer with total value pending billing. Reduces revenue leakage (VF04).

---

### Pricing — Display conditions

!!! example "User prompt"
    *"Show active price conditions for material PROD-001, sales org 1000, channel 10: base price (PR00), discounts, surcharges, and resulting net price."*

**What happens:** Returns active price conditions for the material/sales org/channel: base price, discounts, surcharges, and net price. Validity dates as in VK13. Availability of condition types may depend on configuration.

---

### Returns — Create return order

!!! example "User prompt"
    *"Create a return order type RE for customer 200100 with reference to invoice 9000001234, material PROD-001, quantity 5 units. Reason: defective material."*

**What happens:** Creates the return order (RE) with reference to the billing document; item and quantity as specified; reason code stored. Original invoice must exist.

---

### Credit Management — Credit situation

!!! example "User prompt"
    *"Show the credit situation for customer 200100: credit limit, current exposure (open orders + deliveries + open invoices), available, and % utilization."*

**What happens:** Returns credit limit, current exposure (open SO + deliveries + open invoices), available credit, and utilization %; aligns with FD32/UKM.

---

### Credit Management — Credit blocked orders

!!! example "User prompt"
    *"List all sales orders blocked by credit check in sales org 1000. Show customer, amount, % of limit, and how long they have been blocked."*

**What happens:** Returns sales orders blocked by credit check; shows customer, amount, % of limit, and how long they have been blocked. Supports cash flow and release decisions (VKM1).

---

### Reporting SD — Sales analysis

!!! example "User prompt"
    *"Generate a sales analysis for sales org 1000 in Q1 2026: top 10 materials by revenue, top 10 customers by volume, and monthly revenue trend."*

**What happens:** Returns sales analysis: top 10 materials by revenue, top 10 customers by volume, and monthly revenue trend; totals align with standard reports (VF05).

---

### Reporting SD — OTIF (On Time In Full)

!!! example "User prompt"
    *"Calculate OTIF (On Time In Full) for shipping point 1100 in January 2026. Break down by: % on-time deliveries, % complete deliveries, and combined OTIF %. Top 5 customers with worst OTIF."*

**What happens:** Calculates OTIF for the shipping point and period: % on time, % in full, combined OTIF; returns top 5 customers with worst OTIF for follow-up.

---

## OData Services Used

| Service | Description | SAP Version |
|---------|-------------|-------------|
| `API_SALES_ORDER_SRV` | Sales orders (incl. returns) | S/4 1909+ |
| `API_OUTBOUND_DELIVERY_SRV` | Outbound deliveries | S/4 1909+ |
| `API_BILLING_DOCUMENT_SRV` | Billing documents | S/4 1909+ |
| `API_CREDIT_MGMT_BUSINESS_PARTNER` | Credit management | S/4 1909+ |
| `BAPI_SALESORDER_CREATEFROMDAT2` / `BAPI_SALESORDER_CHANGE` / `BAPI_SALESORDER_GETLIST` | Sales order create/change/list (RFC) | ECC 6.0+ |

!!! note "Availability"
    Pricing condition records (VK13) may require custom or additional APIs; standard OData coverage varies by SAP version. Delivery and billing creation depend on released orders and stock/ATP.

---

## Limitations

!!! warning "Current Limitations"
    - Availability check (ATP) and pricing simulation depend on SAP configuration. Condition types and pricing transparency may be limited by exposed OData.
    - Delivery creation and goods issue depend on stock and physical process; returns and credit blocks follow your custom logic and release procedures.
