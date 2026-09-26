## Why

Humans reviewing a draft need an objective summary of its problems, and Autopilot cannot publish anything without automated checks. One quality gate serves both: informative in Copilot, blocking in Autopilot.

## What Changes

- `Intelligence::QualityGate` combining:
  - deterministic, blocking rules: word count within bounds, links only to allowed domains, HTML output that survives the allowlist sanitizer;
  - an LLM judge (`claude-haiku-4-5`, structured output) scoring style fit and flagging risky claims;
  - style similarity between the draft embedding and the site's style centroid.
- A `quality_score` (0–1) and a `quality_report` jsonb (scores, violations with codes such as `disallowed_link`) stored on the post.
- Evaluation runs automatically after drafting and on demand; the report is shown with the draft.
- `bin/rails intelligence:eval` task over a small reference set to tune thresholds (run on demand, not in CI).

## Capabilities

### New Capabilities
- `quality-gate`: rule checks, LLM judging, style similarity, scoring and reporting.

### Modified Capabilities
- `draft-generation`: every draft is evaluated and carries a quality report (introduced by `add-draft-generation`).

## Impact

- `posts.quality_report`; `Intelligence.evaluate` facade entry point; `LLM_MODEL_FAST`.

## Dependencies

- `add-draft-generation`

## Out of scope

- Blocking publication based on the report (applies only in `add-autopilot-pipeline`).
- Plagiarism or fact-checking services.

## References

- readme.md §2.2 (`QualityGate`), §2.5 point 5, §2.6 (AI evaluation), HU-04 and HU-06.
