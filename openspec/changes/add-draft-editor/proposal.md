## Why

In Copilot mode a person always reads and usually adjusts the draft before it goes live. Several editors can work on the same site, so edits must never silently overwrite each other.

## What Changes

- Draft editor page: Markdown editor with live preview (preview renders only sanitized HTML), title and excerpt fields, quality report panel.
- Save with optimistic locking (`lock_version`): a stale save shows a conflict with the option to reload.
- Re-run the quality evaluation after saving; discard a draft.
- Drafts list per site with status filters.

## Capabilities

### New Capabilities
- `draft-review`: viewing, editing, previewing, saving with conflict detection and discarding drafts.

### Modified Capabilities
- None.

## Impact

- `PostsController#show/update/destroy`, `PostPolicy`, editor components; Markdown rendering shared with publishing.

## Dependencies

- `add-quality-gate`

## Out of scope

- Real-time collaborative editing, version history, WYSIWYG editing.
- Publishing (`add-post-publishing`).

## References

- readme.md HU-04 (§5 backlog), §1.2 point 6, §1.3 (Editor de borrador), §3.2 `posts.lock_version`.
