# monday.com Implementation: Planning Phase

Analyze client documentation and produce a comprehensive monday.com implementation specification that a builder can execute directly.

**Prerequisite**: Run `/monday-implement:research` first. Load `research-summary.md` before starting this phase.

---

**Read and follow `_rules.md`** (canonical critical rules for all phases).

**Read and follow `_mcp-check.md`** (canonical MCP availability check). This phase can proceed without MCP.

**Read `project-config.yaml`** if it exists (from research phase). If not, read `config.md` and ask the user to fill in project variables.

---

## Input Documents

The user will attach some combination of:
- Discovery call transcripts or meeting notes
- Current-state process documentation (e.g., Salesforce workflow guides)
- Emails or messages from the client with requirements, questions, or clarifications
- Data exports (accounts, contacts, reference codes, lookup tables)
- Statements of Work or project scopes
- Screenshots or visual references of existing systems

**Read ALL documents thoroughly before producing any output.** Cross-reference details across documents -- clients often mention requirements in conversation that contradict or extend their written documentation.

Also load the `research-summary.md` from the research phase and use its findings (especially **[DESIGN IMPACT]** items) to inform the spec.

---

## Analysis Framework

Before writing the spec, internally work through these questions:

### Workflow Discovery
- What are the distinct workflows/processes? (e.g., contracts, grants, amendments, procurement)
- What entity types exist? (e.g., accounts, contacts, contracts, grants)
- How do entities relate to each other? (e.g., an Account has many Contacts; a Contract belongs to one Account)
- What is the lifecycle/stage progression for each workflow?
- Who are the actors at each stage? (submitters, reviewers, approvers, signers)
- What are the approval chains and who specifically approves at each step?

### Field & Data Discovery
- What fields exist in the current system? Categorize each as:
  - **Account-level**: stored once per vendor/organization
  - **Transaction-level**: entered per contract/grant
  - **Lookup/reference data**: dropdowns sourced from a master list
  - **Computed/auto-generated**: agreement numbers, statuses
  - **Conditional**: only required when certain thresholds are met
- What are the dropdown values for each field? Pull exact values from the documents.
- What fields are required vs. optional? Under what conditions?
- Are there character limits, format constraints, or validation rules mentioned?

### Conditional Logic & Business Rules
- What thresholds trigger additional requirements? (e.g., dollar amounts requiring extra approvals, checks, or documentation)
- What compliance checks are required? (e.g., suspension/debarment, competitive bidding)
- What notifications should fire and when?
- What automated reminders or escalations were discussed?
- Are there duplicate-detection or data integrity rules?

### Integration & Document Management
- What signature/document tools are in use or under consideration? (DocuSign, GetSign, PandaDoc, etc.)
- Is document merge (populating templates from CRM fields) required?
- What templates exist and how are they categorized?
- What file attachments are expected per record?
- Are there links to external systems (Google Drive, accounting software, etc.)?

### Data Migration
- What data needs to be migrated for launch? (accounts, contacts, historical records, reference tables)
- What are the volumes? (row counts per entity)
- Are there known data quality issues? (duplicate formats, inconsistent naming, legacy ID formats)
- What auto-numbering or ID generation schemes need to carry forward or be newly created?

### Future Phases
- What was explicitly deferred or marked as a future enhancement?
- What integrations are planned but not yet scoped? (accounting systems, reporting tools, AI/automation)
- What "nice to have" features did the client mention in conversation?

---

## Output Specification

Produce the following sections **in this exact order**:

### 1. EXECUTIVE SUMMARY
- One paragraph: what system is being replaced, what monday.com will do, and the launch target.
- Bullet list of key constraints or risks identified from the documents.

### 2. WORKSPACE & BOARD ARCHITECTURE
For each board, specify:
- **Board name** and purpose
- **Board type** (main board, private, shareable)
- **Relationship to other boards** (connected boards, mirrored columns)
- **Groups** (if the board should be segmented, e.g., by region or status)
- **Associated form** (if the board is created via a form, note this -- the form creates the board)

Present this as a clear hierarchy showing how boards relate to each other.

### 3. COLUMN SPECIFICATIONS (per board)
For each board, produce a table with these columns:

| Column Name | Column Type | Required | Form-Facing | Dropdown Values / Configuration | Conditional Logic | Notes |
|-------------|-------------|----------|-------------|--------------------------------|-------------------|-------|

- **Form-Facing**: Yes/No -- indicates whether this column should be created as part of the form (Yes) or added after the form as a backend-only column (No). Backend-only examples: auto_number, connect_boards, mirror, formula, internal status columns.
- Column types must use exact monday.com column types: `text`, `long_text`, `numbers`, `status`, `dropdown`, `date`, `people`, `email`, `phone`, `link`, `file`, `checkbox`, `country`, `formula`, `auto_number`, `connect_boards`, `mirror`, `rating`, `timeline`, `time_tracking`, `tags`, `color_picker`, `location`.

Flag any field where monday.com has a known limitation vs. the current system (e.g., long_text has a 2000-character limit; rich text formatting differences).

### 4. FORMS SPECIFICATION
For each intake form:
- **Form name** and which board it creates/feeds
- **Field order** (the sequence users fill out -- these become the initial board columns)
- **Conditional sections** (fields that show/hide based on answers)
- **Helper text / descriptions** to include on each field
- **Required vs. optional** designation
- **File upload fields** and what documents are expected
- **Links or references** to embed (e.g., Google Drive folders, external sites for compliance checks)

### 5. APPROVAL WORKFLOWS & STAGE MANAGEMENT
For each workflow:
- **Stage names** in order
- **Who reviews/approves** at each stage
- **What triggers the transition** to the next stage (manual button, automation, status change)
- **What happens on rejection/decline** at each stage
- **Permission changes** per stage (e.g., submitter loses edit access after submission)
- **Notification rules** per stage (who gets notified, via what channel)

### 6. AUTOMATIONS
For each automation:
- **Trigger**: what event fires it
- **Condition**: any filters or criteria
- **Action**: what happens
- **Automation type**: native monday.com automation, custom integration, or requires third-party tool

Organize by category: notifications, status changes, reminders/escalations, integrations, data validation.

### 7. INTEGRATIONS
For each integration:
- **Tool**: (DocuSign, GetSign, Sage Intacct, Google Drive, etc.)
- **Purpose**: what it does in the workflow
- **Data flow**: what fields map from monday.com to the external tool and back
- **Document templates**: list each template and the merge fields it requires
- **Phase**: when this integration should be implemented (launch vs. future)

Reference the research phase findings for each tool's actual monday.com integration capabilities.

### 8. DATA MIGRATION PLAN
- **Entity mapping**: source system field -> monday.com board + column
- **Volume**: row counts per entity
- **ID/Number handling**: how legacy IDs are preserved and new auto-numbering schemes work alongside them
- **Data cleanup**: known issues to resolve before import
- **Import method**: recommended approach (CSV import, API, third-party tool)
- **Validation steps**: how to verify the migration was successful

### 9. REFERENCE DATA & LOOKUP TABLES
For each set of reference data (GL codes, department IDs, revenue codes, etc.):
- **Full list of values** as they should appear in dropdowns
- **Which board(s) and column(s)** use this data
- **Maintenance plan**: who updates these values and how

### 10. REPORTING & DASHBOARDS
- **Dashboard name** and audience
- **Widgets**: what visualizations are needed (chart type, data source, filters)
- **KPIs**: what metrics matter (contracts in progress, average approval time, spend by department, etc.)
- **Phase**: launch vs. future

### 11. FUTURE PHASE ROADMAP
For each deferred item:
- **Feature/integration description**
- **Dependencies**: what needs to be in place first
- **Estimated complexity**: low / medium / high
- **Priority**: as indicated by the client

### 12. OPEN QUESTIONS & RISKS
- Any ambiguities, contradictions, or gaps found across the documents
- Decisions that need client confirmation before building
- monday.com platform limitations that affect the design (reference research phase findings)
- Risks to the launch timeline

---

## Formatting Rules
- Use tables for structured data (columns, dropdown values, automations).
- Use exact monday.com terminology and column type names.
- When quoting dropdown values, list them exactly as found in the source documents.
- Flag every assumption with **[ASSUMPTION]** so the builder can verify with the client.
- Flag every known monday.com limitation with **[PLATFORM LIMITATION]** so the builder can plan workarounds.
- Cross-reference document sources when a requirement comes from a specific document (e.g., "per the discovery call transcript" or "per Sandy's email dated [date]").

## Critical Planning Rules
1. Do NOT invent requirements. Every field, automation, and workflow must trace back to something in the attached documents.
2. Do NOT skip dropdown values. If a document lists the options for a field, reproduce them all.
3. Do NOT assume monday.com can do something without flagging it. If unsure whether monday.com natively supports a feature, mark it as **[VERIFY PLATFORM CAPABILITY]**.
4. DO capture "nice to have" items mentioned casually in transcripts or emails -- these are future-phase gold.
5. DO flag contradictions between documents (e.g., a process doc says one thing but the client said something different in the call).

---

## Phase Completion

Save the spec to the project directory as `implementation-spec.md` and confirm with the user before proceeding to `/monday-implement:execute`.

## Quick Reference: monday.com Platform Limitations

Common limitations to watch for during planning:

| Limitation | Workaround |
|-----------|------------|
| `long_text` max 2000 chars | Split into multiple fields or use docs |
| No native conditional field visibility on boards | Use form conditions only; board columns always visible |
| Status column max ~40 labels | Use dropdown for large value sets |
| Mirror columns are read-only | Use automations to sync writable copies |
| No native calculated fields across boards | Use formula + mirror, or custom integrations |
| Forms cannot pre-populate fields | Use API or automation post-submission |
| No native approval workflow engine | Build with status columns + automations + notifications |
| connect_boards linked items limit **[VERIFY PLATFORM CAPABILITY]** | Paginate or restructure for high-volume relationships -- verify current limit in research phase |
| Automations have a monthly action quota | Monitor usage on Pro/Enterprise plans |
| No native duplicate detection | Use integrations or custom API checks |
| Subitems have limited column types | Design around main items where possible |
| Dashboard widgets have limited cross-board filtering | Use board views or separate dashboards |
