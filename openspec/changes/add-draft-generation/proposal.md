## Why

This is the core value of the product: a full blog post, in the site's own voice, from an approved topic. It combines retrieval of the site's closest passages (RAG), the style profile and the topic's sources.

## What Changes

- Table `posts` with the drafting states (`generating`, `pending_review`, `discarded`), title, excerpt, `body_markdown`, origin, run, `lock_version` (publishing columns come later).
- `Intelligence::Drafter`: retrieves the 8 most similar chunks of the site (cosine, HNSW, filtered by site), adds the style profile and topic sources, and generates title, excerpt and Markdown body with structured output in the site's locale.
- Stable prompt prefix (instructions + style profile) uses prompt caching; long generations use streaming; `stop_reason` checked before use.
- Approving a topic starts drafting (`Posts::DraftJob` as a run); "Regenerate" replaces the draft.
- Draft appears in the site's drafts list with status and progress.

## Capabilities

### New Capabilities
- `draft-generation`: generating, regenerating and storing drafts from approved topics with retrieval and style guidance.

### Modified Capabilities
- `topic-research`: approving a topic starts draft generation (introduced by `add-topic-research`, extended by `add-topic-decisions`).

## Impact

- New table: `posts`; `Intelligence.draft` facade entry point.

## Dependencies

- `add-topic-decisions`, `add-style-profile`

## Out of scope

- Quality evaluation (`add-quality-gate`), editing (`add-draft-editor`), publishing (`add-post-publishing`).
- Images, categories and tags.

## References

- readme.md HU-04 (§5 backlog), §2.2 (`Drafter`, Copilot sequence diagram), §3.2 `posts`.
