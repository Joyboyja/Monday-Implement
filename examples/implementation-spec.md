# Implementation Specification

**Client:** [Client Name] — Legal & Compliance Team  
**Implementation:** Agreement Lifecycle Management System  
**Date:** [Date]  
**Version:** 1.0 — Draft for Client Review  
**Source Documents:** Client's workflow document (v3), kickoff call transcript & summary  
**Prepared by:** [Your Name]

---

## 1. EXECUTIVE SUMMARY

[Client Name]'s legal and compliance team (15 members) currently manages agreement reviews — NDAs, term deals, master agreements, amendments, and side letters — manually via a shared distribution email. One paralegal triages incoming requests and assigns them to in-house counsel or, when needed, outside law firms. There is no centralized intake, no lifecycle visibility, no workload tracking, no SLA measurement, and document storage in [Document Management System] sits entirely outside the workflow. monday.com will replace this manual process with a centralized legal intake and agreement lifecycle management system, providing real-time workload visibility across 15 users, SLA monitoring, [Document Management System]-connected workflow items, and a self-sufficient in-house expert (the project lead) through Champion Training. The target timeline is 6-8 weeks (30 hours of consulting), with the project lead as the primary workflow expert and a backup admin.

### Key Constraints & Risks

- [Client Name] signed up for the **monday.com Service** product (not Work Management, which a sister subsidiary uses separately). **[VERIFY PLATFORM CAPABILITY]** Confirm all features below (especially workload widget, time tracking, AI blocks, email integration) are available on the Service product tier. If not, the team may need a product change.
- **[Document Management System] integration is critical** — [Document Management System] is the system of record for documents. monday.com stores links and process status only. No native monday.com ↔ [Document Management System] integration exists. **[VERIFY PLATFORM CAPABILITY]**
- **AI extraction** (governing law, language, page count, counterparty, etc.) from email attachments is a core requirement but relies on monday.com AI blocks or a custom integration. **[VERIFY PLATFORM CAPABILITY]**
- **Email intake automation** (auto-creating items from the shared mailbox) requires the Outlook/email integration. Confirm compatibility with [Client Name]'s Outlook environment.
- **Automations cannot be built via API** — all automations must be configured manually in the monday.com browser UI.
- The team has **very limited monday.com experience** — training is critical for adoption.
- Project lead and backup admin will be out for some days in the implementation period, which may extend the timeline toward 8 weeks.

---

## 2. WORKSPACE & BOARD ARCHITECTURE

```
[Workspace Name]
├── Board 1: Agreements — Lifecycle (Internal)
│   ├── Type: Main board (restricted to legal team)
│   ├── Purpose: Full agreement lifecycle from intake to close
│   │   ├── Columns: status, review tracking, SLA, metadata
│   │   ├── Groups: Intake | Internal Review In Progress |
│   │   │   Counterparty Negotiation | Execution Prep |
│   │   │   Post-Execution | Archived
│   │   └── Automations: notifications, status changes, reminders
│   └── Board 2: Agreement Status — Client View (Shared)
│       ├── Type: Shareable board (viewer-only for commercial contacts)
│       ├── Purpose: Agreement status visibility for commercial teams
│       ├── Relationship: Connected to Internal Board via connect_boards
│       └── Columns: Mirrors selected columns from Internal Board (read-only)
│
├── [Optional] Training Board: onboarding and practice for all 15 users
│   └── Purpose: Track training completion for all team members
```

**Board Relationship:** The Shared Board mirrors selected columns from the Internal Board via a connect_boards + mirror column architecture. Commercial contacts see only the mirrored subset. Internal notes, review statuses, and operational fields are never exposed.

---

## 3. COLUMN SPECIFICATIONS

### Board 1: Agreements — Lifecycle (Internal)

#### Core Columns (visible on board view)

| # | Column Name | Column Type | Required | Dropdown Values / Configuration | Conditional Logic | Notes |
|---|------------|-------------|----------|-------------------------------|-------------------|-------|
| 1 | Item Name | (native) | Yes | Format: `Counterparty — [Client Entity] — Short Code — Document Type (DD.MON.YY)` | — | Auto-composed where possible |
| 2 | Date Received | date | Yes | Auto-populated at intake | — | — |
| 3 | Status | status | Yes | See §5 for full status values | — | Single source of truth for lifecycle stage |
| 4 | Assigned Internal Counsel | people | No | — | Set when routed (Step 6) | Primary legal reviewer |
| 5 | Additional Internal Review Teams | dropdown (multi-select) | No | Tax · Compliance · Credit · Risk · Regulatory · Commercial · Operations · Specialist | Triggers corresponding review status columns | Per client's doc |
| 6 | Priority | status | No | Normal · High | Required if Pages > 2 | [ASSUMPTION] Using status rather than label for color coding |
| 7 | Deadline | date | No | — | Required if Pages > 2 | Target completion date |
| 8 | Pages | numbers | No | — | Auto-populated via AI extraction | Document length |
| 9 | Document Type | dropdown | Yes | Master agreement · Amendment · NDA · Side letter · (add others per client) | Determines coding route at post-execution | Per client's doc |
| 10 | Transaction Type | dropdown | No | Pipeline · Above Ground Storage · Cavern Storage · Transport · Lease · Charter · Sale/Purchase · Truck & Rail | — | [OPEN QUESTION] Confirm full list of transaction types |
| 11 | Product Matrix | dropdown | Yes | Category A · Category B · Category C · Category D · Category E · Category F · (others per client) | — | Product category the commercial team belongs to |
| 12 | Document Title / Desc. | text | No | — | — | Exact wording from document title |
| 13 | Counterparty | dropdown | Yes | Pull full list from [CRM System] | — | [OPEN QUESTION] Need export of counterparty list from [CRM System] |
| 14 | [Client Entity] | dropdown | Yes | Need full entity list from [ERP System] | — | [OPEN QUESTION] Need access to [ERP System] for full entity list |
| 15 | Commercial Client / Requestor | text | Yes | — | Parsed from email/form | Person who submitted the request |
| 16 | [DMS] Link | link | No | — | Prompt to add if blank after set period | Working folder or document link |
| 17 | Supporting Docs | file | No | — | — | Underlying documents, prior amendments, etc. |
| 18 | Last Activity Date | date | No | — | Auto-updated on any status change or activity | — |
| 19 | Notes | long_text | No | — | — | Free-text notes, special instructions. **[PLATFORM LIMITATION]** long_text has 2000 char limit in some views |

#### Internal Review Status Columns

These columns track parallel review work. They become relevant/visible when the corresponding team is selected in "Additional Internal Review Teams."

| # | Column Name | Column Type | Values | Notes |
|---|------------|-------------|--------|-------|
| 20 | Legal Review Status | status | Not started · In progress · Complete | Always visible |
| 21 | Credit Review Status | status | Not started · In progress · Complete | Conditional on Credit selected |
| 22 | Tax Review Status | status | Not started · In progress · Complete | Conditional on Tax selected |
| 23 | Compliance Review Status | status | Not started · In progress · Complete | Conditional on Compliance selected |
| 24 | Risk Review Status | status | Not started · In progress · Complete | Conditional on Risk selected |
| 25 | Regulatory Review Status | status | Not started · In progress · Complete | Conditional on Regulatory selected |
| 26 | Outside Counsel Review Status | status | Not sent · In progress · Comments received · Complete | — |
| 27 | Commercial Review Status | status | Not started · In progress · Complete | Per client's doc question about adding this |
| 28 | Operations Review Status | status | Not started · In progress · Complete | Per client's doc question about adding this |
| 29 | Specialist Review Status | status | Not started · In progress · Complete | — |

**[PLATFORM LIMITATION]** monday.com does not natively support conditional column visibility based on another column's value. Workaround options: (a) use column descriptions to indicate when each is relevant, (b) collapse unused columns, or (c) use board views filtered by review team.

#### Time Tracking / SLA Columns

| # | Column Name | Column Type | Configuration | Notes |
|---|------------|-------------|---------------|-------|
| 30 | Internal Counsel Time Tracking | status or formula | On track · At risk · Overdue (or date/day counter) | [OPEN QUESTION] Client's doc offers two approaches — confirm preference |
| 31 | Credit Time Tracking | status or formula | On track · At risk · Overdue (or date/day counter) | Same question |
| 32 | Outside Counsel Time Tracking | status or formula | On track · At risk · Overdue (or date/day counter) | Same question |
| 33 | Days Since Last Activity | formula | `MINUS(TODAY(),{Last Activity Date})` | Auto-calculated |

#### Item-Level Metadata Fields

These sit in the item view/card, not as prominent board columns. Implementation options: subitems, item updates, or less-prominent columns scrolled right.

**Intake / AI Extraction / Routing Metadata:**

| Field | Type | Notes |
|-------|------|-------|
| Language Detected | text or dropdown | AI-extracted at intake |
| Governing Law | text or dropdown | AI-extracted at intake |
| Governing Law AI Confidence | status | High · Medium · Low |
| Governing Law Sense Check | status | Pending · Confirmed · Corrected |
| Auto-routing Flag | status | Route to [Office A] · Route to [Office B] · Route to [Office C] · Route to [Office D] · [HQ] Review |

**Priority / Timing Support:**

| Field | Type | Notes |
|-------|------|-------|
| Receipt Acknowledgment Sent? | checkbox | — |
| Priority / Deadline Request Sent? | checkbox | For >2 page agreements |
| Internal Routing Date | date | Consider promoting to board column |
| Internal Review Start Date | date | Consider promoting to board column |

**Counterparty / KYC Checks:**

| Field | Type | Notes |
|-------|------|-------|
| Counterparty in [CRM System]? | checkbox | Operational check — per client's doc |
| Counterparty Active? | checkbox | Same as above |
| KYC Reviewer Assigned | people | Optional |
| KYC Check Date | date | Optional |

**Document Management / Filing:**

| Field | Type | Notes |
|-------|------|-------|
| File Naming Compliance | status | OK · Needs fix — format checker |
| Lawyer Routing Email Sent? | checkbox | — |
| Non-Lawyer Routing Email Sent? | checkbox | — |
| Supporting Docs Attached to Routing Email? | checkbox | — |
| DocuSign Envelope Link | link | Native if DocuSign integration available |
| Executed PDF Saved to [Archive System]? | checkbox | — |
| Executed PDF Saved to [DMS]? | checkbox | — |
| Final Word Execution Saved to [DMS]? | checkbox | — |

**Outside Counsel Tracking:**

| Field | Type | Notes |
|-------|------|-------|
| Sent to Outside Counsel? | checkbox | — |
| Outside Counsel Name / Firm | text | — |
| Date Sent to Outside Counsel | date | — |
| Outside Counsel Follow-up Date | date | Optional |
| Response Received from Outside Counsel | date | Optional |

**Execution / Sign-off:**

| Field | Type | Notes |
|-------|------|-------|
| Final Internal Sign-off Requested? | checkbox | — |
| Final Internal Sign-off Received? | checkbox | — |
| Execution Method | status | DocuSign · Wet ink |

**Post-Execution / Coding:**

| Field | Type | Notes |
|-------|------|-------|
| Coding Route | status | route_to_vendor · route_to_contracts_[location] · None |
| Coding Complete? | checkbox | — |
| Closed Date | date | Also a board column candidate |

**Execution Readiness Checklist:**

Implement as subitems or a checklist within the item:

1. Notice info updated
2. Signature blocks correct
3. Draft labels / watermarks removed
4. No brackets / missing data
5. Final counsel approval obtained
6. All exhibits and attachments assembled & included in signing version

---

### Board 2: Agreement Status — Client View (Shared)

| Mirrored Column | Source (Internal Board) | Description |
|----------------|----------------------|-------------|
| Agreement Name | Item Name | Counterparty — [Client Entity] short code — Document Type |
| Counterparty | Counterparty column | Full name |
| [Client Entity] | [Client Entity] column | Applicable entity |
| Commercial Contact | Commercial Client / Requestor | Person who submitted |
| Assigned Internal Contacts | Assigned Internal Counsel | Lawyer / reviewer |
| Review Status | Status column | Main lifecycle status |
| Target Date / Deadline | Deadline column | — |
| Last Update | Last Activity Date | Most recent activity |

**Architecture:** One connect_boards column linking each shared item to its internal counterpart. Eight mirror columns reflecting the fields above. No other columns exposed.

[ASSUMPTION] Commercial contacts will be added as guests to this shared board with viewer-only permissions.

---

## 4. FORMS SPECIFICATION

### Form 1: Agreement Request Intake Form

**Board:** Agreements — Lifecycle (Internal)  
**Purpose:** Centralized intake replacing the shared email distribution list. Commercial teams and legal staff submit new agreement requests here.

| Order | Field | Column Mapping | Required | Helper Text / Description | Conditional |
|-------|-------|---------------|----------|--------------------------|-------------|
| 1 | Commercial Client / Requestor Name | Commercial Client / Requestor | Yes | "Your full name" | — |
| 2 | Counterparty | Counterparty | Yes | "Name of the external counterparty" | — |
| 3 | [Client Entity] | [Client Entity] | Yes | "Which [Client] entity is this agreement for?" | — |
| 4 | Product Matrix | Product Matrix | Yes | "Which product category does this relate to?" | — |
| 5 | Document Type | Document Type | Yes | "Select the type of agreement" | — |
| 6 | Transaction Type | Transaction Type | No | "If applicable, select the transaction type" | — |
| 7 | Document Title / Description | Document Title / Desc. | No | "Exact title or brief description of the document" | — |
| 8 | Priority | Priority | No | "Normal unless deadline-driven" | Show only if user indicates urgency, or always show |
| 9 | Deadline | Deadline | No | "When does this need to be completed by?" | — |
| 10 | Agreement Document(s) | Supporting Docs (file) | Yes | "Upload the agreement and any supporting documents (prior amendments, master agreement, schedules)" | — |
| 11 | Special Instructions / Notes | Notes | No | "Any additional context, special instructions, or known issues" | — |

[ASSUMPTION] The form will be shared via a link that commercial teams can bookmark, replacing the email submission process.

[OPEN QUESTION] Should there be a separate intake form for external law firm submissions, or do they come through the same channel?

---

## 5. APPROVAL WORKFLOWS & STAGE MANAGEMENT

### Main Lifecycle Statuses (Status Column)

| # | Status Value | Color | Meaning | Group |
|---|-------------|-------|---------|-------|
| 1 | New — Not triaged | Light gray | Item just created; not yet reviewed by coordinator | Intake |
| 2 | Gathering requirements | Light blue | Intake admin in progress — collecting priority, deadline, KYC info, docs, routing | Intake |
| 3 | With internal review teams | Yellow | Non-legal parallel reviewers (Credit, Tax, Compliance, Risk, Regulatory) are the active dependency | Internal Review In Progress |
| 4 | With internal counsel | Orange | Legal is the primary active reviewer | Internal Review In Progress |
| 5 | With outside counsel | Purple | External law firm review is underway | Internal Review In Progress |
| 6 | With counterparty | Blue | Redline/comments/draft sent to the other side; awaiting response | Counterparty Negotiation |
| 7 | Finalization | Dark blue | Execution prep, final sign-off, and/or signature coordination in progress | Execution Prep |
| 8 | Executed | Green | All parties have signed | Post-Execution |
| 9 | Saved to [Archive] | Dark green | Executed PDF has been filed in [Archive System] | Post-Execution |
| 10 | Closed | Black/Dark gray | All filing, coding, and closeout actions are complete | Archived |

[ASSUMPTION] "Finalization" is used in place of "Signature process" mentioned in Steps 9-10 of the client's doc — the client's status table in §3 does not include "Signature process" as a distinct status. Confirm which label to use.

### Step-by-Step Workflow

**Step 1 — Intake (email arrives or form submitted)**
- **Trigger:** Email to shared mailbox, form submission, or manual intake
- **Actions:** Create new item in Intake group; auto-populate Date Received, Commercial Client, Counterparty, Document Type, Document Title, [Client Entity], Supporting Docs
- **Status:** → `New — Not triaged`
- **AI Extraction:** If attachment exists, extract Pages, Language Detected, Governing Law, Governing Law AI Confidence, Auto-routing Flag
- **Set:** Governing Law Sense Check → `Pending`

**Step 2 — Acknowledge receipt & gather missing info**
- **Decision logic:**
  - If Pages > 2 AND (Priority OR Deadline is blank) → Send Template 2 (priority/deadline request); Status → `Gathering requirements`
  - If Pages > 2 AND both Priority and Deadline populated → Send Template 1 (acknowledgment); Status → `Gathering requirements`
  - If Pages < 2 → Send Template 1 (acknowledgment); Status → `Gathering requirements`
- **Who:** Agreements coordinator (paralegal)
- **Rejection:** N/A (intake step)

**Step 3 — Office routing based on governing law**
- **Logic:** If Governing Law ∈ {[Jurisdiction A], [Jurisdiction B], [Jurisdiction C], [Jurisdiction D]} → set Auto-routing Flag, assign relevant office/reviewer
- **Human check:** Agreements user performs sense check (Confirmed / Corrected)
- **Status:** Remains `Gathering requirements` if prerequisites incomplete; transitions to `With internal review teams` or `With internal counsel` depending on routing

**Step 4 — Link to [DMS] & confirm document record**
- **Actions:** Populate [DMS] Link, check File Naming Compliance, ensure document filed before review begins
- **Status:** Remains `Gathering requirements` if setup incomplete

**Step 5 — Check counterparty in [CRM System] & gather supporting docs**
- **If counterparty NOT in [CRM System] or NOT active:** Route to KYC, set KYC Review Status → In progress, Status → `With internal review teams`
- **If counterparty active:** Gather supporting materials, add to Supporting Docs

**Step 6 — Route internally (counsel + review teams)**
- **Actions:** Assign Internal Counsel and any Additional Internal Review Teams
- **Routing email:** Auto-populated table with: Legal Team Member, Deadline, Pages, Commercial Client, Document Type/Title, Counterparty, [Client Entity], Review Teams, Underlying Documents, Notes
- **Routing method:**
  - Lawyers → [DMS] link (they have access); attach supporting docs
  - Other review teams → Attach document itself (no [DMS] access), plus underlying docs
- **Status:** → `With internal counsel` (legal primary) or `With internal review teams` (non-legal dependency)

**Step 7 — Track workload, responsiveness, blockers, outside counsel**
- **Monitoring:** Days Since Last Activity, time tracking columns
- **Outside counsel:** If Sent to Outside Counsel = Yes → Status → `With outside counsel`
- **Automations:** Deadline reminders (3 days before), inactivity alerts (7+ days)

**Step 8 — Redline / counterparty negotiation loop**
- **Legal sends redline** → Status → `With counterparty`
- **Counterparty returns draft** → Status → `With internal counsel` or `With internal review teams`
- **Version control:** Stays in Word/[DMS]; monday.com tracks where the draft sits

**Step 9 — Execution readiness**
- **Checklist:** Notice info, signature blocks, draft labels, brackets, counsel approval, exhibits
- **Confirm execution method:** DocuSign or Wet ink
- **Status:** → `Finalization`

**Step 10 — Route for execution & monitor signature**
- Send via DocuSign or wet ink
- On full execution → Status → `Executed`

**Step 11 — Post-execution filing & coding**
- **Filing tasks:** Save executed PDF to [Archive System], save to [DMS], save final Word to [DMS]
- **On PDF saved to [Archive]** → Status → `Saved to [Archive]`
- **Coding rules:**
  - Master Agreement → route to vendor coding
  - Non-master long-term agreement (attached to entity ID) → route to Contracts [Location]
  - Other → None
- **On all closeout complete** → Status → `Closed`; populate Closed Date

---

## 6. AUTOMATIONS

All automations must be configured manually in the monday.com browser UI — they cannot be built via API.

### Notifications

| # | Trigger | Condition | Action | Type |
|---|---------|-----------|--------|------|
| 1 | Item created | — | Notify agreements coordinator | Native |
| 2 | Status changes to any value | — | Notify assigned counsel + coordinator | Native |
| 3 | Assigned Internal Counsel set | — | Send routing email with summary table and [DMS] link | Custom (email template) |
| 4 | Additional Internal Review Teams changed | Team added | Notify relevant review team member(s), send routing email with attachments | Custom (email template) |
| 5 | Sent to Outside Counsel = Yes | — | Notify coordinator | Native |
| 6 | Outside Counsel Review Status → Comments received | — | Notify assigned counsel | Native |
| 7 | Final Internal Sign-off Received = Yes | — | Notify coordinator to initiate execution | Native |

### Status Changes

| # | Trigger | Condition | Action | Type |
|---|---------|-----------|--------|------|
| 8 | Item created (from form/email) | — | Set Status → New — Not triaged | Native |
| 9 | Acknowledgment sent | Pages > 2 and Priority + Deadline populated; OR Pages ≤ 2 | Set Status → Gathering requirements | Native |
| 10 | Assigned Internal Counsel set | — | Set Legal Review Status → In progress; Status → With internal counsel | Native |
| 11 | Review team assigned | — | Set corresponding review status → In progress | Native |
| 12 | Sent to Outside Counsel = Yes | — | Set Outside Counsel Review Status → In progress; Status → With outside counsel | Native |
| 13 | Executed PDF Saved to [Archive] = Yes | — | Status → Saved to [Archive] | Native |
| 14 | Coding Complete = Yes AND all filing checkboxes = Yes | — | Status → Closed; populate Closed Date | Native |

### Reminders / Escalations

| # | Trigger | Condition | Action | Type |
|---|---------|-----------|--------|------|
| 15 | Recurring (daily check) | Deadline exists AND today = Deadline − 3 days AND status is internal review | Notify owner/counsel | Native |
| 16 | Recurring (daily check) | Days Since Last Activity > 7 | Notify owner to follow up | Native |
| 17 | Recurring (daily check) | [DMS] Link blank AND item age > [configurable threshold] | Notify coordinator | Native |
| 18 | Recurring (daily check) | Status = With counterparty AND no update after X days | Notify owner to follow up | Native |
| 19 | Recurring (daily check) | Status = Finalization AND no update after 24 hours | Reminder to follow up on execution | Native |

### Data Validation

| # | Trigger | Condition | Action | Type |
|---|---------|-----------|--------|------|
| 20 | Item created | Pages > 2 AND (Priority OR Deadline blank) | Flag item; send Template 2 | Custom |
| 21 | File naming check | File name doesn't match template | Set File Naming Compliance → Needs fix; notify coordinator | Custom / **[VERIFY PLATFORM CAPABILITY]** |

---

## 7. INTEGRATIONS

### 7a. [Document Management System]

| Attribute | Detail |
|-----------|--------|
| **Tool** | [Document Management System] |
| **Purpose** | System of record for all legal documents. monday.com stores links only. |
| **Data flow** | monday.com → [DMS]: link column populated manually or via integration. [DMS] → monday.com: no direct data flow (link-based). |
| **Phase** | Launch (link column). Deep integration (auto-populating links, file naming validation) is Phase 2. |

**[VERIFY PLATFORM CAPABILITY]** There is no native monday.com ↔ [DMS] integration. Options: (a) manual link entry, (b) custom integration via Make/Integromat, (c) [DMS] API. Confirm with [Client Name] IT.

### 7b. DocuSign

| Attribute | Detail |
|-----------|--------|
| **Tool** | DocuSign |
| **Purpose** | Digital signature execution for agreements |
| **Data flow** | monday.com → DocuSign: trigger envelope from item. DocuSign → monday.com: envelope completion status updates Status → Executed. |
| **Templates** | TBD — depends on [Client Name]'s existing DocuSign templates |
| **Phase** | Launch (basic link tracking). Native integration Phase 2. |

**[VERIFY PLATFORM CAPABILITY]** monday.com has a DocuSign integration app — confirm it's available on the Service product tier and meets [Client Name]'s requirements.

### 7c. Microsoft Outlook (Email Integration)

| Attribute | Detail |
|-----------|--------|
| **Tool** | Microsoft Outlook |
| **Purpose** | Auto-create items from shared mailbox inbox; send templated emails from items |
| **Data flow** | Outlook → monday.com: parse email headers/body/attachments to create items. monday.com → Outlook: send acknowledgments, routing emails, reminders. |
| **Phase** | Launch |

### 7d. Microsoft Teams / OneDrive / SharePoint

| Attribute | Detail |
|-----------|--------|
| **Tool** | Teams, OneDrive, SharePoint |
| **Purpose** | Notifications via Teams; potential document access via OneDrive/SharePoint where [DMS] is not used |
| **Phase** | Phase 2 — confirm with [Client Name] which of these are priorities vs. nice-to-haves |

### 7e. [CRM System] (Counterparty System)

| Attribute | Detail |
|-----------|--------|
| **Tool** | [CRM System] |
| **Purpose** | Counterparty master data — used to populate Counterparty and [Client Entity] dropdowns, and for KYC checks |
| **Data flow** | One-time or periodic export from [CRM System] → monday.com dropdown values |
| **Phase** | Launch (static export); Phase 2 (live integration) |

### 7f. AI Extraction

| Attribute | Detail |
|-----------|--------|
| **Tool** | monday.com AI blocks or custom AI integration |
| **Purpose** | Auto-extract from intake emails/attachments: Language, Governing Law, Pages, Urgency, Deadline, Commercial Client, Document Title/Type, Counterparty, [Client Entity] |
| **Phase** | Launch (basic extraction where available); Phase 2 (full extraction suite) |

**[VERIFY PLATFORM CAPABILITY]** Confirm which AI extraction capabilities are available natively in monday.com vs. requiring a custom Make/Zapier integration with an AI service.

---

## 8. DATA MIGRATION PLAN

### Entity Mapping

| Entity | Source | monday.com Board | Volume | Notes |
|--------|--------|-----------------|--------|-------|
| Counterparty list | [CRM System] export | Internal Board → Counterparty dropdown | TBD | [OPEN QUESTION] Need export |
| [Client Entity] list | [ERP System] export | Internal Board → [Client Entity] dropdown | TBD | [OPEN QUESTION] Need export |
| Product Matrix values | Client's document | Internal Board → Product Matrix dropdown | ~8-10 values | Per client's doc |
| Document Type values | Client's document | Internal Board → Document Type dropdown | ~5+ values | Per client's doc |
| Transaction Type values | Client's document | Internal Board → Transaction Type dropdown | ~8+ values | Per client's doc |
| Historical agreements | TBD | Not scoped for launch | TBD | [OPEN QUESTION] Is there a backlog of open agreements to import? |

### Import Method

- **Dropdown reference data:** CSV import or manual entry for static lists
- **Counterparty/Entity lists:** CSV import from [CRM System] export
- **Historical items:** Not scoped for Phase 1 unless client requests

### Validation Steps

- Confirm all dropdown values match source data exactly
- Test form submissions create items with correct column mappings
- Verify mirror columns on Shared Board reflect Internal Board data
- Test automation triggers end-to-end

---

## 9. REFERENCE DATA & LOOKUP TABLES

### 9a. Product Matrix Values

**Board(s):** Internal Board → Product Matrix dropdown

Values (from client's doc — confirm complete list):
- Category A
- Category B
- Category C
- Category D
- Category E
- Category F
- [OPEN QUESTION] Additional categories? Client's doc says "etc." — need full list

**Maintenance:** Project lead or backup admin updates as new product categories are added.

### 9b. Document Type Values

**Board(s):** Internal Board → Document Type dropdown

Values:
- Master agreement
- Amendment
- NDA
- Side letter
- [OPEN QUESTION] Additional types? Client's doc says "etc."

### 9c. Transaction Type Values

**Board(s):** Internal Board → Transaction Type dropdown

Values:
- Pipeline
- Above Ground Storage
- Cavern Storage
- Transport
- Lease
- Charter
- Sale/Purchase
- Truck & Rail
- [OPEN QUESTION] Client's doc notes "Add transport type options" — clarify what sub-types are needed

### 9d. Additional Internal Review Teams

**Board(s):** Internal Board → Additional Internal Review Teams dropdown (multi-select)

Values:
- Tax
- Compliance
- Credit
- Risk
- Regulatory
- Commercial
- Operations
- Specialist

### 9e. Counterparty List

**Board(s):** Internal Board → Counterparty dropdown  
**Source:** [CRM System] export  
**Volume:** TBD  
**Maintenance:** Periodic refresh from [CRM System]; project lead manages additions

**[PLATFORM LIMITATION]** monday.com dropdown columns have a practical limit of ~2,000 values. If the counterparty list exceeds this, consider using a text column with a separate reference board connected via connect_boards, or a search-based approach.

### 9f. [Client Entity] List

**Board(s):** Internal Board → [Client Entity] dropdown  
**Source:** [ERP System] export  
**Volume:** TBD  
**Maintenance:** Same as Counterparty

---

## 10. REPORTING & DASHBOARDS

**Dashboard:** Legal Operations Dashboard  
**Audience:** Legal team leads, General Counsel, project lead, backup admin

| Widget | Type | Data Source | Filters | Phase |
|--------|------|-----------|---------|-------|
| Workload by Owner | Workload widget | Internal Board → Assigned Internal Counsel | Active items only (exclude Closed/Archived) | Launch |
| Due Soon / Deadlines Within 3 Days | Table or chart | Internal Board → Deadline | Deadline ≤ today + 3 days | Launch |
| Status Distribution (Battery) | Battery | Internal Board → Status | All active items | Launch |
| Average Turnaround by Counsel | Chart | Internal Board → Date Received, Closed Date | Closed items, grouped by counsel | Launch |
| Average Turnaround by Credit | Chart | Internal Board → Credit review dates | Completed credit reviews | Phase 2 |
| Aging Report by Status | Chart | Internal Board → Days Since Last Activity, Status | Active items | Launch |
| Bottleneck Report | Chart/Table | Internal Board → Status | Items in: With counterparty, With internal review teams (KYC, Credit), With outside counsel | Launch |

### KPIs per kickoff call:

- Reduce agreement turnaround time
- Improve SLA compliance
- Balance workload distribution
- Eliminate lost requests

---

## 11. FUTURE PHASE ROADMAP

| # | Feature / Integration | Dependencies | Complexity | Priority | Source |
|---|----------------------|-------------|-----------|----------|--------|
| 1 | Full AI extraction suite (all 9+ fields auto-populated from email/attachment) | AI blocks or custom integration | High | High | Client's doc §5 |
| 2 | Auto-assignment by product/region/law/counterparty type | Stable routing rules, sufficient data | Medium | Medium | Client's doc §5C |
| 3 | [DMS] deep integration (auto-populate links, version tracking, notifications) | [DMS] API access, [Client] IT involvement | High | Medium | Client's doc §4, §8 |
| 4 | DocuSign native integration (trigger envelopes from monday.com, auto-update status) | DocuSign integration app, template mapping | Medium | Medium | Kickoff call, client's doc |
| 5 | Microsoft Teams notifications | Teams integration setup | Low | Medium | Kickoff call |
| 6 | OneDrive/SharePoint integration | Determine use case vs. [DMS] | Low | Low | Kickoff call |
| 7 | [CRM System] live integration (auto-refresh counterparty list, real-time KYC checks) | API access to [CRM System] | High | Medium | Client's doc §5 |
| 8 | AI-assisted email translation | AI blocks or third-party | Medium | Low | Kickoff call (confirmed it's a nice-to-have) |
| 9 | Advanced SLA reporting (time-in-stage tracking, SLA compliance %) | Stable workflow, sufficient data | Medium | High | Kickoff call |
| 10 | Auto-routing based on governing law (fully automated, no human sense check) | Confidence in AI extraction accuracy | Medium | Low | Client's doc §3 — currently requires human review |
| 11 | File naming compliance checker (auto-validate against naming template) | Define naming convention formally | Medium | Medium | Client's doc §4 |
| 12 | Coding workflow automation (auto-route to vendor or contracts based on Document Type) | Clear rules defined and tested | Low | Medium | Client's doc §11 |

---

## 12. OPEN QUESTIONS & RISKS

### Open Questions (Require Client Confirmation)

| # | Question | Source | Impact |
|---|---------|--------|--------|
| 1 | **Counterparty list export from [CRM System]** — Can the project lead or IT provide a CSV of all counterparty names? How many values? | Client's doc | Dropdown column design; may need connected board if >2,000 values |
| 2 | **[Client Entity] list from [ERP System]** — Same as above. Full entity list needed. | Client's doc | Dropdown column design |
| 3 | **Complete Product Matrix values** — Client's doc says "Category A, Category B, Category C, Category D, Category E, Category F, etc." What are the remaining values? | Client's doc | Dropdown completeness |
| 4 | **Complete Document Type values** — "Master agreement, Amendment, NDA, Side letter, etc." — what else? | — | Dropdown completeness; affects coding rules |
| 5 | **Transaction Type sub-types** — Client's doc notes "Add transport type options." What are these? | Client's doc | Dropdown completeness |
| 6 | **Time tracking approach** — Client's doc offers two options: status labels (On track/At risk/Overdue) vs. date/day counter. Which does the team prefer? | Client's doc | Column design |
| 7 | **"Finalization" vs. "Signature process"** — Client's §3 status table does not include "Signature process" but Steps 9-10 reference it. Confirm the correct status label. | Client's doc | Status column design contradiction |
| 8 | **Historical agreement backlog** — Are there open/in-progress agreements that need to be imported at launch? | Not discussed | Data migration scope |
| 9 | **monday.com product tier** — [Client Name] signed up for "monday.com Service." Confirm all required features (workload widget, time tracking, AI blocks, email integration, DocuSign) are available on this tier. | Kickoff call | Entire solution viability |
| 10 | **Commercial Review and Operations Review Teams** — Client's doc asks "Should we add these?" Confirm. | Client's doc | Review status columns |
| 11 | **Coding route details** — For Master Agreements routed to vendor: what specific fields are needed (applicability, product, reporting agent, confirming agent)? Where do these come from? | Client's doc §11 | Post-execution workflow |
| 12 | **Outside counsel email tracking** — Client's doc notes this depends on "whether the internal lawyer copied the agreements inbox on their email." How will this be handled? | Client's doc §7 | Automation reliability |
| 13 | **Retention policy** — How long do attachments/updates remain in monday.com vs. [DMS]/SharePoint? | Client's doc §7 | Governance configuration |
| 14 | **Shared Board access** — Who specifically are the "[Client] commercial contacts" who should see the shared board? How many users? | Client's doc §1 | Licensing and permissions |
| 15 | **SSO setup** — Project lead mentioned IT contacts for this. When should SSO configuration be scheduled? | Kickoff call | User access timeline |

### Platform Limitations

| # | Limitation | Workaround |
|---|-----------|------------|
| 1 | **No conditional column visibility** — Cannot hide/show review status columns based on "Additional Internal Review Teams" selection | Use board views filtered by review team; or document which columns are relevant when |
| 2 | **Dropdown value limit (~2,000)** — Counterparty list may exceed this | Use connected board with text search instead of dropdown |
| 3 | **long_text 2,000-char limit** in some views | Use item updates for longer notes |
| 4 | **No native [DMS] integration** | Manual link entry; custom integration via Make as Phase 2 |
| 5 | **Automations not configurable via API** | Must be set up manually in browser UI |
| 6 | **AI extraction capabilities** may not cover all 9+ fields natively | May require custom Make/Zapier workflows with external AI |
| 7 | **Email template automation** with dynamic table formatting (routing email with auto-populated fields) may not be fully supported natively | May need external email service or Make integration |

### Risks to Timeline

| # | Risk | Mitigation |
|---|------|-----------|
| 1 | [CRM System] data export delays | Request export in Week 1; use placeholder values if delayed |
| 2 | Product tier limitations discovered mid-build | Verify capabilities in Week 1 before building |
| 3 | Project lead absences extending timeline to 8 weeks | Acknowledged in kickoff; plan accordingly |
| 4 | [DMS] integration complexity underestimated | Scope as Phase 2 deep integration; launch with manual links |
| 5 | AI extraction not meeting expectations | Start with manual intake; layer AI as capabilities are proven |
| 6 | Team adoption risk (15 users with limited monday.com experience) | Training curriculum board; General Counsel sets expectations; UAT feedback loop |

---

## AUTO-EMAIL TEMPLATES

### Template 1 — Intake Acknowledgment

> We received your request regarding [Agreement Name]. We are reviewing it and will revert with next steps.

**Trigger:** Step 2 — when acknowledgment is appropriate  
**Merge fields:** Agreement Name (item name)

### Template 2 — Priority & Deadline Request (>2 pages)

> We received your agreement request regarding [Agreement Name]. Because this document is [Pages] pages, we need the following information before routing:
> - **Priority:** Normal or High?
> - **Deadline:** When does this need to be completed?
>
> We will route the agreement for review once this information is received.

**Trigger:** Step 2 — Pages > 2 AND Priority or Deadline is blank  
**Merge fields:** Agreement Name, Pages

### Template 3 — Missing Info / Blanks

> Regarding [Agreement Name], please confirm the following missing details: [list of missing bracketed items / entity / signatory details].

**Trigger:** Manual or automation-triggered when required fields are blank  
**Merge fields:** Agreement Name, specific missing fields

### Template 4 — Internal Routing Email (to counsel + review teams)

Table format in email body:

| Field | Value |
|-------|-------|
| Legal Team Member Assigned | [Assigned Internal Counsel] |
| Deadline | [Deadline] |
| No. of Pages | [Pages] |
| Commercial Client | [Commercial Client / Requestor] |
| Document Type / Title | [Document Type] · [Document Title / Desc.] |
| Counterparty | [Counterparty] |
| [Client Entity] | [[Client Entity]] |
| Additional Internal Review Teams | Tax: Yes/No · Compliance: Yes/No · Credit: Yes/No · Risk: Yes/No · Regulatory: Yes/No |
| Underlying Documents | [Attached] |
| Notes | [Notes] |

**Attachment logic:**
- To lawyers: Include [DMS] link (they have access); attach supporting docs
- To non-lawyer reviewers (Credit/Tax/Compliance/Risk/Regulatory): Attach the document itself + underlying docs (no [DMS] access)

---

*End of Specification — Version 1.0. Next step: Client review of open questions before build begins.*
