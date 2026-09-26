## Why

Everything the product does starts from a connected WordPress site: reading its history, learning its voice and publishing to it. The connection must be safe (the URL is user-supplied, so SSRF is a real risk; the Application Password is a credential) and a workspace must be able to connect several sites.

## What Changes

- Table `sites` (per workspace, unique `base_url` per workspace) with name, niche, seed keywords, locale, timezone, connection status and encrypted `wp_app_password`.
- `Wordpress::Client`, the only code allowed to talk to WordPress or read its credentials: discovery (`GET /wp-json/`), verification (`GET /wp-json/wp/v2/users/me?context=edit`), typed errors, short timeouts.
- `Wordpress::UrlGuard`: HTTPS outside development, DNS resolution, blocking of private, loopback, link-local and metadata addresses, re-validated redirects (`ssrf_filter`).
- Verification rules: REST API present, Application Passwords available, `publish_posts` capability; non-blocking warning when the WordPress user is Administrator or Editor.
- Three-step connection wizard (URL and credentials → verification → niche, language, timezone), site list and site switcher, edit (re-verifies on credential change), re-verify and disconnect.
- JSON contract `POST /workspaces/{workspace_id}/sites` (201/401/403/404/422) as documented in `docs/api/openapi.yaml`.
- `rate_limit` on connection attempts; the password never appears in responses, Inertia props or logs.

## Capabilities

### New Capabilities
- `site-connection`: connecting, verifying, editing and disconnecting WordPress sites, credential handling and SSRF protection.

### Modified Capabilities
- None.

## Impact

- New table: `sites`; new service namespace `app/services/wordpress/`; `SitePolicy` (admin writes, editor reads).
- Adds `ssrf_filter` gem.

## Dependencies

- `add-workspaces-and-roles`

## Out of scope

- Ingesting posts after connecting (`add-content-ingestion` adds that trigger).
- Autopilot settings on the site (`add-autopilot-pipeline`, `add-autopilot-guardrails`).
- Publishing (`add-post-publishing`).

## References

- readme.md HU-01 (§5), ticket BE-01 (§6), §2.5 points 3–4, §3.2 `sites`, §4 `createSite`.
