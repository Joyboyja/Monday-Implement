# Project Configuration

Before starting any phase, ask the user to fill in the following project variables. These are referenced throughout all phases to keep output consistent.

```yaml
# Client & Project
client_name: ""              # e.g., "Acme Corp"
project_name: ""             # e.g., "Acme CRM Migration"
launch_target: ""            # e.g., "2026-06-01"

# monday.com Target
workspace_name: ""           # e.g., "Acme CRM"
workspace_id: ""             # Fill after MCP check if known
monday_plan_tier: ""         # Standard / Pro / Enterprise

# Source System
source_platform: ""          # e.g., "Salesforce", "HubSpot", "Spreadsheets", "None"
source_platform_version: ""  # e.g., "Salesforce Lightning", "HubSpot Free"

# Third-Party Tools
signature_tool: ""           # e.g., "DocuSign", "GetSign", "PandaDoc", "None"
accounting_tool: ""          # e.g., "Sage Intacct", "QuickBooks", "None"
document_storage: ""         # e.g., "Google Drive", "SharePoint", "None"
other_integrations: []       # List any others mentioned in docs

# Contacts
primary_contact: ""          # Client-side project lead
technical_contact: ""        # Client-side IT/admin contact
```

Save the completed config to `project-config.yaml` in the project directory. All phases should read this file and use these values instead of re-inferring them from documents.

If the user skips this step, infer values from the attached documents and confirm with the user before proceeding.
