# Document Flow Tracking

ABA can trace the complete lifecycle of business documents across SAP modules.

## Supported Document Flows

### Procure-to-Pay (P2P)
```
Purchase Requisition → Purchase Order → Goods Receipt → Invoice → Payment
```

### Order-to-Cash (O2C)
```
Sales Order → Delivery → Goods Issue → Billing → Accounting Document → Payment
```

### Plan-to-Produce
```
Planned Order → Production Order → Confirmations → Goods Receipt
```

## Usage

!!! example "Examples"
    - *"Trace the document flow for PO 4500012345"*
    - *"What happened after goods receipt 5000067890?"*
    - *"Is sales order 100234 fully billed and paid?"*
    - *"Show the complete P2P flow for invoice 5100000234"*

ABA follows navigation properties in OData services to traverse related documents, presenting a complete picture of the business process.
