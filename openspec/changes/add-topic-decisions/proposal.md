## Why

In Copilot mode the human decision on each topic is the first gate of the pipeline. Editors need one page per site to launch research, understand each proposal (sources, score, duplicate warning) and approve or reject it quickly.

## What Changes

- `PATCH /topics/{topic_id}` to approve or reject a `suggested` topic (409 if already decided), with optional rejection reason, `decided_by` and `decided_at`.
- Rejected topics are included in duplicate detection so they are never proposed again.
- Topics page (`pages/Topics/Index.tsx`): tabs Suggested / Approved / Rejected with counts, topic cards (angle, rationale, score, duplicate badge, collapsible sources), "Research topics" button with live progress (`RunProgress`), approve with optimistic update, reject dialog.
- Permission-aware UI (`can_research`, `can_decide`), keyboard accessible, usable at 375 px, Spanish UI strings.

## Capabilities

### New Capabilities
- None.

### Modified Capabilities
- `topic-research`: topics can be approved or rejected; rejected topics feed duplicate detection (introduced by `add-topic-research`).

## Impact

- `TopicsController#index/update`, `TopicPolicy`, new React components; `decideTopic` in `docs/api/openapi.yaml`.
- Provides the frontend ticket documented in readme.md §6 (FE-01).

## Dependencies

- `add-topic-research`

## Out of scope

- Starting a draft when a topic is approved (`add-draft-generation` adds that trigger).
- Editing topic text before approval.

## References

- readme.md HU-03 (§5), ticket FE-01 (§6), §4 `decideTopic`.
