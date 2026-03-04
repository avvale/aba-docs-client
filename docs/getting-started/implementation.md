# Implementation Overview

How ABA is deployed, what your organization needs to provide, and what a typical timeline looks like.

---

## Technical Prerequisites

Before deployment can begin, the following must be available in your environment. Avvale handles the deployment and configuration of ABA itself — these are the components your IT team needs to provide or confirm.

### SAP BTP

ABA is deployed as a **Cloud Foundry application on SAP BTP**. It is not a marketplace service — it is Avvale's own application running in your BTP subaccount.

| Requirement | Details |
|-------------|---------|
| **Cloud Foundry environment** | Enabled in a BTP subaccount with at least 2 GB of runtime memory. |
| **Connectivity Service** | Service instance (lite plan) — enables secure communication with your on-premise SAP via Cloud Connector. |
| **Destination Service** | Service instance (lite plan) — stores the connection configuration to your SAP system. |
| **Authorization & Trust Management (XSUAA)** | Service instance (application plan) — handles user authentication. Required for production with single sign-on. |

!!! info "If you already run Fiori apps on BTP"
    You likely have most of these services available. ABA uses the same BTP infrastructure that SAP Fiori launchpad and other BTP applications rely on.

### SAP On-Premise

| Requirement | Details |
|-------------|---------|
| **SAP version** | ECC 6.0 EHP7+ or S/4HANA on-premise 1909+. |
| **SAP Gateway** | Activated and accessible via HTTPS (standard for Fiori environments). |
| **OData services** | Standard SAP OData services activated for the target modules. Avvale provides the specific service list during the Discovery phase. |
| **User authorizations** | SAP users with appropriate roles for the target modules. The same authorizations they use for Fiori or SAP GUI apply. |

### SAP Cloud Connector

The SAP Cloud Connector provides the secure tunnel between BTP and your on-premise SAP system.

| Requirement | Details |
|-------------|---------|
| **Version** | 2.16 or higher. Latest stable version recommended. |
| **Status** | Connected to the target BTP subaccount. |
| **Configuration** | SAP Gateway exposed as a backend system with HTTPS and the required OData paths accessible. |

!!! info "Existing Cloud Connector"
    If you already use SAP Cloud Connector for Fiori, SAP Analytics Cloud, or other BTP services, the existing installation can be reused. Only an additional system mapping for ABA's OData paths is needed.

### LLM Provider

ABA requires an enterprise-grade Large Language Model. You choose the provider:

| Provider | What you need |
|----------|---------------|
| **Anthropic (Claude)** | API key from the Anthropic console. |
| **Azure OpenAI** | Azure subscription with a model deployment (GPT-4o or similar). |
| **OpenAI** | API key from the OpenAI platform. |
| **SAP AI Core** | SAP AI Core entitlement with a model deployment on BTP. |

In all cases, your data is processed in isolated environments, transmitted over encrypted channels, and is never used to train AI models.

### Network

| Connection | What is needed |
|-----------|----------------|
| BTP → SAP (via Cloud Connector) | The Cloud Connector initiates the tunnel outbound — no inbound firewall rules required on your network. |
| Users → Chat interface | Standard HTTPS (port 443) access to the chat client (web, Teams, or Slack). |

---

## What Is Required from Your Side?

| Area | What is needed | Effort |
|------|---------------|--------|
| **SAP Basis** | Activate standard OData services for the target modules | Low — configuration only, no ABAP development |
| **BTP Administration** | Ensure Cloud Foundry environment, service entitlements, and Cloud Connector are available | Low — standard BTP administration |
| **Network / Security** | Confirm HTTPS connectivity between BTP and SAP Gateway via Cloud Connector | Standard — typically already in place for Fiori |
| **Functional users** | Provide test users with appropriate SAP authorizations for validation | Existing SAP users |
| **Business validation** | Review and confirm that ABA responses match expected SAP data | Functional team effort during pilot |

!!! success "What is NOT required"
    - No custom ABAP development
    - No SAP kernel or support package changes
    - No modifications to existing SAP transactions or reports
    - No new SAP licenses

---

## Typical Project Phases

```mermaid
flowchart LR
    D["Discovery"] --> PoC["Proof of Concept"]
    PoC --> Pilot["Pilot"]
    Pilot --> Rollout["Rollout"]
```

### Phase 1 — Discovery (1–2 weeks)

- Identify priority modules and use cases with the business
- Validate OData service availability in the SAP landscape
- Confirm BTP entitlements and Cloud Connector status
- Define the user group for the proof of concept
- Select LLM provider (hyperscaler or SAP AI Core)

### Phase 2 — Proof of Concept (2–4 weeks)

- Deploy ABA on SAP BTP
- Activate OData services for 1–2 target modules
- Configure connectivity (Cloud Connector, Destination, basic auth for PoC)
- Run use cases with a small group of business users
- Validate data accuracy and authorization enforcement

### Phase 3 — Pilot (4–6 weeks)

- Expand to additional modules based on PoC results
- Configure production authentication (Principal Propagation / SSO)
- Broader user group (10–20 users across departments)
- Refine prompt patterns and edge cases
- Assess user adoption and feedback

!!! warning "Planned maintenance"
    Configuring Principal Propagation for production requires setting ICM profile parameters on the SAP system, which need a **scheduled system restart** to take effect. This is coordinated during the Pilot phase.

### Phase 4 — Rollout (modular, ongoing)

- Enable remaining modules incrementally
- Onboard additional user groups
- Each module is independent — rollout at your own pace

---

## Impact on Your SAP System

| Concern | Reality |
|---------|---------|
| **Performance** | ABA queries use standard OData calls — same as Fiori apps. No additional load beyond normal API usage. |
| **Data integrity** | Write operations follow SAP business rules and validations. No direct table access. |
| **Availability** | ABA is a complementary layer. If ABA is offline, SAP operates normally. |
| **Upgrade path** | Standard OData services are maintained by SAP. No custom code to maintain. |

---

## How It Scales

ABA is modular by design:

- **Each SAP module** is an independent set of capabilities.
- **Enable only what you need** — start with FI/CO and add MM, SD, PM, HCM as priorities emerge.
- **New modules** require activating OData services and deploying the corresponding tools — no changes to existing configuration.

---

## User Training

ABA uses natural language — training requirements are minimal:

| Audience | Training | Duration |
|----------|----------|----------|
| Business users | "How to ask questions" — prompt patterns and tips | 30–60 min |
| Power users | Module-specific capabilities and write operations | 1–2 hours |
| IT / Basis | OData activation, connectivity, monitoring | Half day |

Most users are productive within their first session.

---

## Frequently Asked Questions About Implementation

??? question "Is ABA a specific SAP BTP service?"
    No. ABA is Avvale's own application, deployed as a Cloud Foundry app in your BTP subaccount. It consumes standard BTP services (Connectivity, Destination, XSUAA) that are part of the regular BTP offering. You will not find it in the BTP Service Marketplace — it is deployed and managed by Avvale in your environment.

??? question "Can we start with just one module?"
    Yes. Each module is independent. Most organizations start with FI/CO or MM and expand from there.

??? question "Do we need to involve SAP licensing?"
    No new SAP licenses are required. ABA uses existing OData services and the customer's existing SAP user base.

??? question "Can we run a PoC before committing?"
    Yes. The PoC phase is designed exactly for this — validate the solution with real data and real users before expanding.

??? question "What if we change LLM provider later?"
    ABA is LLM-agnostic. Switching providers is a configuration change, not a rebuild.

??? question "Do we need a system restart for the initial PoC?"
    No. The PoC uses basic authentication (a technical service user), which does not require ICM parameter changes. The system restart is only needed when configuring Principal Propagation for production SSO.

??? question "We already use BTP for Fiori — does that simplify things?"
    Significantly. If you already have Cloud Foundry enabled, Cloud Connector connected, and XSUAA in use, the BTP side is largely already in place. ABA reuses the same infrastructure.
