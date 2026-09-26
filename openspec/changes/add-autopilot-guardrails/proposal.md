## Why

An Autopilot that keeps publishing when something goes wrong, or keeps spending past a budget, is a risk to the site's brand and to the customer's bill. These limits are what make auto mode safe to switch on, and they deserve their own specification and tests.

## What Changes

- Frequency and budget limits in `guardrails`: max posts per week and monthly budget (in USD, summed from `runs` cost). When preparing a slot would exceed either, the run is marked `skipped` with the reason and no AI call is made.
- Consecutive-failure tracking: after `max_consecutive_failures` (default 3) failed or rejected runs, Autopilot pauses itself on that site (`autopilot_paused_at`) and admins get an email.
- Manual pause and resume; pausing never cancels publications already scheduled.
- Audit view per site: every Autopilot run with its decisions, scores, tokens, cost and outcome.

## Capabilities

### New Capabilities
- None.

### Modified Capabilities
- `autopilot`: adds frequency and budget limits, auto-pause, alerts and the audit view (introduced by `add-autopilot-pipeline`).

## Impact

- `sites.autopilot_paused_at`, remaining `guardrails` keys; budget query over `runs`; pause-alert mailer.

## Dependencies

- `add-autopilot-pipeline`

## Out of scope

- AI usage dashboard across sites (HU-08, not in MVP).
- Per-model or per-provider spending caps.

## References

- readme.md HU-06 (§5: límites, pausa automática, auditoría), §2.5 point 8, §3.2 `sites.guardrails`, `runs`.
