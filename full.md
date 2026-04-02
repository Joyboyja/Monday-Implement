# monday.com Implementation: Full Implementation

Run all 4 phases of a monday.com CRM implementation in sequence: Research, Plan, Execute, Review.

---

## CRITICAL RULES (ALL PHASES)

1. **No hardcoding.** All solutions must be generic and pattern-based, not tied to specific examples.
2. **Root cause, not bandaid.** Fix underlying structural or data issues, not symptoms.
3. **Data integrity first.** Use consistent, authoritative data sources throughout.
4. **Ask before changing.** If you have questions, ask them before making changes.
5. **Trace every requirement.** Every element in the spec must trace back to a source document. Every element in the build must trace back to the spec.

---

## MCP Availability Check (REQUIRED)

Before starting, verify the monday.com MCP tool is available:

```
Run: mcp__monday__get_user_context
```

- **If the tool exists and returns data**: MCP is available. Confirm with the user that this is the correct account for the project. If they need to switch to a different monday.com account, instruct them: "Go to `/mcp`, select **monday**, and reauthenticate with the correct account." Do not proceed until the correct account is confirmed.
- **If the tool does not exist or errors**: **STOP. Do not proceed.** Prompt the user:
  > "monday.com MCP server is not connected. The full implementation workflow requires it for execution and verification.
  >
  > Would you like to install it now? Install instructions: https://github.com/mondaycom/mcp (yes/no)"
  >
  > If **yes**: Guide the user through installation per the repo README, then re-check with `mcp__monday__get_user_context`. Only proceed once the check passes.
  > If **no**: Stop. Cannot run full implementation without MCP. Suggest running `/monday-implement:research` or `/monday-implement:plan` individually instead.

---

## Phase Sequence

Execute each phase in order. Pause for user confirmation between phases.

### Phase 1: Research
Run `/monday-implement:research` logic.
- Conduct platform, industry, source system, and third-party tool research
- Output: `research-summary.md`
- **Pause**: Present summary to user. Ask: "Research complete. Review the summary and confirm to proceed to Planning, or note any corrections."

### Phase 2: Plan
Run `/monday-implement:plan` logic.
- Analyze all client documents + research summary
- Produce full 12-section implementation spec
- Output: `implementation-spec.md`
- **Pause**: Present spec to user. Ask: "Implementation spec complete. Review and confirm to proceed to Execution, or note any changes needed."

### Phase 3: Execute
Run `/monday-implement:execute` logic.
- Build boards, forms, columns, automations via MCP
- Output: `execution-log.md`
- **Pause**: Present execution summary to user. Ask: "Build complete. Review the execution log and confirm to proceed to Verification, or note any issues to fix first."

### Phase 4: Review
Run `/monday-implement:review` logic.
- Audit the build against the spec via MCP queries
- Output: `verification-report.md`
- **Final output**: Present pass/fail summary with action items

---

## Model Routing Recommendations

While this command runs all phases sequentially, the recommended model for each phase is:

| Phase | Recommended Model | Why |
|-------|------------------|-----|
| Research | Opus 4.6 | Deep reasoning for thorough research synthesis |
| Plan | Opus 4.6 + extended thinking | Complex cross-referencing of documents and platform constraints |
| Execute | Sonnet 4.6 | Fast, accurate MCP tool execution |
| Review | Opus 4.6 | Careful verification requiring spec-to-build comparison |

If running in a single session, all phases will use the current model. For optimal results, consider running phases individually with the recommended model for each.

---

## Artifacts Produced

By the end of a full implementation, the project directory will contain:

```
project-directory/
  research-summary.md       # Platform & context research
  implementation-spec.md    # Full 12-section build spec
  execution-log.md          # Action-by-action build record
  verification-report.md    # Pass/fail audit report
```

These 4 documents form the complete audit trail from requirements to verified build.
