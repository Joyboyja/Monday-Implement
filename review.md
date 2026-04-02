# monday.com Implementation: Review & Verification Phase

Audit the monday.com build against the original implementation spec. Produce a pass/fail report with specific findings for each section.

**Prerequisites**:
- `implementation-spec.md` exists (from `/monday-implement:plan`)
- `execution-log.md` exists (from `/monday-implement:execute`)
- monday.com MCP tool is connected (to query actual board state)

---

## CRITICAL RULES (ALL PHASES)

1. **No hardcoding.** All solutions must be generic and pattern-based, not tied to specific examples.
2. **Root cause, not bandaid.** Fix underlying structural or data issues, not symptoms.
3. **Data integrity first.** Use consistent, authoritative data sources throughout.
4. **Ask before changing.** If you have questions, ask them before making changes.
5. **Trace every requirement.** Every element in the spec must trace back to a source document. Every element in the build must trace back to the spec.

---

## MCP Availability Check (REQUIRED)

Before starting verification, verify the monday.com MCP tool is available:

```
Run: mcp__monday__get_user_context
```

- **If the tool exists and returns data**: Proceed with verification. Confirm with the user that this is the correct account for the project. If they need to switch to a different monday.com account, instruct them: "Go to `/mcp`, select **monday**, and reauthenticate with the correct account." Do not proceed until the correct account is confirmed.
- **If the tool does not exist or errors**: **STOP. Do not proceed.** Prompt the user:
  > "monday.com MCP server is not connected. The Review phase requires it to query board state and verify the build.
  >
  > Would you like to install it now? Install instructions: https://github.com/mondaycom/mcp (yes/no)"
  >
  > If **yes**: Guide the user through installation per the repo README, then re-check with `mcp__monday__get_user_context`. Only proceed once the check passes.
  > If **no**: Stop. Cannot verify without MCP.

---

## Load Documents

1. Read `implementation-spec.md` -- this is the source of truth
2. Read `execution-log.md` -- this is the build record
3. Use MCP tools to query the actual state of each board in monday.com

If either file is missing, instruct the user to run the prerequisite phase first.

---

## Verification Procedure

For each board in the spec, query it via MCP and verify against the spec. Do not rely solely on the execution log -- the log records intent, but the MCP query reveals actual state.

### 1. STRUCTURAL AUDIT
- Every board in the spec exists with the correct name and type
- Every column exists on the correct board with the correct column type
- Every group exists on the correct board
- All connect_boards and mirror columns are linked to the correct target boards
- Board permissions match the spec (if specified)

### 2. DATA INTEGRITY
- All dropdown/status columns have the complete set of values from the spec (none missing, no extras)
- Required field settings match the spec
- Auto-number columns are configured with the correct prefix and starting number
- Reference data (GL codes, department IDs, etc.) is fully loaded with no missing entries
- Column order matches the spec (if specified)

### 3. WORKFLOW VALIDATION
- Each approval workflow has the correct stages in the correct order
- Notification automations fire to the correct people at the correct triggers
- Conditional logic (threshold-based rules, show/hide fields) is working
- Reminder/escalation automations have the correct timing

### 4. FORM VALIDATION
- Each form feeds the correct board
- Field order matches the spec
- Conditional sections show/hide correctly
- Helper text and descriptions are present
- Required fields are enforced
- File upload fields are configured correctly

### 5. INTEGRATION CHECK
- All integrations are connected and authenticated
- Document merge templates are mapped to the correct fields
- Signature workflows route to the correct signers
- If integrations were deferred, note them as N/A with the reason

### 6. GAP REPORT
- List anything in the spec that was NOT built, with a reason
- List anything that was built but DIFFERS from the spec, with a reason
- List any platform limitations encountered and the workarounds applied
- List any items that need client confirmation before going live
- Cross-reference against execution log deviations

---

## Verification Output

Save as `verification-report.md` in the project directory.

### Summary Table

```markdown
## Verification Report

| Section | Status | Details |
|---------|--------|---------|
| Structural Audit | PASS/FAIL | [summary] |
| Data Integrity | PASS/FAIL | [summary] |
| Workflow Validation | PASS/FAIL | [summary] |
| Form Validation | PASS/FAIL | [summary] |
| Integration Check | PASS/FAIL/N-A | [summary] |
| Gap Report | -- | [count] gaps identified |
```

### Per-Board Detail

For each board, output:

```markdown
### Board: [Board Name]

**Columns**: [matched]/[spec total] -- PASS/FAIL
- Missing: [list any missing columns]
- Extra: [list any unexpected columns]
- Type mismatches: [list any wrong types]

**Dropdown/Status Values**: PASS/FAIL
- [Column Name]: Missing values: [list]
- [Column Name]: Extra values: [list]

**Groups**: [matched]/[spec total] -- PASS/FAIL

**Automations**: [matched]/[spec total] -- PASS/FAIL
- Missing: [list]
- Misconfigured: [list with details]

**Form**: PASS/FAIL/N-A
- Field order: PASS/FAIL
- Conditionals: PASS/FAIL
- Required fields: PASS/FAIL
```

### Action Items

At the end, produce a prioritized list of fixes:

```markdown
## Required Fixes (before go-live)
1. [Critical fix] -- [board] -- [what to do]
2. ...

## Recommended Fixes (quality improvements)
1. [Improvement] -- [board] -- [what to do]
2. ...

## Client Confirmations Needed
1. [Question] -- [context]
2. ...
```

---

## Phase Completion

After generating the verification report:
1. Save `verification-report.md`
2. Present the summary table to the user
3. If all sections PASS: confirm the implementation is ready for client UAT
4. If any sections FAIL: list the specific fixes needed and offer to re-run execution for those items
