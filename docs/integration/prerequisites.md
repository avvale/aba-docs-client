# Technical Prerequisites

Detailed requirements for deploying ABA. This page is intended for IT, SAP Basis, and BTP administrators involved in the implementation.

For a business-oriented overview and project phases, see [Implementation Overview](../getting-started/implementation.md).

---

## SAP BTP Requirements

ABA runs as a Cloud Foundry application on SAP BTP. The following are required in the target BTP subaccount.

### Subaccount & Runtime

| Requirement | Details |
|-------------|---------|
| **Cloud Foundry environment** | Enabled in the BTP subaccount. A dedicated space is recommended (e.g. `aba-prod`). |
| **Cloud Foundry runtime** | Minimum 2 GB application runtime memory. Recommended: 4 GB for production workloads. |
| **Region** | Same region as the SAP Cloud Connector, or closest to the on-premise SAP system for minimal latency. |

### Required BTP Services

The following service instances must be created and bound to the ABA application:

| Service | Plan | Purpose |
|---------|------|---------|
| **SAP Connectivity Service** | `lite` | Provides the proxy for secure communication between BTP and the on-premise SAP system via Cloud Connector. |
| **SAP Destination Service** | `lite` | Manages the connection configuration (host, authentication method, proxy type) for the on-premise SAP endpoint. |
| **SAP Authorization & Trust Management (XSUAA)** | `application` | Handles OAuth2 token issuance and user authentication for the CF application. Required for Principal Propagation. |

### Optional BTP Services

| Service | Plan | When needed |
|---------|------|-------------|
| **SAP AI Core** | `standard` / `extended` | Only if using SAP-managed LLM models (e.g. Mistral via SAP AI Core) instead of a hyperscaler LLM directly. |
| **SAP Credential Store** | `standard` | Recommended for centralized secret management (API keys, OAuth secrets) instead of CF environment variables. |
| **SAP HTML5 Application Repository** | `app-host` | Only if deploying a custom web chat UI as a BTP-hosted frontend. |

---

## SAP On-Premise Requirements

### SAP System Version

| System | Minimum Version |
|--------|----------------|
| **SAP ECC** | 6.0 EHP7 or higher |
| **SAP S/4HANA On-Premise** | 1909 or higher |

### SAP Gateway

SAP Gateway must be **activated and accessible via HTTPS**. This is typically already the case if the customer uses SAP Fiori applications.

| Check | How |
|-------|-----|
| HTTPS service active | Transaction `SMICM` → Goto → Services → HTTPS must show status **Active** |
| Gateway hub reachable | `https://<sap-host>:<port>/sap/opu/odata/sap/` should return a service catalog or 401 (not connection refused) |

### OData Services

Required services must be activated per module via transaction `/IWFND/MAINT_SERVICE`. See individual [module pages](../modules/index.md) for the complete list of OData services per module.

!!! tip "Start small"
    Begin with one module (e.g., FI or MM). You can activate additional modules later without affecting the existing setup.

### User Authorizations

ABA respects the SAP authorization model. Each user accessing ABA is authenticated with their own SAP credentials (via Principal Propagation in production). Required:

- Authorization for the activated OData services (standard SAP Gateway authorization objects: `S_SERVICE`, `S_START`)
- Functional authorizations matching their role (same as for Fiori apps or SAP GUI access)
- **Email address configured in SU01** (required for Principal Propagation — the email must match the identity in the corporate IdP)

---

## SAP Cloud Connector

The SAP Cloud Connector (SCC) creates a secure tunnel between SAP BTP and the on-premise SAP system.

| Requirement | Details |
|-------------|---------|
| **Version** | 2.16 or higher. Recommended: latest stable (currently **2.18**). |
| **Connectivity** | Registered with the target BTP subaccount and showing **Connected** status. |
| **System mapping** | On-premise SAP Gateway configured as a backend system: Protocol = HTTPS, Principal Type = X.509 Certificate, "System Certificate for Logon" = checked. |
| **Exposed resources** | Path `/sap/opu/odata/sap/` with **Path and All Sub-Paths** access policy. |
| **Principal Propagation** | CA Certificate configured (signs short-lived user certificates). System Certificate configured (identifies SCC to the backend). Subject Pattern set to `CN=${email}`. |

!!! info "Existing Cloud Connector"
    If the customer already uses SAP Cloud Connector for Fiori apps or other BTP services, the existing installation can be reused. Only the system mapping and resource exposure for ABA's OData paths need to be added.

---

## Network Requirements

| Connection | Protocol / Port | Direction |
|-----------|----------------|-----------|
| End user → Chat client / LLM | HTTPS / 443 | Outbound from user's network |
| LLM → ABA (on BTP) | HTTPS / 443 | Inbound to BTP (managed by CF router) |
| ABA → SAP (via SCC) | HTTPS / 443 or 44300 | BTP → SCC tunnel → SAP Gateway |
| Cloud Connector → SAP BTP | HTTPS / 443 | Outbound from SCC host to BTP (tunnel initiation) |

!!! info "Firewall"
    The Cloud Connector initiates the tunnel to BTP (outbound), so **no inbound firewall rules** are required on the customer's on-premise network for BTP connectivity.

---

## LLM Provider

ABA is designed to work **only with enterprise-grade LLM hosting**:

| Provider | Requirements | Notes |
|----------|-------------|-------|
| **Anthropic Claude** | API key from Anthropic console | Recommended — strong tool use and reasoning. |
| **Azure OpenAI** | Azure subscription + model deployment | For Azure-first customers or EU data residency. |
| **OpenAI** | API key from OpenAI platform | Direct API. Latest GPT models. |
| **SAP AI Core** | SAP AI Core entitlement + model deployment | For customers standardizing on SAP BTP. Partner models (Mistral, etc.). |

In all cases: prompts and SAP data are processed over **encrypted channels**, are **not used for model training**, and remain **logically isolated** per tenant.

> ABA does **not** rely on ad-hoc, unmanaged LLM endpoints. Any "self-hosted" models must run in customer-controlled infrastructure that meets the same security and compliance standards as the rest of the SAP landscape.

---

## Identity & Authentication (Production)

For production deployments, ABA uses **Principal Propagation** to ensure every SAP data access is executed under the authenticated user's identity. This requires:

| Component | Requirement |
|-----------|-------------|
| **Corporate IdP** | Microsoft Entra ID (Azure AD) or equivalent, federated with SAP Cloud Identity Services (IAS). |
| **SAP IAS** | Configured as the trust broker between the corporate IdP and XSUAA on BTP. |
| **XSUAA** | Service instance (application plan) with token exchange configured. |
| **SAP Cloud Connector** | System Certificate and CA Certificate configured for X.509-based user propagation (see above). |
| **SAP ABAP (on-premise)** | ICM profile parameters for trusted reverse proxy and certificate verification, CERTRULE for certificate-to-user mapping, and trusted certificates imported in STRUST. See [Principal Propagation](principal_propagation.md) for full details. |

!!! warning "System restart required"
    The ICM profile parameters (`icm/trusted_reverse_proxy_0`, `icm/HTTPS/verify_client`) require a **full SAP system restart** to take effect. They cannot be applied dynamically via SMICM reload or RZ11. Plan this as a scheduled maintenance window.

!!! tip "PoC simplification"
    For the Proof of Concept phase, a simplified setup with a **technical service user** (basic authentication) can be used to accelerate initial validation. Principal Propagation is configured as part of the Pilot or Rollout phase.

---

## Summary Checklist

| # | Item | Phase |
|---|------|-------|
| 1 | SAP BTP subaccount with Cloud Foundry environment enabled | PoC |
| 2 | Cloud Foundry runtime entitlement (≥ 2 GB) | PoC |
| 3 | Connectivity Service instance (lite plan) | PoC |
| 4 | Destination Service instance (lite plan) | PoC |
| 5 | SAP Cloud Connector installed, connected, and configured with system mapping | PoC |
| 6 | SAP Gateway active with HTTPS enabled | PoC |
| 7 | OData services activated for target modules (`/IWFND/MAINT_SERVICE`) | PoC |
| 8 | Test SAP user(s) with appropriate authorizations | PoC |
| 9 | LLM provider API credentials (or SAP AI Core deployment) | PoC |
| 10 | XSUAA Service instance (application plan) | Prod |
| 11 | Principal Propagation configured (Entra ID → IAS → XSUAA → SCC → SAP) | Prod |
| 12 | SAP users with email in SU01 matching IdP identity | Prod |
| 13 | ICM profile parameters set and **system restarted** (RZ10) | Prod |
| 14 | Trusted certificates imported in STRUST (CA Certificate + System Certificate) | Prod |
| 15 | CERTRULE mapping configured | Prod |
