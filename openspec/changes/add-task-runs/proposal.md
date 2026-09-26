## Why

Ingestion, research, drafting, publishing and Autopilot all run in the background and take from seconds to minutes. Each needs the same things: visible progress, a record of tokens and cost, an audit trail and "only one at a time per site". Building this once prevents every AI change from reinventing polling and bookkeeping.

## What Changes

- Table `runs` (site, kind, trigger source, status, steps log, tokens, cost, error, timestamps, optional `slot_at`).
- `Run` lifecycle: queued → running → succeeded / failed / skipped, with a step-logging API used by jobs.
- Base job concern: creates/updates the run, records errors, applies `limits_concurrency` (one active run per site and kind).
- Usage recording: input/output tokens and estimated cost per run, summable per site and month (used later for budgets).
- `GET /runs/{run_id}` (JSON) and an Inertia prop helper; a reusable React `RunProgress` component using `usePoll` while a run is active.
- Site history view listing recent runs with status, duration and cost.

## Capabilities

### New Capabilities
- `task-runs`: lifecycle, progress reporting, concurrency per site, usage and cost recording, run history.

### Modified Capabilities
- None.

## Impact

- New table: `runs`; new job concern and React component used by every later AI or publishing change.

## Dependencies

- `add-wordpress-site-connection` (runs belong to a site)

## Out of scope

- Budget enforcement (`add-autopilot-guardrails`) and the AI usage dashboard (HU-08, not in MVP).

## References

- readme.md §2.2 (`Usage`, jobs), §3.2 `runs`, §2.5 points 8 and 10.
