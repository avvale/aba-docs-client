# Installation Guide

Step-by-step guide to deploy the ABA MCP Server on SAP BTP and connect it to your SAP on-premise landscape.

---

## Prerequisites Checklist

Before starting, verify the following:

| # | Item | How to check |
|---|------|-------------|
| 1 | SAP Gateway active and reachable via HTTPS | Transaction `SMICM` → Services → HTTPS active |
| 2 | Required OData services activated | Transaction `/IWFND/MAINT_SERVICE` — see individual module pages for service list |
| 3 | SAP Cloud Connector installed and configured (for BTP ↔ on-premise connectivity) | SAP Cloud Connector admin console |
| 4 | SAP BTP subaccount with Cloud Foundry environment | SAP BTP Cockpit |
| 5 | LLM provider API credentials ready | Hyperscaler console or SAP AI Core cockpit |
| 6 | Test SAP user(s) with appropriate authorizations | Transaction `SU01` |

---

## Step 1 — Prepare SAP On-Premise

### 1.1 Activate OData Services

For each module you plan to enable, activate the required OData services:

```
Transaction: /IWFND/MAINT_SERVICE
→ Add Service → Search for the service name (see module pages)
→ Assign system alias
→ Activate
```

!!! tip "Start small"
    Begin with one module (e.g., FI or MM). You can activate additional modules later without affecting the existing setup.

### 1.2 Configure SAP Cloud Connector

The SAP Cloud Connector creates a secure tunnel between your on-premise SAP and SAP BTP:

1. Install SAP Cloud Connector on a server with network access to SAP Gateway
2. Register the Cloud Connector with your SAP BTP subaccount
3. Add the SAP Gateway host as an **internal system mapping**:
    - **Internal Host**: `<sap-gateway-host>:<port>`
    - **Virtual Host**: choose a virtual hostname (e.g., `sap-gw-virtual:443`)
    - **Protocol**: HTTPS
    - **Resource paths**: `/sap/opu/odata/sap/` (or restrict to specific services)
4. Verify connectivity status shows **Connected** in the Cloud Connector admin console

### 1.3 Create a Service User (Optional — for PoC)

For initial testing, a technical service user simplifies setup:

```
Transaction: SU01
→ Create user of type "System" or "Service"
→ Assign authorizations for the target OData services
→ Note: production deployments should use SSO / principal propagation
```

---

## Step 2 — Deploy MCP Server on SAP BTP

### 2.1 Create the Application

```bash
# Clone the MCP Server repository
git clone <mcp-server-repo-url>
cd aba-mcp-server

# Install dependencies
npm install

# Build
npm run build
```

### 2.2 Configure Environment

Create or update the environment configuration:

```bash
# Copy the template
cp .env.example .env
```

Edit `.env` with your values (see [Configuration](configuration.md) for all parameters):

```bash
# SAP Connection
SAP_HOST=sap-gw-virtual
SAP_PORT=443
SAP_PROTOCOL=https
SAP_CLIENT=100

# LLM Provider
LLM_PROVIDER=anthropic
LLM_API_KEY=<your-api-key>
LLM_MODEL=claude-sonnet-4-5-20250929

# Modules to enable
MODULES_ENABLED=fi,co,mm
```

### 2.3 Deploy to Cloud Foundry

```bash
# Login to your BTP subaccount
cf login -a <api-endpoint> -o <org> -s <space>

# Push the application
cf push aba-mcp-server -f manifest.yml

# Verify the application is running
cf apps
```

### 2.4 Bind to SAP Connectivity Service

```bash
# Create connectivity service instance (if not already created)
cf create-service connectivity lite aba-connectivity

# Create destination service instance
cf create-service destination lite aba-destination

# Bind services to the application
cf bind-service aba-mcp-server aba-connectivity
cf bind-service aba-mcp-server aba-destination

# Restage to pick up bindings
cf restage aba-mcp-server
```

---

## Step 3 — Configure Chat Client

The MCP Server exposes a standard MCP endpoint. Connect your chosen chat client:

| Client | Configuration |
|--------|--------------|
| **Web chat** | Point to the MCP Server URL; configure authentication |
| **Microsoft Teams** | Deploy the Teams bot app; configure the MCP endpoint in the bot settings |
| **Slack** | Create a Slack app; configure the MCP endpoint as the backend |
| **Custom** | Use the MCP protocol SDK to build your own client |

---

## Step 4 — Verify the Installation

Run through this checklist to confirm everything works:

- [ ] MCP Server application is running on BTP (`cf apps` shows `started`)
- [ ] Health endpoint responds: `GET <mcp-server-url>/health` → `200 OK`
- [ ] SAP connectivity works: MCP Server logs show successful OData calls
- [ ] A test query returns data: try *"List the first 5 vendors in company code 1000"*
- [ ] Authorization is enforced: test with a user who has limited access and confirm restricted data is not returned

---

## Step 5 — Go Live

| Activity | Description |
|----------|-------------|
| **Pilot group** | Start with 5–10 users from the target business area |
| **Feedback loop** | Collect questions that ABA handles well and cases that need refinement |
| **Module expansion** | Activate OData services for additional modules and enable them in the configuration |
| **Production auth** | Switch from service user to SSO / principal propagation for production |
| **Monitoring** | Set up log monitoring and alerting (see [Troubleshooting](../faq/troubleshooting.md)) |

---

!!! info "Deployment Support"
    For guided deployment and hands-on assistance, contact the Avvale AI team: **ai-team@avvale.com**
