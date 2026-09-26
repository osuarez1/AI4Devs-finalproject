## Why

Finding what to write about is the most time-consuming step for the product's users. The system should research current topics in each site's niche, justify them with sources, and never propose something the site already covered.

## What Changes

- Table `topics` (per site): title, angle, rationale, sources, `embedding`, `trend_score`, `similarity_to_existing`, status (starts as `suggested`), origin, run.
- `Intelligence::TopicResearcher`: Claude (`claude-opus-5`) with the server-side `web_search` tool, structured output of 5–10 candidates with at least 2 sources each, written in the site's locale.
- Duplicate detection: candidates above 0.85 similarity with the site's chunks or previous topics are dropped; 0.70–0.85 are flagged "possible duplicate" (thresholds configurable).
- `trend_score` definition: source recency and volume combined with the model's niche-fit assessment (documented, not a Google Trends value).
- `POST /sites/{site_id}/topic_research` → 202 with the run; one research run per site at a time.
- Web content is treated as untrusted data (prompt-injection guidance in the system prompt; instructions found in pages are never followed).

## Capabilities

### New Capabilities
- `topic-research`: researching, scoring, de-duplicating and storing topic proposals per site.

### Modified Capabilities
- None.

## Impact

- New table: `topics`; `Intelligence.research` facade entry point; `Topics::ResearchJob` as a run.

## Dependencies

- `add-content-ingestion` (duplicate detection), `add-task-runs`

## Out of scope

- Approving and rejecting topics and the full topics page (`add-topic-decisions`).
- Scheduled or automatic research (`add-autopilot-pipeline`).

## References

- readme.md HU-03 (§5), §2.2 (`TopicResearcher`), §2.5 point 5, §3.2 `topics`.
