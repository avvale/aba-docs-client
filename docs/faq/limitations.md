# Known Limitations

Transparency about current limitations helps set the right expectations.

## Functional Limitations

| Limitation | Details | Workaround |
|-----------|---------|------------|
| **Write operations coverage** | Write (create/modify) is available for selected scenarios only | Use SAP GUI/Fiori for scenarios not yet covered; see module pages for current scope |
| **Complex reports** | Not a replacement for SAP reporting tools | Use for ad-hoc queries; formal reports via SAP |
| **Batch processing** | No mass data operations | Use SAP transactions for bulk changes |
| **Workflow execution** | Workflow triggers depend on customer workflow setup and approvals | Use SAP inbox/standard workflow apps when required; some triggers are planned |
| **File attachments** | Cannot read/create SAP attachments | Use SAP DMS or GOS |

## Technical Limitations

| Limitation | Details |
|-----------|---------|
| **OData dependency** | Can only access data exposed via activated OData services |
| **Query complexity** | Very complex joins across many tables may not be possible via OData |
| **Latency variability** | Response times depend on SAP load, network, and number of sequential tool calls |
| **Data volume** | Large result sets (>1000 records) may need to be filtered further |
| **Custom fields** | Z-fields require custom OData service extensions |
| **Deployment scope** | Standard deployment targets **SAP BTP**; other environments may require additional governance and integration work |
| **Cloud scope** | ABA is intentionally focused on **SAP on-premise** landscapes (ECC / S/4HANA on-premise) |

## AI/LLM Limitations

!!! warning "Important"
    - ABA interprets natural language, which can occasionally be ambiguous
    - Complex or poorly phrased queries may need clarification
    - The AI does not "learn" from your SAP data — it uses predefined tools
    - Calculations are performed by the AI, not by SAP; for financial calculations, always verify critical numbers
    - Language quality depends on the chosen model; ABA is primarily tested in **English and Spanish**
    - ABA connects only to **enterprise-grade LLM hosting** (trusted hyperscalers and/or **SAP AI Core**); model availability can vary by provider and region

## What's Being Addressed

See our [Roadmap](../changelog/roadmap.md) for planned improvements.
