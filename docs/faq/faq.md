# Frequently Asked Questions

## General

??? question "What SAP versions does ABA support?"
    ABA is designed for **SAP on-premise landscapes**. It supports SAP ECC 6.0 EHP7+ and S/4HANA on-premise releases where the required OData services are available and activated. See [Supported Systems](../index.md) for the high-level compatibility overview.

??? question "Does ABA require changes to our SAP system?"
    No custom ABAP development is required. ABA uses SAP's standard OData services, which may need to be activated if not already. This is a configuration activity, not a development change.

??? question "Can ABA create or modify data in SAP?"
    Yes, ABA can both **read** and, for selected scenarios, **create and update** data in SAP (for example: purchase requisitions, sales orders, confirmations, time entries). All write operations are implemented with business validations, SAP authorizations, and—where required—approval workflows. See the individual module pages for the current write coverage.

??? question "Which AI models does ABA support?"
    ABA is **LLM-agnostic** but only connects to **enterprise-grade hosting**: trusted hyperscalers (Anthropic Claude, latest OpenAI GPT models, Azure OpenAI) and **SAP AI Core** (including SAP-managed/partner models such as Mistral). Self-hosted models are supported only when deployed in customer-controlled, compliant infrastructure. See [Architecture](../architecture/system-architecture.md) for details.

## Security

??? question "Can users see data they're not authorized to see in SAP?"
    No. Every query is executed with the authenticated user's SAP credentials. ABA respects the same authorization objects that control access in SAP GUI. If a user can't see it in SAP, they can't see it through ABA.

??? question "Is SAP data stored outside of SAP?"
    No. ABA queries SAP in real-time. Business data is never cached, stored, or logged on the ABA side. Only conversation logs (the natural language text) are optionally stored.

??? question "Is the connection encrypted?"
    Yes. All communications use TLS 1.2+ encryption. See [Authentication & Security](../architecture/authentication.md) for the full security model.

## Technical

??? question "How does ABA handle large result sets?"
    ABA uses OData pagination (`$top` and `$skip`) to handle large datasets. By default, queries return up to 50 results. Users can ask for more or refine their filters.

??? question "What happens if an OData service is not activated?"
    ABA will inform the user that the requested capability is not available and suggest contacting the system administrator to activate the required service.

??? question "Can we add our own custom OData services (Z services)?"
    Yes. ABA can integrate with any OData service that follows the standard protocol, including custom Z services. See [Adding OData Services](../integration/adding-odata-services.md).

## Usage

??? question "What languages does ABA support?"
    ABA works with **any language supported by the underlying LLM**. In practice, it is **primarily tested and tuned for English and Spanish**, and can also handle other major languages (German, Italian, French, etc.) depending on the chosen model.

??? question "How accurate are the responses?"
    ABA queries SAP in real-time, so the data is always current. The AI interpretation of natural language queries is highly accurate but may occasionally need clarification for ambiguous requests.

??? question "Can I use ABA from Microsoft Teams?"
    Yes. ABA can be deployed as a Teams bot, Slack app, web chat, or embedded in custom applications. See the integration and architecture sections for deployment patterns.

## Adoption & Operations

??? question "What happens if we change LLM provider?"
    Nothing breaks. ABA is **LLM-agnostic** — switching from one provider to another (e.g., from Azure OpenAI to Anthropic Claude, or to SAP AI Core) is a configuration change, not a rebuild of the solution.

??? question "Do we need to hire ABAP developers?"
    No. ABA uses **standard SAP OData services** — no custom ABAP code is required. Activating OData services is a Basis configuration activity that takes minutes per service.

??? question "Can we try ABA before committing?"
    Yes. ABA follows a **modular engagement model**: a Proof of Concept with 1–2 modules and a small user group can be completed in 2–4 weeks. See [Implementation Overview](../getting-started/implementation.md) for details.

??? question "How much training do end users need?"
    Minimal. ABA uses natural language — users simply type questions the way they would ask a colleague. Most users are productive within their **first session**. No SAP transaction knowledge is required.

??? question "What happens when SAP applies support packages or notes?"
    ABA relies on **SAP's standard OData services**, which are maintained by SAP as part of the platform. Applying support packages or notes does not typically affect OData service availability or ABA functionality.

??? question "Can ABA handle multiple company codes / plants / countries?"
    Yes. ABA respects **SAP organizational structures** — company codes, plants, purchasing organizations, etc. Queries can be scoped to any level the user has authorization for.

??? question "Is there a limit on the number of users?"
    ABA does not impose its own user limits. Scaling depends on the **LLM provider throughput** and **SAP system capacity**. The MCP Server is stateless and can be horizontally scaled.
