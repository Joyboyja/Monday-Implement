# monday.com Implementation: Research Phase

Conduct foundational research before analyzing client documents. This ensures the implementation spec is grounded in current platform realities, not assumptions.

---

## CRITICAL RULES (ALL PHASES)

1. **No hardcoding.** All solutions must be generic and pattern-based, not tied to specific examples.
2. **Root cause, not bandaid.** Fix underlying structural or data issues, not symptoms.
3. **Data integrity first.** Use consistent, authoritative data sources throughout.
4. **Ask before changing.** If you have questions, ask them before making changes.
5. **Trace every requirement.** Every element in the spec must trace back to a source document. Every element in the build must trace back to the spec.

---

## MCP Availability Check

Before starting, verify the monday.com MCP tool is available:

```
Run: mcp__monday__get_user_context
```

- **If the tool exists and returns data**: MCP is available. Note the account info for later phases. Confirm with the user that this is the correct account for the project. If they need to switch to a different monday.com account, instruct them: "Go to `/mcp`, select **monday**, and reauthenticate with the correct account."
- **If the tool does not exist or errors**: monday.com MCP is NOT installed. Prompt the user:
  > "monday.com MCP server is not connected. The Research and Planning phases can proceed without it, but the Execution and Verification phases require it.
  >
  > Would you like to install it now? Install instructions: https://github.com/mondaycom/mcp (yes/no)"
  >
  > If **yes**: Guide the user through installation per the repo README, then re-check with `mcp__monday__get_user_context`.
  > If **no**: Proceed with Research/Planning only. Remind the user to install before running `/monday-implement:execute`.

---

## Research Areas

Read the user's attached documents first to identify what needs to be researched, then conduct web searches for each area below.

### 1A: monday.com Platform Research

Research and document:

- Current column types available in monday.com CRM and their limitations (character limits, behavior quirks, known bugs)
- Native automation capabilities and limits (trigger types, action types, maximum automations per board, rate limits)
- Form builder capabilities and limitations (conditional logic support, field types available in forms vs. boards, file upload behavior)
- API constraints relevant to bulk operations and data migration (rate limits, batch sizes, pagination)
- Available integrations in the monday.com marketplace relevant to the project (search for any tools the client has mentioned)
- CRM-specific features: lead/contact/deal management, connected boards behavior, mirror column limitations
- Auto-number column behavior: format options, starting number configuration, uniqueness guarantees
- Permission and access control model: board-level, column-level, item-level permissions

### 1B: Client Industry/Organization Research

Research and document:

- What the client's organization does, its structure, and operational context
- Industry-specific compliance or regulatory requirements that may affect the CRM design (e.g., NGO procurement rules, government contracting standards, GDPR)
- Common workflows in the client's industry that may inform best practices

### 1C: Source Platform Research

If the client is migrating from another system (Salesforce, HubSpot, spreadsheets, etc.), research and document:

- The source platform's data model (e.g., Salesforce objects, HubSpot entities) and how it maps to monday.com
- Known migration challenges from the source platform to monday.com
- Data export formats and limitations from the source platform
- Features the client may be accustomed to that do not have a direct monday.com equivalent

### 1D: Third-Party Tool Research

For every third-party tool mentioned in the client documents (DocuSign, GetSign, Sage Intacct, PandaDoc, Google Drive, etc.), research and document:

- Whether a monday.com integration exists (native, marketplace app, or API-only)
- What the integration can and cannot do
- Document merge capabilities (which tools support populating Word/PDF templates from monday.com field data)
- Pricing tier requirements (some integrations require specific monday.com or third-party plan levels)
- Setup complexity and known issues

---

## Research Output

Save findings to `research-summary.md` in the project directory, organized by the four sections above.

Flag anything that directly impacts the implementation design with **[DESIGN IMPACT]**.

This summary becomes the foundation the Planning phase builds on. The user should review it before proceeding to `/monday-implement:plan`.
