## Why

Drafts can only sound like a site if the system has the site's own writing to retrieve from, and topic duplicates can only be detected against what the site already published. Ingestion turns a site's recent posts into searchable, embedded chunks.

## What Changes

- Enable the `vector` extension; tables `source_posts` (unique per site and `wp_post_id`, `content_hash`) and `document_chunks` (`vector(1536)` embedding, `embedding_model`, HNSW cosine index, `site_id` for filtered search).
- `Wordpress::Client#recent_posts`: last 50 published posts (`_fields` to limit payload).
- `Intelligence::Chunker` (HTML → text with Nokogiri, split by headings/paragraphs into ~500-token chunks) and `Intelligence::Embedder` (batched embeddings, model recorded per chunk).
- `Intelligence.ingest` facade entry point; `Sites::IngestJob` recorded as a `runs` entry.
- Ingestion starts automatically after a site connects and on demand ("Re-ingest"); unchanged posts (same `content_hash`) are skipped.
- Site dashboard shows ingestion progress and "N posts analyzed".

## Capabilities

### New Capabilities
- `content-ingestion`: fetching recent posts, chunking, embedding, idempotent re-ingestion, per-site similarity search.

### Modified Capabilities
- `site-connection`: a successful connection now starts ingestion (introduced by `add-wordpress-site-connection`).

## Impact

- New tables: `source_posts`, `document_chunks`; pgvector + `neighbor` gem; embeddings API key (`OPENAI_API_KEY`, `EMBEDDING_MODEL`).
- Provides the database ticket documented in readme.md §6 (pgvector schema and HNSW index).

## Dependencies

- `add-wordpress-site-connection`, `add-task-runs`

## Out of scope

- Style profile generation (`add-style-profile`).
- Ingesting drafts, pages or more than the last 50 posts.

## References

- readme.md HU-02 (§5 backlog), §2.2 (`Chunker`, `Embedder`), §3.2 `source_posts`, `document_chunks`.
