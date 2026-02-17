# Master Data Access

ABA provides natural language access to SAP master data across all supported modules.

## Supported Master Data

| Master Data | Module | Example Query |
|-------------|--------|---------------|
| **G/L Accounts** | FI | *"Show chart of accounts for company code 1000"* |
| **Cost Centers** | CO | *"List all cost centers in controlling area 1000"* |
| **Materials** | MM | *"Details for material 100-200 including all views"* |
| **Vendors** | MM | *"Find vendor Siemens — show address and payment terms"* |
| **Customers** | SD | *"Customer master for Acme Corp — credit limit and sales area"* |
| **Equipment** | PM | *"Show equipment PUMP-001 with technical details"* |
| **Employees** | HCM | *"Employee details for personnel number 10023"* |
| **Work Centers** | PP | *"Capacity details for work center ASSEMBLY-01"* |

## Smart Matching

ABA supports **fuzzy matching** for master data lookups. Users don't need to know exact IDs:

- *"Siemens"* → Matches vendor `100234 — Siemens AG`
- *"pump"* → Matches equipment containing "pump" in description
- *"John"* → Matches employees with "John" in name fields

!!! tip "Tip"
    For ambiguous matches, ABA will ask for clarification: *"I found 3 vendors matching 'Siemens'. Did you mean Siemens AG (100234), Siemens Energy (100567), or Siemens Healthineers (100891)?"*
