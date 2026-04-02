# Verification Report

**Client:** [Client Name] — Marketing Operations  
**Implementation:** Project Intake & Workflow Management  
**Verified against:** Live Monday.com API data, [Date]  
**Prepared by:** [Your Name]

---

## Summary Table

| Phase | Section | Status | Details |
|-------|---------|--------|---------|
| 1 | Workspace | PASS | [Workspace Name] ([workspace_id]) — used existing workspace per user instruction |
| 2 | Board Creation | PASS | 3 boards created with correct names and types |
| 3 | Column Schema (Boards 1 & 2) | PASS | 26 columns each (name + 25 custom) — all 22 spec'd columns confirmed |
| 4 | Groups (Boards 1 & 2) | PASS | 9/9 workflow groups in correct order + 1 default |
| 5 | Column Schema (Board 3) | PASS | 22 columns (name + 21 custom) — all spec'd columns confirmed |
| 6 | Groups (Board 3) | PASS | 4/4 groups in correct order + 1 default |
| 7 | Form A — Lead Assignment | CREATED | 2 forms created, routing needed (see notes) |
| 8 | Form B — General Submission | CREATED | 1 form created, routing needed (see notes) |
| 9 | Demo Items (Board 1) | PASS | 3/3 items with correct groups and tiers |
| 10 | Demo Items (Board 3) | PASS | 1/1 item in correct group |
| Bonus | Doc + Dashboard | CREATED | Implementation guide and overview dashboard |

**Final Score: 10/10 phases executed — 8 PASS, 2 CREATED (forms need routing)**

---

## Phase 1: Workspace

**Status:** PASS

[Workspace Name] ([workspace_id]) — used existing workspace per user instruction.

---

## Phase 2: Board Creation

**Status:** PASS

| Board | ID | Type |
|-------|-----|------|
| [Division A] — Projects | [board_id_1] | Main |
| [Division B] — Projects | [board_id_2] | Main |
| [Pre-Approval Queue] | [board_id_3] | Main |

---

## Phase 3: Column Schema (Boards 1 & 2)

**Status:** PASS

Board 1: 26 columns (name + 25 custom) — all 22 spec'd columns confirmed.  
Board 2: 26 columns (name + 25 custom) — identical schema confirmed.

### Dropdown/Status Value Verification

- Priority tier labels: 4 tiers with correct colors -- PASS
- Brand dropdown: 6 options -- PASS
- Team assignment dropdowns: correct options -- PASS

---

## Phase 4: Groups (Boards 1 & 2)

**Status:** PASS (9/9 + default)

Board 1: 9 workflow groups in correct order + 1 default "Group Title"  
Board 2: 9 workflow groups in correct order + 1 default "Group Title"

**Action Required:** Default "Group Title" must be deleted manually on both boards.

---

## Phase 5: Column Schema (Board 3 — Pre-Approval)

**Status:** PASS

22 columns (name + 21 custom) — all spec'd columns confirmed.

- Approval Status: Pending / Approved / Rejected / Needs Info -- PASS
- Line of Business dropdown: 5 options -- PASS
- Lead Assigned: people column -- PASS

---

## Phase 6: Groups (Board 3 — Pre-Approval)

**Status:** PASS (4/4 + default)

Pending Approval / Approved — Ready to Move / Rejected / Needs More Info -- PASS

**Action Required:** Default "Group Title" must be deleted manually.

---

## Phase 7: Form A — Lead Assignment

**Status:** CREATED (see notes)

| Form | Questions | Token | Board Created |
|------|-----------|-------|---------------|
| Form A ([Division A]) | 20 | [token_1]... | [form_board_id_1] |
| Form A ([Division B]) | 20 | [token_2]... | [form_board_id_2] |

**[ACTION REQUIRED]** Forms create their own boards. Need automation to push items from form boards to main project boards, OR recreate forms natively on existing boards via Monday.com UI.

---

## Phase 8: Form B — General Submission

**Status:** CREATED (see notes)

| Form | Questions | Token | Board Created |
|------|-----------|-------|---------------|
| Form B | 21 | [token_3]... | [form_board_id_3] |

Same note as Phase 7 — form creates own board. Routing automation or form recreation needed.

---

## Phase 9: Demo Items (Board 1)

**Status:** PASS (3/3)

| Item | Group | Priority |
|------|-------|----------|
| Sample Project Alpha | New Requests | Tier 2 |
| Sample Project Beta | Content In Progress | Tier 1 |
| Sample Project Gamma | Completed | Tier 4 |

Board items_count = 3 confirmed via API.

---

## Phase 10: Demo Items (Board 3)

**Status:** PASS (1/1)

| Item | Group |
|------|-------|
| Sample Request — Design Review | Pending Approval |

Board items_count = 1 confirmed via API.

---

## Bonus: Doc + Dashboard

**Status:** CREATED

- Implementation Guide doc ([doc_id]) in workspace -- PASS
- Overview Dashboard ([dashboard_id]) connected to all 3 boards -- PASS

---

## Column-by-Column Verification

### Boards 1 & 2 (Project Boards) — 22 required columns

| Column | Type | Status |
|--------|------|--------|
| Priority Tier | status | PASS |
| Brand | dropdown (6 opts) | PASS |
| Project Type | status | PASS |
| Requirements | status (4 opts) | PASS |
| Lead Assigned | people | PASS |
| Team A | dropdown | PASS |
| Team A Person | people | PASS |
| Team B | dropdown | PASS |
| Team B Person | people | PASS |
| Requestor | text | PASS |
| Requestor Dept | text | PASS |
| Deadline | date | PASS |
| Deadline Flexible | checkbox | PASS |
| Channel | tags | PASS |
| Target Market | status | PASS |
| Brief Description | long_text | PASS |
| Key Objectives | long_text | PASS |
| Key Messages | long_text | PASS |
| Design Requirements | long_text | PASS |
| Impact if Missed | long_text | PASS |
| Budget | text | PASS |
| Cost Centre | text | PASS |
| Brief Attachment | file | PASS |
| Supporting Files | file | PASS |
| Stage Timer | time_tracking | PASS |
| Notes | long_text | PASS |

**Result: 22/22 columns present and correct type**

### Board 3 (Pre-Approval) — 21 required columns

All 21 confirmed: Requestor, Dept, LOB, Brand, Project Type, Brief Desc, Key Objectives, Requirements, Deadline, Flexible, Channel, Target Market, Key Messages, Design Reqs, Impact, Budget, Cost Centre, Approvers, Brief Attachment, Supporting Files, Approval Status, Lead Assigned, Reviewer Notes.

**Result: 21/21 columns present and correct type**

---

## Required Fixes (before go-live)

1. **Form routing** -- Boards 1 & 2, Board 3 -- Forms created standalone boards instead of feeding existing boards. Either set up automations to move items from form boards to main boards, or recreate forms natively on the existing boards via Monday.com UI.
2. **Delete default groups** -- Boards 1, 2, 3 -- Remove the default "Group Title" group from each board to avoid user confusion.

## Recommended Fixes (quality improvements)

1. **Form board cleanup** -- After routing is configured, archive or delete the standalone form-created boards ([form_board_id_1], [form_board_id_2], [form_board_id_3]) to avoid duplicate data entry points.

## Client Confirmations Needed

1. **Form routing approach** -- Does the client prefer automation-based routing (items auto-move from form board to project board) or native form recreation (forms rebuilt directly on existing boards)? The latter is cleaner but requires re-creating the forms.
