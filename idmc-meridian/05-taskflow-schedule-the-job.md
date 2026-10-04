# Session 5 — Taskflow and schedule: one job that runs the chain

**Time:** 60 minutes. **Requires:** `mt_clean_north` and `mt_episode_north` have each succeeded once.

## The idea in one minute

A hospital load is not one mapping. Land, then clean, then build the episode. If clean fails, the episode must **not** run, or it will summarize yesterday's file and look successful. A **taskflow** is the job that enforces that order.

A **schedule** does not contain logic. It only starts a task or a taskflow at a time. Leaving a practice schedule enabled is how a DEV agent fills a disk at 2 a.m. You will create a schedule, run it once by hand, then **disable** it before you close the laptop.

Linear taskflow (a straight line of tasks) is enough when there is no branch. Use a full **taskflow** here so you can put a decision: episode runs only after clean succeeds.

## Meridian example

```text
Start
  → mt_clean_north
      → success → mt_episode_north → End
      → fail → End (do not summarize)
```

You are not landing again. Session 2 already landed the raw file. This chain starts at clean.

## Do this

**Goal: a taskflow with a real branch.**

1. Data Integration → **New** → **Taskflows** → **Taskflow**. Name: `tf_meridian_north`. Location: `MERIDIAN_PRACTICE`.
2. From the step palette, add a **Data Task** (wording may be "Data task"). Set it to mapping task `mt_clean_north`.
3. Add a **Decision** or use the success/error path the Data Task already exposes. Different releases draw this in two ways:
   - If the Data Task has a green success path and a red error path, use those.
   - If you need a Decision step, condition is "previous task succeeded".
4. On the **success** path only, add a second Data Task: `mt_episode_north`.
5. On the **fail** path, go to End. Do not add the episode task there.
6. Optional: a Notification step on the fail path to your own email. Skip if mail is not set up. Do not email a distribution list.
7. Save. **Publish** if the button exists. Taskflows often do not run until published. A saved-but-unpublished taskflow is a common "nothing happened".

**Goal: one manual run of the whole chain.**

8. Run the taskflow. Do not schedule yet.
9. Open Monitor. You should see the taskflow **and** the two child tasks.
10. Confirm a new run of `mt_episode_north` started **after** `mt_clean_north` finished. The timestamps are the proof of order.
11. If you want a negative test: only do this if you can undo it in one minute. Point is optional. Skip the negative test if you are tired. The positive run is enough.

**Goal: a schedule you then turn off.**

12. **New** → **Schedule** (sometimes under Tasks, or the Schedule tab on the taskflow). Name: `sch_meridian_practice_DISABLED`.
13. Attach it to `tf_meridian_north`, not to a single mapping task.
14. Set it to run **once**, tomorrow, or weekly — then **disable** it before save if the UI has a status. If the only way to create it is enabled, save, run nothing, and immediately set status to **Disabled** / unschedule.
15. Refresh and confirm the schedule is not next-to-run. Write the status in the log.

## What you should see

- One successful taskflow instance.
- Child order: clean, then episode.
- Schedule exists and is **disabled**.

## If it fails

| What you see | What it usually means |
|---|---|
| Taskflow will not run | Not published. |
| Only the first task ran, flow says success, second did not | Second task is not on the success path. |
| Both ran, but episode started first | They are not in one taskflow. You ran them by hand. |
| Schedule fired after you left | It was left enabled. Disable it now. Delete it if you cannot disable it. |

## Write this in the session log

```text
Taskflow run id:
Clean child run id:
Episode child run id:
Schedule status: disabled
```

## Answer before session 6

1. What does a schedule add that a taskflow does not?
2. Why must the episode task sit on the success path only?

## Stop

Next sitting makes one mapping serve more than one state: [06-parameters.md](06-parameters.md).
