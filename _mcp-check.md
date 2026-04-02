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

---

## MCP Capability Reference

Use this to determine what can be automated vs. what requires manual user action.

### MCP CAN do (tag as `[MCP]` in spec)

| Capability | MCP Tool |
|-----------|----------|
| Create workspaces | `create_workspace` |
| Create boards | `create_board` |
| Create columns (all types, with settings) | `create_column` |
| Create groups (with ordering and color) | `create_group` |
| Create items and subitems (with column values) | `create_item` |
| Update item column values | `change_item_column_values` |
| Create forms + form questions | `create_form`, `form_questions_editor` |
| Update form settings, appearance, features | `update_form` |
| Create docs (workspace or item-level) | `create_doc`, `add_content_to_doc` |
| Create dashboards | `create_dashboard` |
| Create dashboard widgets | `create_widget` |
| Create folders | `create_folder` |
| Move boards/folders between workspaces | `move_object` |
| Send notifications to users | `create_notification` |
| Post updates/comments on items | `create_update` |
| Query board structure, items, forms, docs | `get_board_info`, `get_board_items_page`, `get_form`, `read_docs` |
| Run any GraphQL query/mutation | `all_monday_api` |
| Delete columns, groups, items (via GraphQL) | `all_monday_api` |
| Rename columns (via GraphQL) | `all_monday_api` |

### MCP CANNOT do (tag as `[MANUAL]` in spec)

These must be done by the user in the monday.com browser UI after execution:

| Action | Why |
|--------|-----|
| **Create/configure automations** | Automations are not exposed via the monday.com API. Must be configured manually in browser UI. |
| **Create/configure integrations** (DocuSign, email, Outlook, etc.) | Integration setup requires OAuth flows and UI configuration. |
| **Set board permissions** (restrict who can view/edit) | Permission changes require admin UI. |
| **Configure board views** (filtered views, Kanban, timeline) | Views are not creatable via API. |
| **Set up email notifications / templates** | Custom email templates require integration setup. |
| **Configure SSO / user provisioning** | Admin-level IT configuration. |
| **Import CSV data** | Bulk import wizard is browser-only. (API can create items one-by-one as alternative.) |
| **Delete default "Group Title" groups** | Can be done via `all_monday_api` GraphQL, but flag for user review before deleting. |
| **Configure connected board column linking direction** | Two-way vs one-way mirroring settings require UI. |
| **Set column permissions** (restrict who can edit specific columns) | Column-level permissions require UI. |
