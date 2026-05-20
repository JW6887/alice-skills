# Cron Job Gotchas

## enabled_toolsets causes silent failure

**Symptom**: `cronjob(action="create", ...)` returns `{"error": "job_id is required for action ''"}` even though `action="create"` is correctly passed.

**Cause**: Passing `enabled_toolsets` parameter in the create call triggers an internal bug where the action parameter is dropped/misparsed.

**Fix**: Omit `enabled_toolsets` entirely. All cron jobs default to having web/search/browser tools available. Add it later via `cronjob(action="update", job_id=..., enabled_toolsets=[...])` if needed.

**Verified**: 2026-05-15, across multiple job creation attempts. Pattern is consistent: with enabled_toolsets → fails; without → succeeds.

## Jobs created but never run

**Symptom**: `cronjob list` shows jobs with `schedule` set, `state: scheduled`, but `last_run_at: null` hours after creation.

**Cause**: Cron schedules starting at `0 6 * * *` created at 14:00 will not fire until next 06:00. If created on May 15 with schedule `0 12 * * *`, the 12:00 slot has already passed.

**Fix**: After creating a cron job, immediately call `cronjob(action="run", job_id=...)` to execute the first round manually. Also check `next_run_at` to verify when the next automatic run will occur.

## Verify output, not just state

**Symptom**: Jobs show `last_status: ok` but produce empty files or no output.

**Fix**: After every `cronjob run`, check the output directory with `find ~/.hermes/survival/ -type f -newer <timestamp_file>` to verify actual content was produced. `state: scheduled` or `last_status: ok` does not guarantee output.
