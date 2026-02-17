# Prerequisites

Before deploying ABA, ensure the following requirements are met.

## SAP System Requirements

| Requirement | Details |
|-------------|---------|
| **SAP Version** | ECC 6.0 EHP7+ or S/4HANA 1909+ |
| **SAP Gateway** | Activated and accessible |
| **OData Services** | Required services activated per module |
| **ICM / Reverse Proxy** | HTTPS access to Gateway from MCP Server |
| **Authorization** | Users configured with appropriate auth objects |

## Network Requirements

| Connection | Protocol | Port |
|-----------|----------|------|
| Client → AI/LLM | HTTPS | 443 |
| AI/LLM → MCP Server | HTTPS | 443 (configurable) |
| MCP Server → SAP Gateway | HTTPS | 443 / 8443 |

!!! warning "Firewall Rules"
    The MCP Server must have network access to SAP Gateway endpoints. Work with your network team to open required ports.

## LLM Provider

ABA is designed to work **only with enterprise-grade LLM hosting**. In practice this means:

- **Trusted hyperscalers**:
  - **Anthropic Claude** via cloud providers (API key required)
  - **OpenAI** (latest GPT models, API key required)
  - **Azure OpenAI** (Azure subscription + deployment, for Azure-first and data residency needs)
- **SAP AI Core**:
  - SAP-managed models (including partner models such as Mistral) exposed via SAP BTP

When using these providers:

- Prompts and SAP data are processed over **encrypted channels**.
- Tenants share infrastructure but remain **logically isolated**.
- Data is **not used to train public models** and is **not exposed to other customers**.

> ABA does **not** rely on ad-hoc, unmanaged LLM endpoints. Any \"self-hosted\" models must run in customer-controlled infrastructure that meets the same security and compliance standards as the rest of the SAP landscape.

## MCP Server Infrastructure

| Resource | Minimum | Recommended |
|----------|---------|-------------|
| CPU | 2 cores | 4 cores |
| RAM | 4 GB | 8 GB |
| Storage | 10 GB | 20 GB |
| OS | Linux (Ubuntu 20.04+) | Ubuntu 22.04 LTS |
| Runtime | Node.js 18+ | Node.js 20 LTS |
