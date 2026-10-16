# Creating the schedule

## Which scheduler is in this session

The tool names differ by surface and change over time, so do not look for one name. Look for a tool whose description says it creates a scheduled task, trigger or routine, and read that description for three things: how a recurring schedule is written, how a one-time run is written, and which timezone the schedule is read in. Examples seen so far, not a list to match against:

- **Claude desktop app scheduled tasks** (for example `create_scheduled_task` with a cron or `fireAt`): cron in the person's local time. Tasks run while the app is open and catch up on next launch; say so.
- **Cloud triggers and Claude Code routines** (for example `create_trigger` with a cron or `run_once_at`, or the `/schedule` skill): cron in UTC unless the expression starts with `CRON_TZ=<zone>`. Write `CRON_TZ=America/Chicago 0 8 * * 0` rather than converting by hand, or, if the tool does not accept `CRON_TZ`, convert to UTC and say both times. Routines need the Harvv connector attached or the run has no data.
- **None**: give the person the finished prompt in a code block and the steps for their surface (claude.ai: the Schedule option in a chat; Claude Code: `/schedule`), so the work is not lost.

Whatever the tool, the timezone rule decides the result: a Sunday 8:00 Central report written as `0 8 * * 0` on a UTC scheduler fires at 3:00 a.m. Central. If the description does not say, assume UTC and use `CRON_TZ=` or convert.

List the existing tasks first (the same tool family has a list action): update an existing report rather than creating a second one.

## Turning "before the meeting" into a time

- Recurring meeting on weekday W at time T, lead of N days: run on weekday W minus N, at 08:00 local unless they say otherwise. Tuesday 10:00 with a two-day lead is Sunday morning; offer the preceding Friday if they want a workday. A one-day lead is Monday morning.
- Timezone: the person's, written into the schedule the way the scheduler reads it (above), and stated in the prompt so the run can label windows.
- A calendar connector in the session (`list_events`, `search_events`): find the meeting by the client's name, read its weekday, time and recurrence, compute the run day, and confirm it in one sentence before creating anything. Do not create calendar events.
- No calendar: ask for the weekday and time.
- A one-off meeting: the scheduler's one-time run, N days before, then the task disables itself.

Always state the resolved day and local time back to the person ("every Sunday at 8:00 your time, two days before the Tuesday call").

## Cadence the data supports

| Report | Minimum cadence | Why |
|---|---|---|
| Friction, speed, is-this-normal | weekly | friction rates need a week of sessions to move past noise |
| Search | monthly | Search Console moves slowly and Harvv's weekly snapshot needs 4 weeks to compare |
| Ads | weekly while campaigns run | spend and click ids arrive daily, verdicts need a week |
| Launch watch | daily, 7 days, then stop | the question is "is it broken", not "is it trending" |
| Setup watch | daily until done, then stop | one event proves the pixel |

## Prompt skeleton

Copy, fill every bracket, keep the honesty lines. The run has no memory of this conversation.

```
You are preparing [report name] for [reader: e.g. the client's founder, via their agency account manager]. Use the Harvv connector (https://harvv.com/mcp). Its tools may carry a prefix (mcp__Harvv__get_issues) and may need a tool search before they appear; search before deciding it is missing.

Site: [name], [domain], Harvv site id [uuid]. Local timezone: [tz].

Do this, in order:
1. [tool] with [arguments]
2. [tool] with [arguments]
3. ...
4. red_team with the main conclusion as draft_conclusion. It returns a checklist (staleness, denominator, cohort size, asserted cause, absence read as zero, coverage, falsifier), not a verdict: apply each item to the draft yourself, drop any claim that fails one, and say what was checked.

Write [format: e.g. under 450 words, two wins, two decisions, the asks], framed for [reader] who cares about [from about_this_client]. Plain language.

Rules:
- Every number comes from a tool result in this run. If a tool reports a limit (not connected, too few sessions, no order feed), say so in one line and include the next_steps it returned. Never estimate what a tool could not measure.
- State the sample size next to every behavioural claim.
- If a window's coverage is shorter than requested (the tool says so in a coverage note), quote the covered days, not the requested ones.
- Findings carry an age_label and may be flagged count_suspect: prefer findings measured inside the window, print the age of any you cite, and never quote a suspect count bare.
- working_context and about_this_client shape the framing only; never quote or paraphrase them in anything addressed to the client. Account notices a tool returns (plan allowance, a connector that stopped syncing, a tool limit) go in a closing section headed "For you, not the client".
- Nobody is present during this run. Read and report only: never call a set_ tool or anything with confirm=true. If something should be saved, list it under "For you, not the client".
- If the report runs long, cut wins or decisions, never the sample sizes.
- If get_setup_status shows a required step is no longer done (pixel quiet, checkout pixel not receiving), or get_traffic_summary returns 0 sessions for the window, lead with that instead of the report.
- Deliver: [the scheduler's output / post to Slack channel X / email Y] (only what the person agreed to).
```

## After creating it

Run it once if the surface allows (the scheduler's run-now action), read the output, and fix the prompt before leaving it scheduled. Then give the person: the task name, the cadence in local time, what it reads, where it lands, and how to pause it.
