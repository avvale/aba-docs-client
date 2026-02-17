# Key Capabilities

ABA provides several interaction patterns across all supported SAP modules.

## Interaction Patterns

### :material-magnify: Query & Search

Find and filter business objects using natural language. ABA translates your intent into the appropriate OData filters.

!!! example "Examples"
    - *"Show me all purchase orders from last month over €50,000"*
    - *"Find all open sales orders for customer Acme Corp"*
    - *"Which maintenance notifications are overdue?"*

### :material-file-document-outline: Detail Lookup

Retrieve complete information about a specific business object, including related data and document flow.

!!! example "Examples"
    - *"Show me the details of production order 1000234"*
    - *"What are the line items on PO 4500012345?"*
    - *"Give me all the info on material 100-200"*

### :material-chart-bar: Analytics & Summary

Aggregate and summarize data across objects. ABA handles grouping and calculations.

!!! example "Examples"
    - *"What's the total spend by cost center for Q3?"*
    - *"Top 10 vendors by open balance"*
    - *"Average delivery time by material group this year"*

### :material-check-circle-outline: Status Check

Quick status queries that return concise answers.

!!! example "Examples"
    - *"Has delivery 80001234 been shipped?"*
    - *"Is invoice 5100000234 paid?"*
    - *"What's the status of production order 60001234?"*

### :material-link-variant: Cross-Module Navigation

Follow document flows across modules.

!!! example "Examples"
    - *"Show me the purchase order for this invoice"*
    - *"Trace the full document flow from sales order 100234"*
    - *"Which delivery note corresponds to this billing document?"*

### :material-pencil: Guided Write Operations

Perform controlled changes in SAP through guided conversations, always with business and technical validations.

!!! example "Examples"
    - *"Create a purchase requisition for 1,000 units of material 100-200 for plant 1000"*  
    - *"Move the requested delivery date of sales order 123456 to next Friday"*

### :material-translate: Multi-Language Support

Interact with ABA in the language that is most natural for your users.

!!! example "Examples"
    - *"Muéstrame las facturas abiertas del proveedor Siemens de este mes"*  
    - *"Mostra-mi tutti gli ordini di vendita aperti per il cliente ACME Italia"*  

## What's Coming

| Capability | Status | Description |
|-----------|--------|-------------|
| Read operations | :white_check_mark: Available | Query and retrieve SAP data |
| Cross-module queries | :white_check_mark: Available | Navigate between related documents |
| Write operations | :white_check_mark: Available | Create and modify selected SAP documents with validation |
| Workflow triggers | :material-clock-outline: Planned | Initiate approval workflows via conversation |
| Scheduled queries | :material-clock-outline: Planned | Set up recurring data checks with alerts |
| Multi-language | :white_check_mark: Available | Interact in any language supported by the underlying LLM; especially tested in **Spanish** and **English** |

