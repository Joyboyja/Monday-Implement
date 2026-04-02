## MCP Availability Check

> **Canonical source**: This file is the single source of truth for the MCP check logic. All phase files reference this.

Before starting, verify the monday.com MCP tool is available:

```
Run: mcp__monday__get_user_context
```

### MCP is connected (tool returns data)

1. Note the account name and user info returned.
2. Confirm with the user: "Connected to monday.com account: **[account name]**. Is this the correct account for this project?"
3. If they need to switch accounts: instruct them to go to `/mcp`, select **monday**, and reauthenticate with the correct account.
4. Re-check with `mcp__monday__get_user_context` after they reauthenticate.
5. Do not proceed until the correct account is confirmed.

### MCP is NOT connected (tool does not exist or errors)

Prompt the user:

> "monday.com MCP server is not connected. Would you like to install it now?
>
> Install instructions: https://github.com/mondaycom/mcp (yes/no)"

- If **yes**: Guide the user through installation per the repo README, then re-check with `mcp__monday__get_user_context`. Only proceed once the check passes.
- If **no**: Behavior depends on the phase:
  - **Research / Plan**: Proceed without MCP. Remind the user to install before running `/monday-implement:execute`.
  - **Execute / Review / Full**: **STOP.** Cannot proceed without MCP.
