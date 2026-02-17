# Adding OData Services

One of ABA's key advantages is its extensibility. Adding support for a new SAP OData service follows a straightforward process.

## Prerequisites

Before adding a new service, ensure:

1. The OData service is **activated** in your SAP system (`/IWFND/MAINT_SERVICE`)
2. The service is **accessible** from the MCP Server network
3. You have the **service metadata** (entity sets, properties, navigation properties)
4. Appropriate **authorization objects** are configured for target users

## Step-by-Step Process

### 1. Discover the OData Service

Identify the service from:

- **SAP API Business Hub** ([api.sap.com](https://api.sap.com)) — Browse by module
- **SAP Gateway** (`/IWFND/MAINT_SERVICE`) — See activated services
- **Transaction SEGW** — Review custom OData services

### 2. Define the MCP Tool

Create a tool definition the LLM can understand:

```json
{
  "name": "mm_get_purchase_orders",
  "description": "Retrieve purchase orders from SAP. Filter by company code, vendor, date range, PO type, and amount.",
  "parameters": {
    "company_code": {
      "type": "string",
      "description": "SAP company code (e.g., '1000')"
    },
    "supplier": {
      "type": "string",
      "description": "Vendor number or name"
    },
    "date_from": {
      "type": "string",
      "description": "Start date (YYYY-MM-DD)"
    },
    "date_to": {
      "type": "string",
      "description": "End date (YYYY-MM-DD)"
    }
  }
}
```

### 3. Implement the Handler

The handler translates parameters into OData calls:

```
Input: { supplier: "Siemens", date_from: "2025-01-01" }
    ↓
OData: GET /API_PURCHASEORDER_PROCESS_SRV/A_PurchaseOrder
       ?$filter=SupplierName eq 'Siemens' 
        and CreationDate ge datetime'2025-01-01T00:00:00'
       &$top=50
    ↓
Output: Formatted purchase order list
```

### 4. Register and Test

1. Register the tool in MCP Server configuration
2. Test with sample queries
3. Verify authorization behavior with different user profiles

### 5. Document

Update the module documentation with the new capability.

## Best Practices

!!! tip "Guidelines"
    - **Start with read operations** — Safe and immediately valuable
    - **Use `$select`** — Request only needed fields for performance
    - **Implement pagination** — Use `$top` and `$skip` for large result sets
    - **Handle errors gracefully** — Translate SAP errors into user-friendly messages
    - **Test authorization** — Verify with restricted users, not just admin accounts

## Custom (Z) OData Services

The same process applies to custom services. Ensure they follow OData standards and are documented for the tool definition.

---

Need help? Contact the **Avvale AI team** for integration assistance.
