# Supported Modules

ABA provides coverage across SAP's core modules. Each module exposes a set of MCP tools that the AI uses to serve user requests.

## Module Status

| Module | Name | Read | Create | Modify | Maturity |
|--------|------|:----:|:------:|:------:|----------|
| **FI** | Financial Accounting | :white_check_mark: | :white_check_mark: | :white_check_mark: | :green_circle: Production |
| **CO** | Controlling | :white_check_mark: | — | — | :green_circle: Production |
| **MM** | Materials Management | :white_check_mark: | :white_check_mark: | :white_check_mark: | :green_circle: Production |
| **SD** | Sales & Distribution | :white_check_mark: | :white_check_mark: | :white_check_mark: | :green_circle: Production |
| **PP** | Production Planning | :white_check_mark: | — | — | :yellow_circle: Beta |
| **HCM** | Human Capital Mgmt | :white_check_mark: | :white_check_mark: | — | :green_circle: Production |
| **PM** | Plant Maintenance | :white_check_mark: | :white_check_mark: | — | :green_circle: Production |
| **QM** | Quality Management | :white_check_mark: | — | — | :orange_circle: Alpha |
| **WM/EWM** | Warehouse Mgmt | :white_check_mark: | — | — | :orange_circle: Alpha |

!!! note "Maturity Levels"
    - :green_circle: **Production** — Tested and validated in customer environments
    - :yellow_circle: **Beta** — Functional, being refined with real-world usage
    - :orange_circle: **Alpha** — Core read operations available, expanding coverage

## The OData Principle

ABA works with **any standard SAP OData service**. This means:

- If SAP exposes it via OData → ABA can use it
- Adding a new module = activating OData services + creating MCP tool definitions
- No custom ABAP development required for read operations
- The platform grows with your SAP landscape

For details on each module, see the [SAP Modules](../modules/index.md) section.
