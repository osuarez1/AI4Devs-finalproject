## Why

Consistent cadence matters more than publishing immediately. Editors need to schedule posts in the site's timezone, and Autopilot needs defined publishing slots to fill.

## What Changes

- Scheduling on publication: `publish_at` in the future sets `scheduled` and enqueues the publish job with `wait_until` (the platform publishes; WP-Cron is not used). Scheduled posts can be cancelled.
- Table `schedules` (per site): daily or weekly frequency, days of week, local publish time, active flag; timezone taken from the site.
- Slot calculation service: next N slots in the site's local time, skipping slots already taken.
- Calendar page per site: scheduled posts and upcoming slots; the publish dialog suggests the next free slot.

## Capabilities

### New Capabilities
- `publishing-calendar`: per-site publishing slots, slot calculation and calendar view.

### Modified Capabilities
- `publishing`: posts can be scheduled for a future time and cancelled (introduced by `add-post-publishing`).

## Impact

- New table: `schedules`; `posts.scheduled_for` used; recurring slot logic reused by `add-autopilot-pipeline`.

## Dependencies

- `add-post-publishing`

## Out of scope

- Autopilot filling slots automatically (`add-autopilot-pipeline`).
- Rescheduling by drag and drop.

## References

- readme.md HU-05, HU-06 (§5), §2.2 (Programación de la publicación), §3.2 `schedules`.
