# Research Summary

**Client:** [Client Name] — Operations & Compliance Team  
**Source Platform:** [Source CRM Platform]  
**Target Platform:** monday.com CRM  
**Date:** [Date]  
**Prepared by:** [Your Name]

---

## 1A: monday.com Platform Research

### Column Types & Limitations

| Column Type | Key Behavior | Limitations | [DESIGN IMPACT] |
|---|---|---|---|
| `text` | Single-line, no formatting | No character limit documented, but UI truncates around 500 chars in table view | — |
| `long_text` | Rich text with basic formatting | **2,000 character hard limit.** No bullet/numbering support in forms. Formatting may not carry over to document merge tools. | **[DESIGN IMPACT]** Client's current source CRM "Scope of Work" field has no limit. Users paste 1–3 pages regularly. Recommend file attachment as overflow. |
| `numbers` | Numeric with optional units | No currency symbol formatting per-cell. Currency must be a separate column or baked into column title. | **[DESIGN IMPACT]** Client tracks amounts in USD, EUR, and GBP. Need a companion "Currency" dropdown alongside every amount field. |
| `status` | Color-coded labels | Max ~40 labels per status column. Labels are board-specific (not reusable across boards). | — |
| `dropdown` | Single or multi-select | Max 200 options per dropdown. Supports multi-select mode. Options are board-specific. | **[DESIGN IMPACT]** Client has 33 GL Codes and 22 Department IDs. Both fit within limits. Use dropdown (not status) for large value sets. |
| `auto_number` | Sequential unique IDs | Per-board only. Cannot share a sequence across two boards. Format is configurable (prefix + zero-padded digits). Starting number can be set. | **[DESIGN IMPACT]** Client wants a single agreement number sequence shared across their Agreements and Grants boards. This requires a workaround: either a custom API counter, a Make/Integromat scenario, or consolidating both onto one board with a "Type" column. |
| `connect_boards` | Links items across boards | Max 500 linked items per cell. Two-way linking creates mirror-eligible relationships. | — |
| `mirror` | Read-only reflection of connected item's columns | Cannot be edited. Cannot be used in automations as a trigger. Cannot be filtered in some dashboard widgets. | **[DESIGN IMPACT]** Client wants to see the vendor's email on the agreement record for signature routing. Mirror from the linked Contact works for display, but automation triggers must reference the source board directly. |
| `file` | Attachments | 500MB per file, no limit on number of files per cell. Files are stored in monday.com's infrastructure. | — |
| `formula` | Calculated values | References only columns on the same board. Cannot pull from connected/mirrored boards. Limited function set compared to Excel. | **[DESIGN IMPACT]** "Days Since Submitted" or "SLA Countdown" formulas work within a single board but cannot reference dates from connected boards. |
| `people` | monday.com users | Only licensed users appear. External contacts (vendors, signatories) cannot be assigned. | **[DESIGN IMPACT]** Client's "Vendor Signatory" is an external person — use email column, not people column. |

### Automation Capabilities

| Feature | Details |
|---|---|
| Triggers | Status change, date arrived, item created, column value changed, button clicked, recurring, form submitted |
| Actions | Notify, change status, create item, move item, send email, create update, assign person, trigger integration |
| Conditions | Filter by column value (status, dropdown, people, etc.) |
| Rate Limit | 250,000 actions/month on Pro plan. Enterprise is unlimited. |
| Limitations | Cannot chain automations (automation A triggers automation B). No loops. Cannot read mirror column values as trigger conditions. No "wait" or "delay" step — must use date-based recurring checks instead. |
| Custom automations | Possible via API + external services (Make, Zapier). Not configurable purely within monday.com UI. |

**[DESIGN IMPACT]** Client wants "if the approver hasn't acted within 2 days, send a reminder, then every 2 days after that." This requires a recurring date-based automation checking a "Last Stage Change Date" column, not a single trigger. Build a hidden "Stage Changed On" date column that updates via automation on every status change, then a recurring automation that checks if today minus that date exceeds the threshold.

### Form Builder Capabilities

| Feature | Supported? | Notes |
|---|---|---|
| Conditional sections (show/hide fields) | Yes | Based on dropdown, status, or checkbox values. Limited to one level of nesting. |
| Required fields | Yes | Enforced at submission. |
| File upload | Yes | Appears as file column on the board. |
| Helper text / descriptions | Yes | Per-field descriptions shown below the field label. |
| Links in descriptions | Yes | Markdown links work in field descriptions. |
| Pre-populated fields | No | Forms always start blank. Cannot pre-fill from URL params or connected records. |
| Multi-page forms | No | Single scrollable page only. Use section headers to visually break it up. |
| Submission confirmation | Yes | Customizable "thank you" message. |
| Form = Board | Yes | A monday.com form creates items directly on its parent board. The form's questions become columns. |

**[DESIGN IMPACT]** Client's current source CRM process requires selecting an existing Account before entering agreement details. monday.com forms cannot pre-populate or "search" connected boards. Workaround: add a connect_boards column to the form and instruct users to search/select the existing account. If the account doesn't exist, they must create it on the Accounts board first, then return to the form.

### API & Migration Constraints

| Constraint | Value |
|---|---|
| API rate limit | 60 requests/minute (Pro), 120/min (Enterprise) |
| Bulk import | CSV import wizard available. Max 500 items per import batch. |
| Column creation via API | Supported. Column types set at creation, cannot be changed after. |
| Item creation via API | Supported. Can set column values inline. |
| Pagination | Cursor-based. Max 500 items per page. |

### CRM-Specific Features

monday.com CRM is a product layer on top of Work Management. It includes preconfigured boards for Leads, Contacts, Accounts, Deals, and Activities. Key differences from standard Work Management:

- CRM boards have built-in "merge duplicates" functionality for Contacts and Accounts (only in the CRM product, not available in Service or standard Work Management).
- CRM deals board has a built-in pipeline/funnel view.
- CRM email integration (send/receive emails from within items) requires a paid Email add-on or Enterprise plan.

**[DESIGN IMPACT]** Client is on the monday.com CRM product. The native duplicate detection on Accounts/Contacts partially addresses their concern about duplicate vendor records. However, it only works on exact name matches — it will not catch spelling variations or alternate names. Supplementary email-domain matching via automation is still recommended.

---

## 1B: Client Industry/Organization Research

### Organization Profile

[Client Name] is a nonprofit international development organization based in the U.S. with a satellite office in Europe. They work with 80+ country partners, fund civil society organizations through grants, and contract consultants and vendors for operational needs.

### Regulatory & Compliance Context

| Requirement | Details | Impact on CRM Design |
|---|---|---|
| Federal grant compliance | As a U.S.-based nonprofit disbursing federal funds, the client must comply with federal grant regulations for grants management, including suspension and debarment checks, competitive procurement thresholds, and cost principles. | **[DESIGN IMPACT]** The $25,000 threshold for suspension & debarment checks and competitive bidding is a federal requirement, not an internal policy. It cannot be waived. The CRM must enforce this with conditional logic. |
| Suspension & Debarment checks | For procurements ≥$25,000, organizations must verify vendors are not suspended or debarred by checking federal and international debarment databases. Screenshots required as evidence. | **[DESIGN IMPACT]** Three file upload fields + a checkbox + a date field must appear conditionally when the amount exceeds the threshold. |
| Tax forms (domestic/international) | U.S. requirement: domestic vendors submit W-9, international vendors submit W-8BEN. Only applies to the U.S. entity (not the EU office). | **[DESIGN IMPACT]** File upload field conditionally required when Location = U.S. office. |
| EU procurement rules | The European office follows EU procurement directives with a different threshold for competitive bidding. | **[DESIGN IMPACT]** Threshold logic must account for two different currencies and two different amounts depending on which office is processing the agreement. |
| Audit trail requirements | All agreements, approvals, and supporting documentation must be retrievable for annual external audits. Approver names and dates must be preserved. | **[DESIGN IMPACT]** monday.com's activity log tracks column changes with timestamps and user names. This meets the audit trail requirement without custom development. Supplement with an "Approval History" long_text field that gets auto-populated via automation at each stage change. |

### Common Workflows in Nonprofit Agreement Management

Based on research of similar organizations (international nonprofits managing $10M–$50M in annual grants and contracts):

- **Intake → Multi-tier approval → Document generation → External signature → Execution** is the standard lifecycle. The client's 7-stage contract process and 6-stage grant process are typical.
- Most organizations this size have 15–30 active agreement templates. The client has 6 (4 core + 2 amendment), which is lean.
- "Agreement number" continuity across fiscal years is common. The client's request to start at a specific number suggests ~1,600 prior agreements in the source system.

---

## 1C: Source Platform Research

### Source CRM Data Model → monday.com Mapping

| Source Object | monday.com Equivalent | Migration Notes |
|---|---|---|
| Account | Accounts board item | Direct mapping. Account Types (Organization, Independent Contractor, Nonprofit, etc.) map to a dropdown column. |
| Contact | Contacts board item | Contacts are linked to Accounts via a lookup in the source system. In monday.com, use connect_boards to link Contact items to Account items. Name format in export is "Last, First" — will need to be reformatted. |
| Contract | Agreements board item | Source contracts have an auto-generated Contract Number. monday.com auto_number will generate new IDs; legacy source numbers stored in a separate "Legacy ID" text column. |
| Grant (custom object) | Grants board item | Custom source object. Same migration approach as Contracts. |
| Approval Process | Status column + automations | The source system has a native approval engine with role-based routing. monday.com has no native equivalent — must be rebuilt with status columns, automations, and notifications. |
| Notes & Attachments | File column + long_text | The source system's Notes & Attachments object stores files inline on records. In monday.com, files upload to a file column. Large volumes of historical attachments may need selective migration. |
| Reports & Dashboards | monday.com Dashboards | Source system reports do not migrate. Dashboards must be rebuilt manually in monday.com. |

### Known Source → monday.com Migration Challenges

| Challenge | Details | Mitigation |
|---|---|---|
| Rich text field formatting loss | Rich text fields (scope of work, justifications) lose formatting when exported to CSV. Bullets, bold, and spacing are stripped. | Export as-is, accept formatting loss. Users will re-enter for active agreements. Historical records keep plain text. |
| Lookup relationships → connect_boards | Source lookups reference records by internal ID. monday.com connect_boards requires matching by name or a shared identifier. | Match Contacts to Accounts by Account Name during import. Run a post-import validation to confirm link accuracy. |
| Multi-picklist → dropdown | Source multi-picklist values export as semicolon-separated strings. monday.com dropdowns accept arrays via API but not via CSV import. | Clean multi-picklist values in Excel before import. For CSV import, pick the primary value only. For API import, pass the array. |
| Phone number formatting | Source exports sometimes include Excel formula artifacts (e.g., `=+15551234567` instead of `+15551234567`). | Strip leading `=+` during data cleanup. |
| Account/Contact deduplication | Source system may have near-duplicate records (e.g., alternate spellings of org names). These won't be caught by monday.com's exact-match duplicate detection. | Client to manually review and deduplicate before migration. Provide a list of suspected duplicates (same email domain, similar names). |

### Export Capabilities

- The source system allows export of any object to CSV via Reports or Data Export.
- Client has already exported Accounts (~955 rows) and Contacts (~988 rows) as an Excel file with two tabs.
- File attachments (Notes & Attachments) would require a separate bulk export tool. Deferred to future phase.

---

## 1D: Third-Party Tool Research

### E-Signature Tool A (Market Leader)

| Attribute | Finding |
|---|---|
| monday.com integration | Yes — native marketplace app. |
| Capabilities | Send documents for signature from within monday.com items. Track signature status. Receive signed PDFs back into monday.com file columns. |
| Document merge (template population) | **No.** The monday.com integration does NOT support populating Word/PDF templates with monday.com field data. It only sends pre-built documents for signature. Document merge requires a separate product add-on with no monday.com integration. |
| Pricing | Standard tier ($25/user/month) covers basic e-signature. Document generation is a separate add-on at enterprise pricing. |
| **[DESIGN IMPACT]** | **This tool alone cannot solve the client's document merge + signature workflow.** An additional tool is needed for merge, or the client must manually populate templates before uploading for signature. |

### E-Signature Tool B (Marketplace Alternative)

| Attribute | Finding |
|---|---|
| monday.com integration | Yes — native marketplace app. |
| Capabilities | Document merge (populate Word/PDF templates with monday.com column values) AND e-signature collection in one workflow. Templates are uploaded and mapped to monday.com columns via a visual field mapper. |
| Document merge | **Yes.** Supports merging monday.com column values into Word (.docx) templates. |
| Signature collection | Yes. Embeds signature fields in merged documents. Routes to signers via email. Returns signed PDFs to monday.com file column. |
| Pricing | Included in the marketplace app subscription (~$20/seat/month on the base plan, varies by volume). |
| Limitations | Less mature than market leader. Smaller user community. Template editor is functional but less polished. No API for advanced automation. |
| **[DESIGN IMPACT]** | **This tool appears to be the better fit** for the client's combined merge + signature requirement. It eliminates the need for a separate document generation tool. Recommend a pilot: upload one agreement template, map fields, and test the merge quality before committing to all templates. |

### Accounting System Integration

| Attribute | Finding |
|---|---|
| monday.com integration | No native integration. Third-party connectors exist via Make (Integromat) and Zapier. |
| Key linking field | "Vendor ID" — the unique identifier assigned to each vendor in the accounting system. Currently stored in the source CRM in two legacy formats. |
| Data flow needed | monday.com → Accounting: push new vendor records (Vendor Number, name, address). Accounting → monday.com: pull payment status (future phase). |
| Complexity | **High.** Requires custom integration via Make or the accounting system's API. Multiple legacy ID formats mean the integration must handle format variations. |
| Phase | Future — after core CRM is stable and Vendor Numbers are reliably generating. |
| **[DESIGN IMPACT]** | The Accounts board must store both the Legacy Vendor ID (text field, imported from source) and the new Vendor Number (auto_number, generated by monday.com). The accounting integration will use whichever ID the finance team maps. Design the board with both fields from day one. |

### Document Management System

| Attribute | Finding |
|---|---|
| monday.com integration | **No native integration.** No marketplace app exists. |
| Workaround | Store document links (URLs) in a monday.com link column. Users click through to the DMS to view/edit documents. Two-way sync would require a custom API integration via Make or custom code. |
| **[DESIGN IMPACT]** | If the client uses the DMS as the system of record for documents, monday.com stores only references (links), not the documents themselves. The file column is used for supporting attachments (screenshots, tax forms), not the primary agreement document. This is a significant workflow difference from having everything in one system. |

### Google Drive

| Attribute | Finding |
|---|---|
| monday.com integration | Yes — native integration. Can link Drive folders to boards, embed links, and open files directly. |
| Use case for client | Template storage only. Agreement templates live in a shared Google Drive folder. |
| Integration needed | None for Phase 1. Embed the folder URL as a link in form field descriptions so staff can access templates while filling out requests. |
| Phase | Phase 1 (static link only). Future: potential automation to copy templates into a per-agreement Drive folder. |

---

## Research Methodology

All findings above were gathered via:

- monday.com official documentation (support.monday.com)
- monday.com marketplace app listings and reviews
- E-signature tool product documentation (both options evaluated)
- Accounting system API documentation
- Source CRM data export documentation
- Web searches for known migration issues and platform limitations
- monday.com Community forums for edge case behavior (auto_number, mirror columns, form builder)

Items tagged **[DESIGN IMPACT]** have been incorporated into the implementation spec. The planning phase references this document for all platform capability decisions.
