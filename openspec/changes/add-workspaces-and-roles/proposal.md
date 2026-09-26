## Why

The product is multi-tenant: a team or agency shares WordPress sites, and admins and editors can do different things. Tenant isolation and role checks must exist before any tenant data (sites, topics, posts) is created, otherwise every later change would have to retrofit them.

## What Changes

- Tables `workspaces` and `memberships` (`role` admin/editor, unique per user and workspace).
- Signing up creates a workspace with the new user as its admin.
- `Current.workspace` resolved from the URL (`/workspaces/:workspace_id/...`); a workspace switcher for users in several workspaces.
- Pundit policies with a role × action matrix; `policy_scope` on every query; records of another workspace answer 404.
- Members page: list members, change a member's role, remove a member.
- Domain rule: a workspace always keeps at least one admin (cannot demote or remove the last one).

## Capabilities

### New Capabilities
- `workspace-access`: workspaces, memberships, roles, permission matrix, tenant isolation, last-admin protection.

### Modified Capabilities
- `user-authentication`: sign-up now also creates the user's first workspace (introduced by `add-user-authentication`).

## Impact

- New tables: `workspaces`, `memberships`.
- All future controllers use `authorize` / `policy_scope`; a shared request-spec helper asserts cross-tenant 404s.

## Dependencies

- `add-user-authentication`

## Out of scope

- Inviting new members by email (`add-member-invitations`).
- Per-site permissions (HU-09, not in MVP).

## References

- readme.md §1.2 point 1, §2.5 point 2, §3.2 `workspaces`, `memberships`.
- HU-07 (partially).
