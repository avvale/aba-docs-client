# SAP Modules Overview

ABA provides natural language access across SAP's core functional modules. Each module page details the available capabilities, OData services used, and example interactions.

## Module Coverage

| Module | Name | Read | Create | Modify | Maturity | Details |
|--------|------|:----:|:------:|:------:|----------|---------|
| **FI** | Financial Accounting | :white_check_mark: | :white_check_mark: | :white_check_mark: | :green_circle: Production | [View →](fi.md) |
| **CO** | Controlling | :white_check_mark: | — | — | :green_circle: Production | [View →](co.md) |
| **MM** | Materials Management | :white_check_mark: | :white_check_mark: | :white_check_mark: | :green_circle: Production | [View →](mm.md) |
| **SD** | Sales & Distribution | :white_check_mark: | :white_check_mark: | :white_check_mark: | :green_circle: Production | [View →](sd.md) |
| **PP** | Production Planning | :white_check_mark: | — | — | :yellow_circle: Beta | [View →](pp.md) |
| **HCM** | Human Capital Mgmt | :white_check_mark: | :white_check_mark: | — | :green_circle: Production | [View →](hcm.md) |
| **PM** | Plant Maintenance | :white_check_mark: | :white_check_mark: | — | :green_circle: Production | [View →](pm.md) |
| **QM** | Quality Management | :white_check_mark: | — | — | :orange_circle: Alpha | [View →](qm.md) |
| **WM** | Warehouse Management | :white_check_mark: | — | — | :orange_circle: Alpha | [View →](wm.md) |

## The OData Principle

ABA works with **any standard SAP OData service**:

- If SAP exposes it via OData → ABA can use it
- Adding a new module = activating OData services + creating MCP tool definitions
- No custom ABAP development required for read operations
- The platform grows with your SAP landscape

## Common Interaction Patterns

All modules support these interaction types:

| Pattern | Description | Example |
|---------|-------------|---------|
| **Query** | Find and filter objects | *"POs over €50k from last month"* |
| **Detail** | Look up specific objects | *"Details of order 1000234"* |
| **Summary** | Aggregate data | *"Total spend by cost center"* |
| **Status** | Quick checks | *"Is delivery 800123 shipped?"* |
| **Navigation** | Cross-document flow | *"PO for this invoice"* |

## Extending Coverage

Need a module not listed? ABA is designed for extensibility. See [Adding OData Services](../integration/adding-odata-services.md).
