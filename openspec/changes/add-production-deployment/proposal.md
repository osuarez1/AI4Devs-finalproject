## Why

The course asks for a project URL (readme.md §0.4), and a real WordPress site can only be connected over HTTPS. The product needs a repeatable, zero-downtime path from `main` to a public server, with secrets, backups and a way back.

## What Changes

- Kamal 2 configuration in `apps/platform/config/deploy.yml`: one VPS, image pushed to GHCR, kamal-proxy with Let's Encrypt TLS, health check on `/up`.
- Solid Queue runs inside Puma (`SOLID_QUEUE_IN_PUMA`), with a documented path to a separate `job` role.
- PostgreSQL 17 + pgvector as a Kamal accessory with a persistent volume.
- Secrets through `.kamal/secrets` read from the CI environment; nothing secret committed.
- Daily `pg_dump` backups to S3-compatible storage, plus a documented restore check.
- GitHub Actions deploy workflow: on merge to `main`, after CI passes, run `kamal deploy`. Rollback with `kamal rollback <version>`.
- Mission Control – Jobs, protected with basic authentication.

## Capabilities

### New Capabilities
- `deployment`: automated deploys, TLS, secrets handling, backups and restore, rollback.

### Modified Capabilities
- `developer-environment`: CI gains a deploy stage after the quality gates (introduced by `bootstrap-platform`).

## Impact

- `config/deploy.yml`, `.kamal/`, `.github/workflows/deploy.yml`; a VPS, a domain and an S3-compatible bucket (external accounts).

## Dependencies

- `bootstrap-platform` (can be done any time after it; needed before Entrega 3 for the project URL).

## Out of scope

- Staging environment, database replicas, autoscaling, external error tracking.

## References

- readme.md §0.4 (URL del proyecto), §2.4 (Producción, proceso de despliegue, observabilidad).
