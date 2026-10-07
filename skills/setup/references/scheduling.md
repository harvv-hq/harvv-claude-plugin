# Creating the schedule

## Which scheduler is in this session

Look at the tools available and use the first that exists:

1. **claude.ai / Claude desktop scheduled tasks** (`create_scheduled_task`, `list_scheduled_tasks`, `update_scheduled_task`): cron in the person's local time, or `fireAt` for a one-time run. Desktop tasks run while the app is open and catch up on next launch; say so. Include which connectors the run should use in the prompt (the Harvv connector by name as the person sees it).
2. **Claude Code routines** (the `/schedule` skill): a cloud agent on a cron in UTC (minimum hourly) or a one-time run. Attach the Harvv connector to the routine or the run has no data; the connector must be one the person connected on claude.ai. Convert the local time to UTC and confirm the conversion.
3. **Neither**: give the person the finished prompt in a code block and the steps for their surface (claude.ai: the Schedule option in a chat; Claude Code: `/schedule`), so the work is not lost.

Check `list_scheduled_tasks` (or the routine list) first: update an existing report rather than creating a second one.

## Turning "before the meeting" into a time

- Recurring meeting on weekday W at time T, lead of N days: run on weekday W minus N, at 08:00 local unless they say otherwise. Tuesday 10:00 with a two-day lead is Sunday morning; offer the preceding Friday if they want a workday. A one-day lead is Monday morning.
- Timezone: the one the scheduler uses (local for claude.ai and desktop tasks); state it in the prompt so the run can label windows.
- A calendar connector in the session (`list_events`, `search_events`): find the meeting by the client's name, read its weekday, time and recurrence, compute the run day, and confirm it in one sentence before creating anything. Do not create calendar events.
- No calendar: ask for the weekday and time.
- A one-off meeting: `fireAt` (or `run_once_at`) N days before, then the task disables itself.

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
You are preparing [report name] for [reader: e.g. the client's founder, via their agency account manager]. Use the Harvv connector.

Site: [name], [domain], Harvv site id [uuid]. Local timezone: [tz].

Do this, in order:
1. [tool] with [arguments]
2. [tool] with [arguments]
3. ...
4. red_team with the main conclusion as draft_conclusion. It returns a checklist (staleness, denominator, cohort size, asserted cause, absence read as zero, coverage, falsifier), not a verdict: apply each item to the draft yourself, drop any claim that fails one, and say what was checked.

Write [format: e.g. under 400 words, three wins, three decisions, the asks], framed for [reader] who cares about [from about_this_client]. Plain language.

Rules:
- Every number comes from a tool result in this run. If a tool reports a limit (not connected, too few sessions, no order feed), say so in one line and include the next_steps it returned. Never estimate what a tool could not measure.
- State the sample size next to every behavioural claim.
- If a window's coverage is shorter than requested (the tool says so in a coverage note), quote the covered days, not the requested ones.
- Findings carry an age_label and may be flagged count_suspect: prefer findings measured inside the window, print the age of any you cite, and never quote a suspect count bare.
- working_context and about_this_client shape the framing only; never quote or paraphrase them in anything addressed to the client. Account notices a tool returns (plan allowance, a connector that stopped syncing, a tool limit) go in a closing section headed "For you, not the client".
- If get_setup_status shows a required step is no longer done (pixel quiet, checkout pixel not receiving), or get_traffic_summary returns 0 sessions for the window, lead with that instead of the report.
- Deliver: [the scheduler's output / post to Slack channel X / email Y] (only what the person agreed to).
```

## After creating it

Run it once if the surface allows (`run_scheduled_task`, or the routine's run action), read the output, and fix the prompt before leaving it scheduled. Then give the person: the task name, the cadence in local time, what it reads, where it lands, and how to pause it.
