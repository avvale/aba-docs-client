# Principal Propagation: SAP BTP → SAP ABAP On-Premise

Step-by-step configuration guide for delegated authentication (Principal Propagation) from a Cloud Foundry application on SAP BTP to an on-premise SAP ABAP system via Cloud Connector.

The user invoking ABA is propagated all the way to SAP without requiring separate credentials — every OData call is executed as the authenticated user, respecting their existing SAP authorizations.

---

## Architecture Overview

```
User (Teams / web client)
    ↓  AAD token
CF Application on BTP (ABA)
    ├─ Exchange AAD token → IAS token
    ├─ Exchange IAS token → XSUAA user token  (jwt-bearer grant)
    ├─ Obtain Connectivity Service technical token  (client_credentials)
    └─ Call SAP OData via Connectivity Service proxy
           Proxy-Authorization: Bearer <connectivity-token>
           SAP-Connectivity-Authentication: Bearer <xsuaa-user-token>
    ↓
Connectivity Service (BTP)
    └─ Generates short-lived X.509 certificate  CN=<user-email>
    ↓
Cloud Connector (on-premise)
    ├─ Receives user certificate from Connectivity Service
    ├─ Signs certificate with CA Certificate
    ├─ Forwards to SAP backend via HTTP header  SSL_CLIENT_CERT
    └─ Subject Pattern: CN=${email}
    ↓
SAP ICM (on-premise, port 44300)
    ├─ Verifies intermediary via icm/trusted_reverse_proxy_0
    ├─ Validates certificate signed by CA Certificate
    ├─ Extracts CN from certificate (user email)
    └─ CERTRULE maps email → SAP username
    ↓
SAP ABAP
    └─ Executes OData request as the propagated user
```

---

## Prerequisites

- SAP BTP subaccount with Cloud Foundry enabled
- Cloud Connector 2.16+ installed and connected to the BTP subaccount
- On-premise SAP ABAP system reachable from the Cloud Connector host
- IAS tenant federated with the corporate IdP (Azure AD / Microsoft Entra ID)
- SAP users with email address configured in SU01

---

## Part 1 — Microsoft Entra ID (Azure AD)

### 1.1 App Registration

In **Entra ID → App registrations**, create a new application:

| Field | Value |
|---|---|
| Name | Descriptive name (e.g. `SAP-IAS-Federation`) |
| Supported account types | Single tenant |
| Redirect URI | `https://<ias-tenant>.accounts.ondemand.com/oauth2/callback` |

After registering, note down:

- **Application (client) ID** — needed for IAS configuration
- **Directory (tenant) ID** — needed for admin consent steps

### 1.2 Expose an API — Scope definition

In **Expose an API**, set the Application ID URI and create a scope:

| Field | Value |
|---|---|
| Application ID URI | `api://<app-client-id>` |
| Scope name | `access_as_user` (**exact spelling — double s**) |
| Who can consent | Admins and users |

!!! warning "Common typo"
    The scope must be `access_as_user` (two s's). A single-s typo (`acces_as_user`) causes authentication to fail silently.

Authorize the SAP client applications under **Authorized client applications**. For Microsoft Teams:

- `5e3ce6c0-2b1f-4285-8d4b-75ee78787346`
- `1fec8e78-bce4-4aaf-ab1b-5451cc387264`

### 1.3 Manifest — Token version

In **Manifest**, ensure:

```json
"accessTokenAcceptedVersion": 2
```

This is required for v2.0 tokens, which IAS expects.

### 1.4 Token configuration — Email claim

In **Token configuration → Add optional claim → Access token**, enable the **`email`** claim.

!!! warning "Required"
    Without the email claim in the access token, IAS cannot identify the user during the jwt-bearer token exchange. Authentication will fail even if everything else is correct.

### 1.5 Client secret

Create a **Client secret** for the application.

### 1.6 IAS — Corporate IdP trust

In **SAP Cloud Identity Services (IAS) → Identity Providers**, configure Microsoft Entra ID as a Corporate Identity Provider:

- **Metadata URL**: `https://login.microsoftonline.com/<tenant-id>/federationmetadata/2007-06/federationmetadata.xml`
- **User attribute mapping**: map the Entra `email` claim to the IAS `email` attribute

---

## Part 2 — SAP BTP (Cloud Foundry Application)

### 2.1 Required service instances

Bind the following services to the ABA application:

| Service | Plan |
|---------|------|
| XSUAA | `application` |
| SAP Connectivity Service | `lite` |
| SAP Destination Service | `lite` |

### 2.2 Destination configuration

Create an OnPremise destination with Principal Propagation:

| Field | Value |
|---|---|
| Name | Any name (e.g. `SAP_ONPREM_PP`) |
| Type | HTTP |
| URL | `http://<virtual-host>:<virtual-port>` |
| Proxy Type | OnPremise |
| Authentication | PrincipalPropagation |

!!! info "Expected behaviour"
    The Destination Service with `PrincipalPropagation` on an OnPremise destination does **not** return `authTokens` in the response — this is by design. The Connectivity Service proxy is responsible for generating the X.509 certificate and forwarding it to the Cloud Connector.

---

## Part 3 — Cloud Connector

### 3.1 Certificate overview

The Cloud Connector uses **two separate certificates** with distinct roles:

| Certificate | Location in SCC | Purpose |
|---|---|---|
| **System Certificate** | Configuration → On-Premises → System Certificate | Identifies the SCC to the backend during SSL handshake |
| **CA Certificate** | Configuration → On-Premises → CA Certificate | Signs the short-lived user certificates generated for PP |

!!! warning "No Subject Alternative Names on System Certificate"
    The System Certificate must **not contain Subject Alternative Names (SANs)**. If it does, intermediary validation in the backend will fail.

### 3.2 System Mapping

In **Cloud to On-Premises → System Mapping**, configure the backend:

| Field | Value |
|---|---|
| Back-end Type | ABAP System |
| Protocol | HTTPS |
| Internal Host | `<backend-hostname>` |
| Internal Port | `44300` (or the configured HTTPS port) |
| Principal Type | X.509 Certificate |
| **System Certificate for Logon** | ✅ **Checked** |
| Host in Request Header | Use Virtual Host |

!!! warning "Critical checkbox"
    "System Certificate for Logon" must be checked. Without it, ICM receives no client certificate from the SCC and cannot trust the forwarded user certificate.

### 3.3 Subject Pattern

In the system backend configuration → **Principal Propagation**, set:

```
Subject Pattern: CN=${email}
```

This generates user certificates with `CN=user@domain.com`, which CERTRULE maps to the SAP user by email.

### 3.4 Accessible resources

In **Cloud to On-Premises → Resources**, add:

| Path | Access Policy |
|---|---|
| `/sap/opu/odata/` | Path And All Sub-Paths |

---

## Part 4 — SAP ABAP On-Premise

### 4.1 STRUST — Import trusted certificates

Transaction **STRUST** → **SSL Server Standard**:

1. Double-click "SSL Server Standard"
2. Import the Cloud Connector **CA Certificate** (e.g. `CN=SCC_CA`)
3. Import the Cloud Connector **System Certificate** (e.g. `CN=SCC_SYSTEM`)
4. Save

Both certificates must appear in the trust list of the SSL Server Standard PSE.

### 4.2 CERTRULE — Certificate to SAP user mapping

Transaction **CERTRULE** — create a rule:

| Field | Value |
|---|---|
| Issuer filter | `CN=<SCC-CA-Certificate-CN>` |
| Subject attribute | CN |
| Login As | Email address |

When a certificate arrives with `CN=user@domain.com`, SAP looks up the user whose email in SU01 matches.

### 4.3 SU01 — SAP user email

Each SAP user must have the **email address** configured in the address data tab. The email must match exactly the email from the IdP token (Entra ID / IAS).

### 4.4 RZ10 — ICM profile parameters

Transaction **RZ10** → edit the instance profile. Add the following parameters:

| Parameter | Value | Notes |
|---|---|---|
| `icm/HTTPS/verify_client` | `1` | Requests client certificate in SSL handshake |
| `login/certificate_mapping_rulebased` | `1` | Enables CERTRULE-based mapping |
| `icm/trusted_reverse_proxy_0` | `SUBJECT="CN=<SCC-System-Cert-CN>", ISSUER="CN=<SCC-System-Cert-CN>"` | Declares the Cloud Connector as a trusted intermediary |

!!! danger "Do NOT mix old and new trust parameters"
    SAP provides two approaches (see SAP Note 2052899):

    - **Old approach:** `icm/HTTPS/trust_client_with_issuer` + `icm/HTTPS/trust_client_with_subject`
    - **New approach:** `icm/trusted_reverse_proxy_<n>` (supports multiple proxies, recommended)

    Use **only one approach**. If both are set, the `icm/trusted_reverse_proxy_<n>` parameters are silently ignored.

!!! warning "Full system restart required"
    `icm/trusted_reverse_proxy_<n>` and `icm/HTTPS/verify_client` are **not dynamic** — they require a full SAP system restart (`stopsap` / `startsap`). They cannot be applied via SMICM reload or RZ11. Plan this as a scheduled maintenance window.

**SAP references:**

- KBA 3017609 — *"Reject untrusted forwarded certificate" in Principal Propagation scenarios*
- SAP Note 2052899 — *ICM: multiple trusted reverse proxies*
- KBA 3371621 — *Common mistakes when setting ICM parameters related to SAP Cloud Connector*

---

## Part 5 — Verification and Troubleshooting

### 5.1 Cloud Connector monitor

In **Cloud to On-Premises → Monitor**, verify:

- Requests arrive with the propagated user visible in the "User" column
- No connection errors to the backend

### 5.2 ICM trace (SMICM)

Enable trace level 3: **SMICM → Administration → Trace File → Set Level → 3**

**Success message:**
```
Accept trusted forwarded certificate (received via HTTPS with trusted certificate)
```

**Error messages and causes:**

| ICM trace message | Cause | Fix |
|---|---|---|
| `intermediary is NOT trusted` + `received via HTTPS without certificate` | SCC not presenting System Certificate | Check "System Certificate for Logon" in SCC System Mapping |
| `intermediary is NOT trusted` + `received via HTTPS with untrusted certificate` | `icm/trusted_reverse_proxy_0` not active or wrong value | Verify parameter in RZ10 and restart SAP |
| `received via HTTPS without certificate` (no forwarded cert at all) | `icm/HTTPS/verify_client` not set | Add parameter in RZ10 and restart |
| Certificate accepted but user not found / 401 | Email in SU01 does not match CN in certificate | Check email in SU01 and Subject Pattern in SCC |

### 5.3 Common pitfalls

- **Spaces in certificate DN values**: SUBJECT/ISSUER values in `icm/trusted_reverse_proxy_0` must match the certificate exactly, including spacing between attributes.
- **SANs on the System Certificate**: if the SCC System Certificate has Subject Alternative Names, remove them and regenerate.
- **Dynamic vs. static parameters**: `icm/trusted_reverse_proxy_<n>` is marked as dynamic in metadata but does **not** apply via RZ11 or SMICM reload — a full restart is required.
- **`authTokens` empty in Destination Service response**: this is normal for OnPremise PrincipalPropagation. Use the Connectivity Service proxy directly.
- **AAD scope typo**: ensure the Entra scope is `access_as_user` (double s).
- **Missing email claim in AAD token**: the `email` optional claim must be enabled in Entra token configuration.

---

## Configuration Summary

```
Microsoft Entra ID (Azure AD)
├── App Registration: Application ID URI configured
├── Scope: access_as_user (exact spelling)
├── Manifest: accessTokenAcceptedVersion = 2
├── Token configuration: email claim enabled
└── Client secret configured

SAP Cloud Identity Services (IAS)
└── Corporate IdP: Microsoft Entra ID federated
    └── email attribute mapped

SAP Cloud Connector
├── System Certificate: CN=<SCC-SYSTEM>  (presented in SSL handshake)
├── CA Certificate: CN=<SCC-CA>          (signs user certificates)
├── System Mapping: Protocol=HTTPS, Principal Type=X.509
│   System Certificate for Logon = true
└── Subject Pattern: CN=${email}

SAP ABAP — RZ10 instance profile
├── icm/HTTPS/verify_client = 1
├── login/certificate_mapping_rulebased = 1
└── icm/trusted_reverse_proxy_0 = SUBJECT="CN=<SCC-SYSTEM>", ISSUER="CN=<SCC-SYSTEM>"
    ⚠️  Requires full system restart

SAP ABAP — STRUST (SSL Server Standard)
├── CN=<SCC-CA>      (CA Certificate — signs user certs)
└── CN=<SCC-SYSTEM>  (System Certificate — intermediary identity)

SAP ABAP — CERTRULE
└── Issuer: CN=<SCC-CA>, Attribute: CN, Login As: Email address

SAP ABAP — SU01
└── User email = email from IdP token
```

---

## References

- SAP KBA 3017609 — *"Reject untrusted forwarded certificate" in Principal Propagation scenarios*
- SAP KBA 3452851 — *Step-by-step Principal Propagation setup (without Web Dispatcher)*
- SAP KBA 3221674 — *Cloud Connector: "no suitable certificate found" warning*
- SAP Note 2052899 — *ICM: multiple trusted reverse proxies*
- SAP Note 2885371 — *How to ensure the server requests the X.509 client certificate*
