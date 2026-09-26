## Why

Nothing exists yet besides documentation. Every feature change needs the same foundation — the Rails app, the React/Inertia UI stack, local services (PostgreSQL + pgvector, WordPress, Mailpit), a test setup and CI — so it is built once, first, instead of leaking into feature PRs.

## What Changes

- Generate the Rails 8 app in `apps/platform/` (PostgreSQL, RSpec instead of Minitest), never at the repo root (a generated `README.md` would clobber `readme.md` on case-insensitive filesystems).
- Add the UI stack: `inertia_rails`, `vite_rails`, React 19, TypeScript, Tailwind CSS 4, a base layout and an example page.
- Configure Solid Queue in the primary database (single-database setup) and run it from `bin/dev`.
- Add `compose.yaml` at the repo root: `pgvector/pgvector:pg17`, `wordpress` + `mysql` with `WP_ENVIRONMENT_TYPE=local`, a `wp-cli` seed (Author user, Application Password, sample posts) and `mailpit`.
- Add `mise.toml`, `Makefile` (`up`, `wp-seed`, `test`) and `.env.example`.
- Test tooling: RSpec, FactoryBot, WebMock, VCR, rswag scaffold, Vitest + React Testing Library, Playwright scaffold.
- GitHub Actions workflow for `apps/platform/**`: RuboCop, ESLint, Brakeman, `bundler-audit`, `npm audit`, RSpec (PostgreSQL + pgvector service) and Vitest.
- Health endpoint `/up`.

## Capabilities

### New Capabilities
- `developer-environment`: one-command local stack, reproducible tool versions, CI quality gates on every PR, health check.

### Modified Capabilities
- None.

## Impact

- New: `apps/platform/`, `compose.yaml`, `mise.toml`, `Makefile`, `.env.example`, `.github/workflows/`, `infra/wordpress/`.
- No product behavior yet; every later change builds on this.

## Dependencies

- None.

## Out of scope

- Production deployment (Kamal, VPS, TLS, backups) — see `add-production-deployment`.
- Authentication and any domain tables.

## References

- readme.md §1.4 (installation), §2.3 (file structure), §2.4 (local environment), §2.6 (test strategy).
