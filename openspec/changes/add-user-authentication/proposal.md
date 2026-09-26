## Why

Every screen and every action in the product belongs to a person with a role, so identity has to exist before workspaces, sites or content. Rails 8 ships an authentication generator that covers most of it with little code and sound defaults.

## What Changes

- Run the Rails 8 authentication generator: `users` (`email_address`, `password_digest`, plus `name`), database-backed `sessions`, signed `httpOnly` session cookie, `Current.user`.
- Add sign-up (the generator has none), sign-in, sign-out and password reset (email via Action Mailer; Mailpit locally).
- Inertia pages for sign-up, sign-in, forgot password and reset password.
- `rate_limit` on sign-in and password reset requests.
- JSON responses return 401 for unauthenticated requests; HTML requests redirect to sign-in.

## Capabilities

### New Capabilities
- `user-authentication`: account creation, sign-in/out, session lifetime, password reset, brute-force protection.

### Modified Capabilities
- None.

## Impact

- New tables: `users`, `sessions`.
- New controllers: registrations, sessions, passwords; `Authentication` concern applied to all controllers.
- Mailer: password reset.

## Dependencies

- `bootstrap-platform`

## Out of scope

- Workspaces and roles (`add-workspaces-and-roles`) — sign-up creating a workspace is added there.
- Invitations (`add-member-invitations`).
- OAuth/SSO, two-factor authentication, email confirmation.

## References

- readme.md §2.2 (Autenticación), §2.5 point 1, §3.2 `users`, `sessions`.
- HU-07 (prerequisite).
