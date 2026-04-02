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

Copy-paste the command for your platform. Installs fresh or updates if already installed.

**Mac / Linux / Git Bash:**
```bash
mkdir -p ~/.claude/commands && { [ -d ~/.claude/commands/monday-implement ] && git -C ~/.claude/commands/monday-implement pull || git clone https://github.com/Joyboyja/Monday-Implement.git ~/.claude/commands/monday-implement; }
```

**Windows (PowerShell):**
```powershell
New-Item -ItemType Directory -Force -Path "$env:USERPROFILE\.claude\commands" | Out-Null; if (Test-Path "$env:USERPROFILE\.claude\commands\monday-implement") { git -C "$env:USERPROFILE\.claude\commands\monday-implement" pull } else { git clone https://github.com/Joyboyja/Monday-Implement.git "$env:USERPROFILE\.claude\commands\monday-implement" }
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
  .gitignore         # Excludes generated artifacts
```

## Critical Rules (All Phases)

Defined in `_rules.md` and referenced by all phase files:

1. **No hardcoding.** All solutions must be generic and pattern-based.
2. **Root cause, not bandaid.** Fix underlying structural or data issues, not symptoms.
3. **Data integrity first.** Use consistent, authoritative data sources throughout.
4. **Ask before changing.** If you have questions, ask them before making changes.
5. **Trace every requirement.** Every element in the spec must trace back to a source document. Every element in the build must trace back to the spec.
