## Why

Real roles only matter if an admin can bring editors into the workspace. Without invitations the only way to add people is the database seed.

## What Changes

- Table `invitations` (email, role, `expires_at`, `accepted_at`, `invited_by_id`); one pending invitation per email and workspace (partial unique index).
- Admin invites by email with a role; invitation email with a signed token (`generates_token_for :invitation`, 7-day expiry, invalid once accepted).
- Accept flow for both cases: new person (creates an account) and existing user (adds a membership).
- Admin can list pending invitations, resend and revoke them.

## Capabilities

### New Capabilities
- None.

### Modified Capabilities
- `workspace-access`: members can join a workspace through an email invitation (introduced by `add-workspaces-and-roles`).

## Impact

- New table: `invitations`; new mailer and accept controller; Members page gains an "Invite" action.

## Dependencies

- `add-workspaces-and-roles`

## Out of scope

- Bulk invitations, invitation links without email, domain-based auto-join.

## References

- readme.md §3.2 `invitations`, §1.3 (Miembros screen).
- HU-07.
- Delivery note: lowest-priority change of wave 1; the first admin can come from seeds if time runs short.
