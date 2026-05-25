# Workspace Sharing

RCH supports multi-user collaboration. Invite team members to your workspace and assign roles that control what they can do.

## Roles

| Role | Can View | Can Operate | Can Edit | Can Admin |
|------|----------|-------------|----------|-----------|
| **viewer** | ✅ | — | — | — |
| **operator** | ✅ | ✅ | — | — |
| **editor** | ✅ | ✅ | ✅ | — |
| **admin** | ✅ | ✅ | ✅ | ✅ |

### What Each Level Means

- **View** — See dashboards, screens, pages, widgets, sources, logs
- **Operate** — Interact with control widgets (press buttons, move sliders, toggle switches)
- **Edit** — Create/modify/delete screens, pages, widgets, sources, endpoints, bindings
- **Admin** — Manage workspace settings, invite/remove members, change roles, manage API keys

## Inviting Members

1. Sidebar → **Workspace** panel → click the members icon
2. Click **Invite Member**
3. Search for the user by username
4. Select a role
5. Click **Add**

The user must already have an RCH account. They'll see the workspace in their workspace selector immediately.

> **Tip:** Use the `operator` role for people who only need to press buttons and monitor — they can't accidentally break your dashboard configuration.

## Changing Roles

1. Open the members dialog
2. Click the role dropdown next to the member
3. Select the new role
4. Change takes effect immediately

## Removing Members

1. Open the members dialog
2. Click the remove button next to the member
3. Confirm removal

The user loses access immediately. Their sessions for this workspace are not terminated, but any new request will be denied.

## Workspace Isolation

Each workspace is fully isolated:
- Members of workspace A cannot see workspace B's data
- Screens, sources, bindings, and widgets belong to exactly one workspace
- Audit logs are workspace-scoped
- API keys operate within the user's workspace context

## Who Can Manage Members

Only users with the **admin** role in a workspace can:
- Invite new members
- Remove existing members
- Change member roles

The workspace creator is automatically an admin.

## Limits

- No limit on members per workspace
- A user can be a member of multiple workspaces
- Each user has one role per workspace (not cumulative)
