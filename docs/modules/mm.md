# MM — Materials Management

The Materials Management module enables natural language access to procurement, inventory, and material master data across purchase requisitions, purchase orders, goods movements, invoicing, and analytics.

## Scope

| Area | Read | Create | Modify |
|------|:----:|:------:|:------:|
| Purchase Requisitions | :white_check_mark: | :white_check_mark: | :white_check_mark: |
| MRP / Planning | :white_check_mark: | — | — |
| Purchase Orders | :white_check_mark: | :white_check_mark: | — |
| PO Release | :white_check_mark: | — | :white_check_mark: |
| Goods Receipt | :white_check_mark: | :white_check_mark: | — |
| Service Entry Sheet | :white_check_mark: | :white_check_mark: | — |
| Supplier Invoice & Release | :white_check_mark: | :white_check_mark: | :white_check_mark: |
| Consignment Settlement | :white_check_mark: | :white_check_mark: | — |
| RFQ & Quotations | :white_check_mark: | :white_check_mark: | — |
| Purchase Info Record | :white_check_mark: | — | :white_check_mark: |
| Inventory (transfers, movements) | :white_check_mark: | :white_check_mark: | — |
| Physical Inventory | :white_check_mark: | :white_check_mark: | — |
| GR/IR & Procurement Analytics | :white_check_mark: | — | — |
| Vendor Master / Duplicate Check | :white_check_mark: | — | — |
| Subcontracting (e.g. provision 541) | :white_check_mark: | :white_check_mark: | — |
| Stock Transport Order (STO) | :white_check_mark: | :white_check_mark: | — |

---

## Use Cases

### Purchase Requisitions

!!! example "User prompt"
    *"Show me purchase requisition 10000133"*

**What happens:** Retrieves the purchase requisition with positions, material, quantity, delivery date, and organizational data.

---

### MRP / Planning

!!! example "User prompt"
    *"Run MRP for plant 1100. Return new proposed PRs for MAT-0001 (quantities/dates) and explain the cause (demand/stock/safety stock)."*

**What happens:** Runs MRP for the plant and returns proposed requisitions with quantities and dates, plus a short explanation (demand, stock, safety stock).

---

### Purchase Orders

!!! example "User prompt"
    *"Create a PO from PR 10001234 for vendor 100045, plant 1100. Check the proposed price from the info record if available."*

**What happens:** Creates a purchase order with reference to the PR, proposes price from the info record when available, and returns the new PO number.

---

### PO Release

!!! example "User prompt"
    *"Release PO 4500001234 if the amount is <= 25,000 and the plant is 1100. If not, indicate the required approval step."*

**What happens:** Releases the PO when conditions are met, or explains which approval step is required.

---

### Goods Receipt

!!! example "User prompt"
    *"Post goods receipt for PO 4500001234 for 30 units in storage location 0001. Then show the 20 remaining units pending delivery."*

**What happens:** Posts partial goods receipt for the PO and returns the remaining quantity to be received.

---

### Service Entry Sheet

!!! example "User prompt"
    *"Create a service entry sheet for PO 4500002233 for 8 hours of service SER-001 and charge to internal order IO-300001."*

**What happens:** Creates the service entry sheet with hours and allocation to the internal order.

---

### Supplier Invoice & Release

!!! example "User prompt"
    *"Post invoice for PO 4500001234 with unit price +12% vs PO and explain if it gets blocked due to tolerances."*

**What happens:** Posts the supplier invoice; if it blocks due to tolerance, the system explains the reason. Optionally: *"Release the blocked invoice for PO 4500001234 if the approver for plant 1100 has authorized it."*

---

### Consignment Settlement

!!! example "User prompt"
    *"Settle consignment for vendor 100045 for MAT-0001 in plant 1100. Show the resulting document and accounting entries."*

**What happens:** Runs consignment settlement and returns the resulting FI document and accounting entries.

---

### RFQ & Quotations

!!! example "User prompt"
    *"Create an RFQ for MAT-0001 (100 units) with delivery in 21 days and send it to vendors 100045, 100077, 100099. Return the RFQ number."*

**What happens:** Creates the RFQ with the position and sends it to the specified vendors; returns the RFQ number. For comparison: *"Compare quotations for the MAT-0001 RFQ weighting 70% price, 20% lead time, 10% payment terms. Recommend the winning vendor and justify."*

---

### Purchase Info Record

!!! example "User prompt"
    *"Update the info record for vendor 100045 / MAT-0001 with price 9.85 EUR effective today. Verify the new price is proposed when creating a new PO."*

**What happens:** Updates the purchasing info record with the new price and validity; confirms the price is proposed on new POs.

---

### Inventory (transfers, movements)

!!! example "User prompt"
    *"Post a 311 transfer of 15 units of MAT-0001 from storage location 0001 to 0002 in plant 1100 and confirm the final stock in both locations."*

**What happens:** Posts the internal transfer (311) and returns updated stock in both storage locations.

---

### Physical Inventory

!!! example "User prompt"
    *"Create a physical inventory document for MAT-0001 in plant 1100 storage location 0001, post count (count=95 vs stock=100) and post the differences."*

**What happens:** Creates the physical inventory document, posts the count, and posts the quantity difference.

---

### GR/IR & Procurement Analytics

!!! example "User prompt"
    *"Show GR/IR aging for vendor 100045 in company code 1000 with buckets 0-30/31-60/61-90/>90 and list the 10 oldest documents."*

**What happens:** Returns GR/IR aging by bucket and the top 10 oldest documents. Similarly: *"List open POs without GR in the last 30 days for plant 1100 and vendor 100045, sorted by amount — show top 20."*

---

### Vendor Master / Duplicate Check

!!! example "User prompt"
    *"Before creating a new vendor, check for duplicates by tax ID ESX1234567 and IBAN ES66XXXX. Return candidates and a recommendation."*

**What happens:** Searches for existing vendors by tax number and IBAN and returns candidates with a short recommendation.

---

### Subcontracting (e.g. provision 541)

!!! example "User prompt"
    *"Post 541 provision of 10 units of component COMP-001 to vendor 100045 for the subcontracting PO. Confirm special stock at vendor."*

**What happens:** Posts the 541 movement and confirms special stock at vendor.

---

### Stock Transport Order (STO)

!!! example "User prompt"
    *"Create an STO from plant 1100 to 1200 for MAT-0001 quantity 40. Post goods issue and goods receipt, and show in-transit stock and final stock at destination."*

**What happens:** Creates the STO, posts goods issue and goods receipt, and returns in-transit and destination stock.

---

## OData Services Used

| Service | Description | SAP Version |
|---------|-------------|-------------|
| `API_PURCHASEORDER_PROCESS_SRV` | Purchase orders | S/4 1909+ |
| `API_PURCHASEREQ_PROCESS_SRV` | Purchase requisitions | S/4 1909+ |
| `API_PRODUCT_SRV` | Material master | S/4 1909+ |
| `API_MATERIAL_STOCK_SRV` | Material stock | S/4 1909+ |
| `API_MATERIAL_DOCUMENT_SRV` | Material documents (GR/GI) | S/4 1909+ |
| `API_BUSINESS_PARTNER` | Vendor master | S/4 1909+ |

---

## Limitations

!!! warning "Current Limitations"
    - Batch and serial number tracking support is planned.
    - Collective PR release (ME55) and some workflow steps may have limited automation depending on customer setup.
    - Physical inventory and some movements depend on physical count and process configuration.
