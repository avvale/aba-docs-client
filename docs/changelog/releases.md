# Release Notes

## v0.1.0 — Initial Release (February 2026)

:material-tag: **First release**

### Modules
- **FI** (Financial Accounting) — Production ready
- **CO** (Controlling) — Production ready
- **MM** (Materials Management) — Production ready
- **SD** (Sales & Distribution) — Production ready
- **PP** (Production Planning) — Beta
- **HCM** (Human Capital Management) — Production ready
- **PM** (Plant Maintenance) — Production ready
- **QM** (Quality Management) — Alpha
- **WM/EWM** (Warehouse Management) — Alpha

### Features
- Natural language querying across all supported modules
- Cross-module document flow navigation
- SAP authorization model integration
- Enterprise LLM provider support via trusted hyperscalers and SAP AI Core
- Web chat, Microsoft Teams, and Slack client support
- On-premise focus (SAP ECC and S/4HANA on-premise)
- Read + controlled write operations (create/modify) for selected scenarios (see module pages)
- Multi-language support (primarily tested in English and Spanish)

### Known Issues
- Coverage varies by module and scenario; not all SAP processes are available via conversation yet
- Workflow triggers and scheduled queries depend on customer setup and may be limited/roadmapped
- Large result sets (>1000 records) may need additional filtering
- Latency may vary for cross-module queries due to multiple sequential SAP calls
