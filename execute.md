# monday.com Implementation: Execution Phase

Execute the implementation spec using the monday.com MCP tool. Build boards, columns, forms, automations, and integrations exactly as specified.

**Prerequisites**:
- `research-summary.md` exists (from `/monday-implement:research`)
- `implementation-spec.md` exists (from `/monday-implement:plan`)
- monday.com MCP tool is connected

---

## CRITICAL RULES (ALL PHASES)

1. **No hardcoding.** All solutions must be generic and pattern-based, not tied to specific examples.
2. **Root cause, not bandaid.** Fix underlying structural or data issues, not symptoms.
3. **Data integrity first.** Use consistent, authoritative data sources throughout.
4. **Ask before changing.** If you have questions, ask them before making changes.
5. **Trace every requirement.** Every element in the spec must trace back to a source document. Every element in the build must trace back to the spec.

---

## MCP Availability Check (REQUIRED)

Before starting execution, verify the monday.com MCP tool is available:

```
Run: mcp__monday__get_user_context
```

- **If the tool exists and returns data**: Proceed with execution. Confirm with the user that this is the correct account for the project. If they need to switch to a different monday.com account, instruct them: "Go to `/mcp`, select **monday**, and reauthenticate with the correct account." Do not proceed until the correct account is confirmed.
- **If the tool does not exist or errors**: **STOP. Do not proceed.** Prompt the user:
  > "monday.com MCP server is not connected. The Execution phase requires it to create boards, columns, and automations.
  >
  > Would you like to install it now? Install instructions: https://github.com/mondaycom/mcp (yes/no)"
  >
  > If **yes**: Guide the user through installation per the repo README, then re-check with `mcp__monday__get_user_context`. Only proceed once the check passes.
  > If **no**: Stop. Cannot execute without MCP.

---

## Load Spec

Read `implementation-spec.md` from the project directory. If it does not exist, instruct the user to run `/monday-implement:plan` first.

---

## Execution Rules

1. **Follow the spec exactly.** Do not add, skip, or rename anything unless you encounter a platform limitation, in which case flag it and propose an alternative before proceeding.

2. **Work board by board in spec order.** For each board: create the form and its questions first (forms are boards in monday.com), then add any additional columns to the board that don't need to appear on the form. Complete groups and automations after all columns are in place.

3. **After creating each board**, output a checklist of what was created vs. what the spec called for so the user can spot-check.

4. **Use exact column types** from the spec. If a column type is not available, stop and ask.

5. **For automations**, describe the trigger/condition/action in plain language and confirm with the user before creating.

6. **Forms first, then extra columns.** When a board has an associated form, create the form first since it generates the board and its form-facing columns. Then add any backend-only columns (auto-numbers, connected boards, mirrors, formulas, internal statuses) that don't need to be visible on the form.

7. **Do not configure integrations** (DocuSign, GetSign, etc.) without explicit user confirmation of credentials and account details.

8. **Log every action** in the execution log so the verification step has a clear record to audit against.

---

## Execution Order

For each board in the spec, follow this sequence:

```
1. Create form (if board has an associated form)
   -> This creates the board + form-facing columns
2. Add backend-only columns (auto_number, connect_boards, mirror, formula, internal statuses)
3. Create groups
4. Configure automations (confirm each with user first)
5. Set up connected board relationships
6. Configure mirror columns
7. Output board completion checklist
```

For boards without forms:
```
1. Create board
2. Add all columns
3. Create groups
4. Configure automations
5. Set up relationships and mirrors
6. Output board completion checklist
```

---

## Execution Log

Maintain a running log. Save as `execution-log.md` in the project directory after each board is completed.

Format:

```markdown
# Execution Log

## Board: [Board Name]
**Created**: [timestamp]
**Method**: Form-first / Direct creation

### Form
- Form created: [form name] - OK / DEVIATION
- Questions: [count] created

### Columns
| Column Name | Type | Source | Status |
|-------------|------|--------|--------|
| [name] | [type] | Form | OK |
| [name] | [type] | Backend | OK |
| [name] | [type] | Backend | DEVIATION: [reason] |

### Groups
- [group name] - OK
- [group name] - OK

### Automations
- [description] - OK / SKIPPED: [reason]

### Checklist
- [ ] All form-facing columns created ([n]/[n])
- [ ] All backend columns created ([n]/[n])
- [ ] All groups created ([n]/[n])
- [ ] All automations created ([n]/[n])
- [ ] Connected boards linked ([n]/[n])
- [ ] Mirror columns configured ([n]/[n])

### Deviations
- [Any deviations from spec with reasons]

---
```

---

## Handling Deviations

When the spec cannot be followed exactly:

1. **Stop** before making the change
2. **Explain** what the spec calls for and what the platform limitation is
3. **Propose** an alternative approach
4. **Wait** for user confirmation before proceeding
5. **Log** the deviation in the execution log with the reason and approved alternative

---

## Phase Completion

After all boards are built:
1. Save the final `execution-log.md`
2. Output a summary showing total boards, columns, forms, and automations created
3. List any deviations or items that need follow-up
4. Instruct the user to run `/monday-implement:review` for verification
