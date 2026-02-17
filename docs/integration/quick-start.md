# Quick Start (Developer)

Get ABA running locally in under 15 minutes. This guide is for developers setting up a local development environment against a sandbox/development SAP system.

---

## Prerequisites

| Requirement | Version |
|-------------|---------|
| Node.js | 18+ (20 LTS recommended) |
| npm | 9+ |
| Git | Any recent version |
| SAP system | Development/sandbox with OData services activated |
| LLM API key | Anthropic, OpenAI, Azure OpenAI, or SAP AI Core |

---

## Step 1 — Clone and Install

```bash
git clone <mcp-server-repo-url>
cd aba-mcp-server
npm install
```

---

## Step 2 — Configure Environment

```bash
cp .env.example .env
```

Edit `.env` with your development values:

```bash
# --- SAP Connection ---
SAP_HOST=<your-sap-dev-host>
SAP_PORT=443
SAP_PROTOCOL=https
SAP_CLIENT=100
SAP_AUTH_METHOD=basic
SAP_USERNAME=<your-sap-user>
SAP_PASSWORD=<your-sap-password>

# --- LLM Provider ---
LLM_PROVIDER=anthropic
LLM_API_KEY=sk-ant-...
LLM_MODEL=claude-sonnet-4-5-20250929
LLM_TEMPERATURE=0

# --- Modules ---
MODULES_ENABLED=fi,mm

# --- Logging ---
LOG_LEVEL=debug
LOG_CONVERSATIONS=true
LOG_SAP_DATA=true
LOG_AUDIT=true
```

!!! warning "Dev only"
    `basic` auth and `LOG_SAP_DATA=true` are for local development only. Never use these settings in production.

---

## Step 3 — Start the MCP Server

```bash
npm run dev
```

Expected output:

```
[info] [server] ABA MCP Server starting...
[info] [server] Loading configuration from .env
[info] [sap] Testing SAP connectivity... OK
[info] [tool] Registered 24 tools for modules: fi, mm
[info] [server] MCP Server listening on http://localhost:3000
```

Verify the health endpoint:

```bash
curl http://localhost:3000/health
```

Expected response:

```json
{
  "status": "ok",
  "sap_connected": true,
  "modules_enabled": ["fi", "mm"],
  "tools_registered": 24
}
```

---

## Step 4 — Test Your First Prompt

### Option A — Built-in test client

```bash
npm run test:prompt -- "List the first 5 vendors in company code 1000"
```

### Option B — cURL to the MCP endpoint

```bash
curl -X POST http://localhost:3000/mcp \
  -H "Content-Type: application/json" \
  -d '{
    "prompt": "List the first 5 vendors in company code 1000"
  }'
```

### Option C — Connect a chat client

Point any MCP-compatible chat client to `http://localhost:3000` and start chatting.

---

## Step 5 — Enable More Modules

Edit `.env` to add modules:

```bash
MODULES_ENABLED=fi,co,mm,sd
```

Restart the server:

```bash
npm run dev
```

Check the `/health` endpoint to confirm the new tools are registered.

---

## Common Development Tasks

| Task | Command |
|------|---------|
| Start dev server with hot reload | `npm run dev` |
| Run tests | `npm test` |
| Run a single tool test | `npm run test:tool -- <tool-name>` |
| Lint | `npm run lint` |
| Build for production | `npm run build` |
| Check OData connectivity | `npm run check:sap` |

---

## Project Structure (Overview)

```
aba-mcp-server/
├── src/
│   ├── server.ts          # MCP Server entry point
│   ├── config/             # Configuration loading
│   ├── sap/                # SAP OData client and auth
│   ├── tools/              # MCP tool definitions
│   │   ├── fi/             # FI module tools
│   │   ├── mm/             # MM module tools
│   │   ├── sd/             # SD module tools
│   │   └── ...
│   └── utils/              # Shared utilities
├── .env.example            # Environment template
├── manifest.yml            # BTP deployment manifest
├── package.json
└── tsconfig.json
```

---

## Troubleshooting Local Setup

??? failure "Cannot connect to SAP"
    1. Verify the SAP host is reachable from your machine: `curl https://<host>/sap/opu/odata/sap/`
    2. If behind a VPN, ensure you are connected
    3. Check `SAP_PORT` and `SAP_PROTOCOL` match your SAP Gateway configuration

??? failure "LLM API key errors"
    1. Verify the API key is correct and has not expired
    2. Check that the `LLM_PROVIDER` value matches the key type
    3. For Azure OpenAI, ensure `LLM_BASE_URL` includes the resource name

??? failure "No tools registered"
    1. Check `MODULES_ENABLED` — it must list the modules you want
    2. Ensure the corresponding OData services are activated in SAP
    3. Check server logs for tool registration errors

---

## Next Steps

- [Configuration Reference](configuration.md) — all available parameters
- [Adding OData Services](adding-odata-services.md) — add new SAP services
- [Custom Tools](custom-tools.md) — build your own MCP tools
- [Troubleshooting](../faq/troubleshooting.md) — common issues and solutions
