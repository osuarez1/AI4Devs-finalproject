## Why

A reviewed draft only creates value once it is live on the site. Publishing must be safe (sanitized HTML, least-privilege WordPress user), reliable (retries, visible failures) and give the editor the live URL.

## What Changes

- Publishing columns on `posts`: `publishing`, `published`, `failed` states, `wp_post_id`, `live_url`, `published_at`, `last_error`, `publish_attempts`.
- Markdown → HTML (`commonmarker`) → allowlist sanitizer before sending.
- `Wordpress::Client#create_post` (`POST /wp-json/wp/v2/posts`, `status: publish`); `Posts::PublishJob` as a run with exponential-backoff retries for transient errors.
- `POST /posts/{post_id}/publication` (publish now) with `lock_version` check (409 on stale version), as documented in `docs/api/openapi.yaml`.
- The published post is fed back into `source_posts` so duplicate detection and style retrieval include it.
- The editor shows the live URL or the failure reason with "Retry".

## Capabilities

### New Capabilities
- `publishing`: publishing approved posts, sanitization, retries, failure handling and live URL.

### Modified Capabilities
- None.

## Impact

- `posts` gains publishing columns; `publishPost` contract; adds `commonmarker`.

## Dependencies

- `add-draft-editor`

## Out of scope

- Scheduling for a future date (`add-publishing-schedules`).
- Updating or unpublishing posts already on WordPress, categories, tags, featured images.

## References

- readme.md HU-05 (§5 backlog), §2.5 points 3 and 5, §4 `publishPost`, §3.2 `posts`.
