# monday.com Implementation: Research Phase

Conduct foundational research before analyzing client documents. This ensures the implementation spec is grounded in current platform realities, not assumptions.

---

**Read and follow `_rules.md`** (canonical critical rules for all phases).

**Read and follow `_mcp-check.md`** (canonical MCP availability check). This phase can proceed without MCP.

**Read `config.md`** and ask the user to fill in project variables if not already done. Save to `project-config.yaml`.

---

## Research Areas

Read the user's attached documents first to identify what needs to be researched, then conduct web searches for each area below.

**IMPORTANT**: Always search for the most recent and up-to-date information. monday.com updates features frequently. Append the current year to search queries (e.g., "2026") and prefer results from the last 6 months. If you find conflicting information across sources, use the most recent source and flag the conflict with **[VERIFY PLATFORM CAPABILITY]**.

### 1A: monday.com Platform Research

Research and document using queries like these (adapt to project specifics):

- `monday.com column types and limitations [current year]`
- `monday.com long_text column character limit [current year]`
- `monday.com form builder conditional logic capabilities [current year]`
- `monday.com automation limits per board rate limits [current year]`
- `monday.com API rate limits batch operations [current year]`
- `monday.com connect_boards column linked items limit [current year]`
- `monday.com auto-number column format configuration [current year]`
- `monday.com CRM product features lead contact deal [current year]`

Specifically document:

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
