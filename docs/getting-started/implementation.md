# Implementation Overview

How ABA is deployed, what your organization needs to provide, and what a typical timeline looks like.

---

## What Is Required from Your Side?

| Area | What is needed | Effort |
|------|---------------|--------|
| **SAP Basis** | Activate standard OData services for the target modules | Low — configuration only, no ABAP development |
| **Network / Security** | Allow HTTPS connectivity between BTP (MCP Server) and SAP Gateway | Standard firewall rule |
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
- Define the user group for the proof of concept
- Select LLM provider (hyperscaler or SAP AI Core)

### Phase 2 — Proof of Concept (2–4 weeks)

- Deploy MCP Server on SAP BTP
- Activate OData services for 1–2 target modules
- Configure connectivity and authentication
- Run use cases with a small group of business users
- Validate data accuracy and authorization enforcement

### Phase 3 — Pilot (4–6 weeks)

- Expand to additional modules based on PoC results
- Broader user group (10–20 users across departments)
- Refine prompt patterns and edge cases
- Assess user adoption and feedback

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

- **Each SAP module** is an independent set of MCP tools.
- **Enable only what you need** — start with FI/CO and add MM, SD, PM, HCM as priorities emerge.
- **New modules** require activating OData services and deploying the corresponding MCP tools — no changes to existing configuration.

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

??? question "Can we start with just one module?"
    Yes. Each module is independent. Most organizations start with FI/CO or MM and expand from there.

??? question "Do we need to involve SAP licensing?"
    No new SAP licenses are required. ABA uses existing OData services and the customer's existing SAP user base.

??? question "Can we run a PoC before committing?"
    Yes. The PoC phase is designed exactly for this — validate the solution with real data and real users before expanding.

??? question "What if we change LLM provider later?"
    ABA is LLM-agnostic. Switching providers is a configuration change, not a rebuild.
