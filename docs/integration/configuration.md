# Configuration

The ABA MCP Server is configured through environment variables and a YAML configuration file. This page covers all available parameters.

---

## Environment Variables

Environment variables take precedence over the YAML configuration file and are the recommended way to handle secrets.

### SAP Connection

| Variable | Required | Default | Description |
|----------|----------|---------|-------------|
| `SAP_HOST` | Yes | — | SAP Gateway hostname (or Cloud Connector virtual host) |
| `SAP_PORT` | No | `443` | SAP Gateway HTTPS port |
| `SAP_PROTOCOL` | No | `https` | Protocol (`https` recommended; `http` only for local dev) |
| `SAP_CLIENT` | Yes | — | SAP client number (e.g., `100`) |
| `SAP_LANGUAGE` | No | `EN` | SAP logon language |

### Authentication

| Variable | Required | Default | Description |
|----------|----------|---------|-------------|
| `SAP_AUTH_METHOD` | No | `saml` | Authentication method: `saml`, `oauth2`, `basic` |
| `SAP_USERNAME` | Cond. | — | Required only for `basic` auth (PoC / dev environments) |
| `SAP_PASSWORD` | Cond. | — | Required only for `basic` auth (PoC / dev environments) |
| `SAP_OAUTH_TOKEN_URL` | Cond. | — | OAuth2 token endpoint (when `oauth2` is used) |
| `SAP_OAUTH_CLIENT_ID` | Cond. | — | OAuth2 client ID |
| `SAP_OAUTH_CLIENT_SECRET` | Cond. | — | OAuth2 client secret |

!!! warning "Security"
    Never use `basic` authentication in production. Use `saml` (principal propagation) or `oauth2` for production deployments. Never commit credentials to version control.

### LLM Provider

| Variable | Required | Default | Description |
|----------|----------|---------|-------------|
| `LLM_PROVIDER` | Yes | — | Provider identifier: `anthropic`, `openai`, `azure_openai`, `sap_ai_core`, `self_hosted` |
| `LLM_API_KEY` | Yes | — | API key for the LLM provider |
| `LLM_MODEL` | No | Provider default | Model name/ID (e.g., `claude-sonnet-4-5-20250929`, `gpt-4o`) |
| `LLM_MAX_TOKENS` | No | `4096` | Maximum tokens in the LLM response |
| `LLM_TEMPERATURE` | No | `0` | Temperature for response generation (0 = deterministic) |
| `LLM_BASE_URL` | Cond. | — | Custom API endpoint (required for `azure_openai`, `self_hosted`, `sap_ai_core`) |

#### Provider-Specific Notes

**Azure OpenAI:**

```bash
LLM_PROVIDER=azure_openai
LLM_API_KEY=<azure-api-key>
LLM_BASE_URL=https://<resource-name>.openai.azure.com
LLM_MODEL=<deployment-name>
```

**SAP AI Core:**

```bash
LLM_PROVIDER=sap_ai_core
LLM_API_KEY=<sap-ai-core-service-key>
LLM_BASE_URL=https://<ai-core-endpoint>/v2/inference/deployments/<deployment-id>
LLM_MODEL=<model-name>
```

**Self-hosted:**

```bash
LLM_PROVIDER=self_hosted
LLM_BASE_URL=https://<your-model-host>/v1
LLM_API_KEY=<optional-api-key>
LLM_MODEL=<model-name>
```

### Module Activation

| Variable | Required | Default | Description |
|----------|----------|---------|-------------|
| `MODULES_ENABLED` | No | All | Comma-separated list of modules to enable: `fi,co,mm,sd,pm,hcm,pp,qm,wm` |

Only enabled modules will register their tools with the MCP Server. Disabling a module prevents users from accessing its functionality, regardless of SAP authorizations.

### Logging

| Variable | Required | Default | Description |
|----------|----------|---------|-------------|
| `LOG_LEVEL` | No | `info` | Log level: `debug`, `info`, `warn`, `error` |
| `LOG_CONVERSATIONS` | No | `true` | Log natural language queries and responses |
| `LOG_SAP_DATA` | No | `false` | Log raw SAP data in responses — **set to `false` in production** |
| `LOG_AUDIT` | No | `true` | Log tool calls for audit trail |

---

## YAML Configuration File

For complex setups or multi-environment management, use a YAML configuration file:

```yaml
# config.yaml — example configuration
server:
  port: 3000
  cors_origins: ["https://chat.company.com"]

sap:
  host: sap-gw-virtual
  port: 443
  protocol: https
  client: "100"
  language: EN
  auth_method: saml

llm:
  provider: anthropic
  model: claude-sonnet-4-5-20250929
  max_tokens: 4096
  temperature: 0

modules:
  fi: true
  co: true
  mm: true
  sd: true
  pm: true
  hcm: true
  pp: false
  qm: false
  wm: false

logging:
  level: info
  conversations: true
  sap_data: false
  audit: true
```

!!! tip "Environment override"
    Environment variables always override values in the YAML file. This allows you to use the YAML file for non-sensitive defaults and environment variables for secrets and environment-specific values.

---

## Environment-Specific Configuration

Maintain separate configuration files per environment:

| File | Purpose |
|------|---------|
| `config.dev.yaml` | Local development — may use `basic` auth and `debug` logging |
| `config.test.yaml` | QA / testing — mirrors production structure with test data |
| `config.prod.yaml` | Production — `saml`/`oauth2` auth, `info` logging, no SAP data logging |

Start the MCP Server with a specific config:

```bash
# Local development
ABA_CONFIG=config.dev.yaml npm start

# Production (on BTP, set via environment variable)
cf set-env aba-mcp-server ABA_CONFIG config.prod.yaml
cf restage aba-mcp-server
```

---

## Secrets Management

!!! danger "Never commit secrets"
    API keys, SAP credentials, and OAuth secrets must **never** appear in version control.

Recommended approaches:

| Method | When to use |
|--------|-------------|
| **BTP environment variables** | Standard for Cloud Foundry deployments (`cf set-env`) |
| **BTP Credential Store** | For centralized secret management on BTP |
| **SAP BTP Destination Service** | For SAP connection credentials with automatic token refresh |
| **HashiCorp Vault / Azure Key Vault** | For customers with existing enterprise secrets management |

---

## Verification

After configuration, verify the setup:

```bash
# Check running configuration (sanitized — no secrets)
GET <mcp-server-url>/config

# Check enabled modules and their tool count
GET <mcp-server-url>/health

# Check SAP connectivity
GET <mcp-server-url>/health/sap
```

Expected healthy response:

```json
{
  "status": "ok",
  "sap_connected": true,
  "modules_enabled": ["fi", "co", "mm", "sd"],
  "tools_registered": 42,
  "llm_provider": "anthropic",
  "uptime_seconds": 3600
}
```
