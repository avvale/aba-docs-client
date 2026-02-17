# What is ABA?

**ABA (Avvale Business Assistant)** is a conversational AI layer that sits on top of your existing SAP on-premise system, enabling users to interact with SAP data and processes using natural language.

## The Problem

SAP is powerful but complex. Business users often need to:

- Navigate multiple transactions to find simple information
- Learn SAP-specific terminology and transaction codes
- Depend on power users or IT for ad-hoc reports
- Wait for custom reports to be developed

## The Solution

ABA bridges the gap between business users and SAP data by providing a natural language interface:

!!! example "Before ABA"
    1. Open SAP GUI
    2. Navigate to transaction FBL1N
    3. Enter vendor number, company code, open items flag
    4. Execute and export to Excel
    5. Manually filter and sort results

!!! success "With ABA"
    Simply ask: *"Show me all open invoices from Siemens over €50,000"*

## Key Differentiators

### On-Premise First
ABA is designed specifically for **on-premise** environments. It works with your existing SAP ECC or S/4HANA on-premise installation, bringing modern conversational capabilities without requiring a move to full cloud.

### vs. Custom Chatbots
ABA uses the **Model Context Protocol (MCP)** standard, which means:

- No custom ABAP development required
- Modular tool architecture — add capabilities incrementally
- LLM-agnostic — switch between AI providers without rebuilding
- Built on SAP's standard OData services

### vs. SAP Analytics / Fiori
ABA doesn't replace reporting tools — it complements them by providing **quick, conversational access** to data that would otherwise require navigating complex screens or waiting for reports.

## Who Is It For?

| User | How They Use ABA | Example question |
|------|-----------------|------------------|
| **Finance Manager** | Quick balance checks, open item reviews, aging reports | *"AP aging for company code 1000 by bucket"* |
| **Procurement** | Purchase order status, vendor spend analysis | *"Open POs without GR for vendor 100045"* |
| **Plant Manager** | Maintenance notifications, order costs, KPIs | *"Overdue maintenance for plant 1100 with costs"* |
| **HR** | Employee data lookups, headcount, absences | *"Headcount by department with hires this month"* |
| **Management** | Cross-module KPIs and quick summaries | *"Compare profit centers by revenue and margin"* |
| **IT / Basis** | System queries, OData status, user activity | *"Which OData services are active in the gateway?"* |

## What ABA Is NOT

!!! warning "Important to understand"
    - **Not a replacement for SAP GUI** — Complex transactions and configurations still happen in SAP
    - **Not a reporting tool** — For scheduled, formatted reports use SAP's native tools
    - **Not write-only** — ABA supports both read and guided write operations (create/modify) for selected scenarios, always with SAP validations and authorizations
    - **Not magic** — It can only access data available through activated OData services with proper authorization
