## Why

Autopilot is the product's differentiator: keeping a site's publishing cadence without manual work. It reuses the whole Copilot pipeline (research → draft → evaluate → publish), replacing the human decisions with automatic checks. This change delivers the pipeline itself; the safety limits come in `add-autopilot-guardrails`.

## What Changes

- Site settings: `autopilot_mode` (off / review / auto) and the topic-policy and quality keys of `guardrails` (minimum quality score, word bounds, blocked topics, allowed link domains, lead time).
- `AutopilotTickJob` (Solid Queue recurring task, every 15 minutes) finds sites with Autopilot on and a slot within the lead time (24 h by default), and enqueues `AutopilotRunJob`.
- One run per site and slot (partial unique index on `runs` for `kind = 'autopilot'`), never two concurrently for the same site.
- Pipeline per slot: research → topic policy (niche fit, duplicates, blocked topics) → draft → quality gate, each step recorded in the run.
  - review mode: the draft stays pending review and editors get an email; if nobody approves it before the slot, the slot is not published.
  - auto mode: the post is scheduled for the slot only if every check passes; otherwise it goes to pending review with the reasons.
- Autopilot settings page (admin only) showing mode, the next 4 slots in the site's local time and the pipeline settings.

## Capabilities

### New Capabilities
- `autopilot`: modes, slot-driven runs, topic policy, review and auto behavior, editor notifications.

### Modified Capabilities
- None.

## Impact

- `sites` gains `autopilot_mode` and `guardrails`; `config/recurring.yml`; review-request mailer.

## Dependencies

- `add-publishing-schedules`, `add-quality-gate`

## Out of scope

- Weekly and budget limits, auto-pause, admin alerts and the audit view (`add-autopilot-guardrails`).
- Several posts per slot or content types other than posts.

## References

- readme.md HU-06 (§5), §1.2 point 8, §2.2 (Flujo Autopilot), §3.2 `sites`, `runs.slot_at`.
