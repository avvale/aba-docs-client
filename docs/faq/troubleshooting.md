# Troubleshooting

Common issues, their root causes, and solutions — organized by category.

---

## SAP Connectivity

??? failure "Connection refused / Timeout when connecting to SAP"
    **Symptoms:** MCP Server logs show `ECONNREFUSED` or `ETIMEDOUT` when calling OData endpoints.

    **Checklist:**

    1. Verify SAP Gateway is running: transaction `SMICM` → Services → HTTPS must be active
    2. Check SAP Cloud Connector status — must show **Connected** in the admin console
    3. Confirm virtual host mapping in Cloud Connector matches the `SAP_HOST` configured in the MCP Server
    4. Verify firewall rules allow HTTPS traffic from the Cloud Connector to SAP Gateway
    5. Test the OData endpoint directly: `https://<sap-host>/sap/opu/odata/sap/` — should return the service catalog

??? failure "401 Unauthorized"
    **Symptoms:** SAP returns HTTP 401 on every request.

    **Checklist:**

    1. Verify credentials are correct (for `basic` auth: `SAP_USERNAME` / `SAP_PASSWORD`)
    2. Check that the SAP user account is not locked: transaction `SU01` → Lock status
    3. For `saml`/`oauth2` auth: verify token validity and clock synchronization between servers
    4. Ensure the user has an active SAP session license and is not blocked by `SM04` limits

??? failure "403 Forbidden on specific OData services"
    **Symptoms:** Some queries work, others return HTTP 403.

    **Checklist:**

    1. The SAP user lacks authorization for the specific OData service or its underlying BAPIs
    2. Check authorization objects in transaction `SU53` (after the failed request) to identify missing authorizations
    3. Verify the OData service is activated for the correct system alias in `/IWFND/MAINT_SERVICE`
    4. Check ICF node activation in transaction `SICF` → `/sap/opu/odata/sap/<service_name>`

??? failure "404 Not Found for an OData service"
    **Symptoms:** HTTP 404 when calling a specific OData endpoint.

    **Checklist:**

    1. Verify the service is registered in `/IWFND/MAINT_SERVICE`
    2. Check that the system alias is correctly assigned
    3. Confirm the service technical name matches what the MCP tool expects (see module pages for service names)
    4. On S/4HANA: some services use `/sap/opu/odata4/sap/` (OData V4) — check the correct path

---

## OData / Data Issues

??? failure "Query returns empty results when data exists in SAP"
    **Possible causes:**

    1. **Authorization:** The user cannot see the data — verify in SAP GUI with the same user
    2. **Filters too restrictive:** The AI may have applied filters that exclude the expected data. Check the MCP Server logs for the actual OData query sent
    3. **Company code / org level mismatch:** The query may target a different organizational unit
    4. **OData service pagination:** Default `$top` may be too low — the MCP Server uses `$top=50` by default

??? failure "OData metadata errors"
    **Symptoms:** `Metadata loading failed` or `$metadata request failed`.

    **Checklist:**

    1. Test metadata directly: `GET /sap/opu/odata/sap/<service>/$metadata` — should return XML
    2. If the service was recently activated, regenerate the metadata cache: `/IWFND/MAINT_SERVICE` → select service → Load Metadata
    3. Check for SAP notes related to the service — some services require specific EHP or correction notes

??? failure "OData filter not supported"
    **Symptoms:** HTTP 400 or `UnsupportedFilterExpression` in the SAP response.

    **Resolution:**

    - Not all OData services support all filter combinations. Check the service's `$metadata` for the `sap:filterable` annotation on each property
    - Some services require specific filter parameters (e.g., `CompanyCode` is mandatory on many FI services)
    - The MCP tool should handle this gracefully — if not, report the filter combination to the development team

---

## LLM / AI Issues

??? failure "LLM request timeout"
    **Symptoms:** The LLM call times out before returning a response.

    **Checklist:**

    1. Check the LLM provider's status page for outages
    2. Verify network connectivity from the MCP Server (on BTP) to the LLM endpoint
    3. If using SAP AI Core, check the deployment status in the AI Core cockpit
    4. For complex queries requiring many sequential tool calls, increase the `LLM_MAX_TOKENS` and client-side timeout

??? failure "LLM returns incoherent or incorrect response"
    **Possible causes:**

    1. **Ambiguous question:** The user's prompt may be too vague — ask for more details
    2. **Tool description mismatch:** The LLM may have chosen the wrong tool. Check which tool was called in the logs
    3. **Context window overflow:** Very large SAP responses may exceed the LLM's context. Consider narrowing the query scope
    4. **Temperature too high:** Ensure `LLM_TEMPERATURE=0` for consistent, deterministic responses

??? failure "Tool not found / Tool call failed"
    **Symptoms:** LLM tries to call a tool that doesn't exist or fails during execution.

    **Checklist:**

    1. Verify the module is enabled in the configuration (`MODULES_ENABLED`)
    2. Check MCP Server startup logs — tool registration errors will appear during initialization
    3. If a specific tool fails, check the tool's OData service is activated and returns data when called directly
    4. Restart the MCP Server after configuration changes: `cf restart aba-mcp-server`

---

## Performance

??? failure "Slow responses (> 30 seconds)"
    ABA response time depends on multiple factors. Investigate in order:

    1. **SAP system load:** Check SM50 (work processes) and ST03N (workload analysis)
    2. **Number of tool calls:** Cross-module queries execute multiple sequential OData calls. This is normal — the AI shows its reasoning as it progresses
    3. **OData query efficiency:** Check the MCP Server logs for the actual OData queries. Large unfiltered queries take longer
    4. **Network latency:** BTP ↔ Cloud Connector ↔ SAP introduces latency per hop
    5. **LLM response time:** Varies by provider and model size; check provider dashboard

??? failure "MCP Server high memory usage"
    **Checklist:**

    1. Check if `LOG_SAP_DATA=true` — this stores full SAP responses in memory/logs and should be `false` in production
    2. Verify there are no memory leaks by checking the `/health` endpoint's `uptime` and memory metrics over time
    3. Scale the Cloud Foundry application memory if needed: `cf scale aba-mcp-server -m 1G`

---

## Reading MCP Server Logs

### On SAP BTP (Cloud Foundry)

```bash
# Stream live logs
cf logs aba-mcp-server

# Recent logs (last 100 lines)
cf logs aba-mcp-server --recent

# Filter by level
cf logs aba-mcp-server --recent | grep "ERROR"
```

### Log Structure

Each log entry contains:

```
[timestamp] [level] [component] message
```

| Component | What it logs |
|-----------|-------------|
| `server` | Application lifecycle (start, stop, health checks) |
| `sap` | OData requests and responses (status codes, durations) |
| `llm` | LLM calls (model, tokens used, duration) |
| `tool` | Tool execution (tool name, parameters, result summary) |
| `auth` | Authentication events (login, token refresh, failures) |

### Useful Log Queries

| What you want | How to find it |
|---------------|---------------|
| Failed SAP calls | `grep "sap.*ERROR"` |
| Slow OData requests | `grep "sap.*duration" | awk '$NF > 5000'` |
| Tool call sequence for a query | Search by conversation ID: `grep "<conversation-id>"` |
| Authentication failures | `grep "auth.*ERROR\|401\|403"` |

---

!!! info "Need more help?"
    Contact the Avvale AI team: **ai-team@avvale.com**
