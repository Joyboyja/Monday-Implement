# Monday-Implement

monday.com CRM implementation workflow for Claude Code. 4 phases: Research, Plan, Execute, Review. Analyzes client docs to produce board architecture, column specs, forms, automations, and integrations. Builds via monday.com MCP with forms-first execution. Includes platform limitation flags, spec traceability, and pass/fail verification.

## Commands

| Command | Purpose |
|---------|---------|
| `/monday-implement:research` | Research platform capabilities, client industry, source system, and third-party tools |
| `/monday-implement:plan` | Analyze client docs + research to produce a 12-section implementation spec |
| `/monday-implement:execute` | Build boards, forms, columns, automations via monday.com MCP |
| `/monday-implement:review` | Audit the build against the spec with pass/fail scoring |
| `/monday-implement:full` | Run all 4 phases sequentially with confirmation gates |

## Prerequisites

- [Claude Code](https://claude.ai/claude-code)
- [monday.com MCP server](https://github.com/mondaycom/mcp) (auto-detected; install prompted if missing)

## Installation

One command — copy-paste and run. Installs fresh or updates if already installed.

**Mac / Linux / Git Bash:**
```
git clone https://github.com/Joyboyja/Monday-Implement.git ~/.claude/commands/monday-implement 2>/dev/null || git -C ~/.claude/commands/monday-implement pull
```

**Windows (Command Prompt):**
```
git clone https://github.com/Joyboyja/Monday-Implement.git "%USERPROFILE%\.claude\commands\monday-implement" || git -C "%USERPROFILE%\.claude\commands\monday-implement" pull
```

**Windows (PowerShell):**
```
git clone https://github.com/Joyboyja/Monday-Implement.git "$env:USERPROFILE\.claude\commands\monday-implement"; if (-not $?) { git -C "$env:USERPROFILE\.claude\commands\monday-implement" pull }
```

## Usage

1. Attach client documents (discovery call transcripts, SOWs, process docs, data exports)
2. Run `/monday-implement:research` to build a knowledge foundation
3. Run `/monday-implement:plan` to generate the implementation spec
4. Run `/monday-implement:execute` to build via MCP
5. Run `/monday-implement:review` to verify the build

Or run `/monday-implement:full` to execute all 4 phases with user confirmation between each.

## Recommended Model Routing

| Phase | Model | Why |
|-------|-------|-----|
| Research | Opus 4.6 | Thorough research synthesis |
| Plan | Opus 4.6 + extended thinking | Complex cross-referencing of documents and platform constraints |
| Execute | Sonnet 4.6 | Fast, accurate MCP tool execution |
| Review | Opus 4.6 | Careful spec-to-build verification |

## Compatibility

This tool is built for **Claude Code** (CLI, desktop app, web app, and IDE extensions). It uses Claude Code's custom commands system (`~/.claude/commands/`) and the monday.com MCP server. It does not work with Claude Desktop projects or claude.ai chat -- only environments that support slash commands and MCP tools.

## Artifacts

A full run produces 5 audit trail files in your project directory:

```
project-config.yaml        # Project variables (client, workspace, tools)
research-summary.md        # Platform & context research
implementation-spec.md     # Full 12-section build spec
execution-log.md           # Action-by-action build record
verification-report.md     # Pass/fail audit report
```

These are generated per-project and excluded from the commands repo via `.gitignore`.

## File Structure

```
monday-implement/
  _rules.md          # Shared critical rules (single source of truth)
  _mcp-check.md      # Shared MCP check logic (single source of truth)
  config.md          # Project config template
  research.md        # Phase 1: Research
  plan.md            # Phase 2: Plan
  execute.md         # Phase 3: Execute
  review.md          # Phase 4: Review
  full.md            # All 4 phases orchestrated
  examples/          # Reference outputs showing what good looks like
  .gitignore         # Excludes generated artifacts
```

## Examples

The `examples/` folder contains anonymized reference outputs from a real implementation project (NGO migrating from Salesforce). Use these as a benchmark for output quality and format:

- `examples/research-summary.md` -- Research phase output with [DESIGN IMPACT] flags, platform limitations tables, and third-party tool evaluations
- `examples/implementation-spec.md` -- Plan phase output: full 12-section spec with 33+ columns, 11-step workflow, 21 automations, and email templates
- `examples/execution-log.md` -- Execute phase output showing MCP tool calls, fix sequence, and deviation tracking
- `examples/verification-report.md` -- Review phase output with phase-by-phase pass/fail, column-by-column verification, and action items

## Critical Rules (All Phases)

Defined in `_rules.md` and referenced by all phase files:

1. **No hardcoding.** All solutions must be generic and pattern-based.
2. **Root cause, not bandaid.** Fix underlying structural or data issues, not symptoms.
3. **Data integrity first.** Use consistent, authoritative data sources throughout.
4. **Ask before changing.** If you have questions, ask them before making changes.
5. **Trace every requirement.** Every element in the spec must trace back to a source document. Every element in the build must trace back to the spec.
