## Why

Retrieved chunks give the model examples, but not explicit rules. A per-site style profile (voice, tone, formality, structure, typical length) turns the site's history into instructions every draft can follow, and its embedding centroid gives the quality gate an objective style-similarity measure.

## What Changes

- Table `style_profiles` (one per site): `profile` jsonb, `style_centroid vector(1536)`, posts analyzed, model, generation date.
- `Intelligence::StyleProfiler`: analyzes a representative sample of ingested posts with `claude-opus-5` using structured output (JSON Schema); computes the centroid from the site's chunk embeddings.
- Generated automatically when ingestion succeeds; can be regenerated on demand.
- Site dashboard shows the profile in readable form; sites with no posts get a warning and can proceed without a profile.

## Capabilities

### New Capabilities
- `style-profile`: generating, storing, displaying and regenerating a site's style profile and centroid.

### Modified Capabilities
- None.

## Impact

- New table: `style_profiles`; first Claude API integration (`anthropic` gem, `ANTHROPIC_API_KEY`, `LLM_MODEL_PRIMARY`).

## Dependencies

- `add-content-ingestion`

## Out of scope

- Manually editing the style profile (candidate follow-up).
- Using the profile in drafts (`add-draft-generation`) or quality scoring (`add-quality-gate`).

## References

- readme.md HU-02 (§5 backlog), §2.2 (`StyleProfiler`), §3.2 `style_profiles`.
