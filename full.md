# monday.com Implementation: Full Implementation

Run all 4 phases of a monday.com CRM implementation in sequence: Research, Plan, Execute, Review.

---

**Read and follow `_rules.md`** (canonical critical rules for all phases).

**Read and follow `_mcp-check.md`** (canonical MCP availability check). Full implementation **requires** MCP -- stop if not available.

**Read `config.md`** and ask the user to fill in project variables. Save to `project-config.yaml`.

---

## Phase Sequence

Execute each phase by reading its file from disk and following it completely. Pause for user confirmation between phases.

### Phase 1: Research
**Read `~/.claude/commands/monday-implement/research.md`** and execute it fully.
- Conduct platform, industry, source system, and third-party tool research
- Output: `research-summary.md`
- **Pause**: Present summary to user. Ask: "Research complete. Review the summary and confirm to proceed to Planning, or note any corrections."

### Phase 2: Plan
**Read `~/.claude/commands/monday-implement/plan.md`** and execute it fully.
- Analyze all client documents + research summary
- Produce full 12-section implementation spec
- Output: `implementation-spec.md`
- **Pause**: Present spec to user. Ask: "Implementation spec complete. Review and confirm to proceed to Execution, or note any changes needed."

### Phase 3: Execute
**Read `~/.claude/commands/monday-implement/execute.md`** and execute it fully.
- Build boards, forms, columns, automations via MCP
- Output: `execution-log.md`
- **Pause**: Present execution summary to user. Ask: "Build complete. Review the execution log and confirm to proceed to Verification, or note any issues to fix first."

### Phase 4: Review
**Read `~/.claude/commands/monday-implement/review.md`** and execute it fully.
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
  project-config.yaml       # Project variables (client, workspace, tools)
  research-summary.md       # Platform & context research
  implementation-spec.md    # Full 12-section build spec
  execution-log.md          # Action-by-action build record
  verification-report.md    # Pass/fail audit report
```

These 5 documents form the complete audit trail from requirements to verified build.
