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

## Which asset to create

Use **Orchestration → Taskflow**. That is the canvas in your first screenshot (`Taskflow1`). It can hold a Decision.

Do **not** use these, even though they appear in the New Asset dialog:

| What you clicked | Use it? |
|---|---|
| Orchestration → **Taskflow** | Yes. This is the one. |
| Orchestration → Sequential Tasks with Decision | No. That template runs **both** tasks first, then decides. We need the opposite: do not start the episode if clean failed. |
| Orchestration → Parallel Tasks | No. Clean and episode must not run at the same time. |
| Other Assets → **Linear Taskflow** | No. The dialog says so: if you need a decision, create a taskflow instead. Linear Taskflow is a straight list. |
| Other Assets → Mapplet, Masking Task, PowerCenter Task | No. Close that dialog. |

If **Linear Taskflow10** is open, click **Cancel** or close it without Save. If it already saved, delete it from Explore. Do not add tasks to it.

## Do this

**Goal: one taskflow, episode only after clean succeeds.**

`mt_clean_north` and `mt_episode_north` must each be **Valid** and have run once. If `mt_episode_north` is still Invalid from session 4, stop. A taskflow cannot run an invalid mapping task.

1. Close the New Asset dialog with **Cancel**.
2. Open the taskflow you already started (`Taskflow1`). Do not create another one.
3. Click empty canvas (not a step). In **General**, set the name to `tf_meridian_north`. Location `MERIDIAN_PRACTICE`. Save.
4. Click **Data Task 1**. **Data Task** tab. It already shows `mt_clean_north`, type Mapping Task, location `MERIDIAN_PRACTICE/MERIDIAN_PRACTICE`. Leave it. Input Fields **None** is correct. Do not type a task name that does not exist.
5. Your canvas is Start → Data Task 1 → Decision 1 → two empty curves → a merge diamond → End. It is **Invalid** because those two curves have no condition and the episode task is not on either curve.
6. Click **Decision 1**. Open the **Decision** tab. The screen you have is the right one. Do not change the **Field**. It must stay **Data Task 1 → Output Parameters → Task Status**.
   - Path 1: open the **Condition** dropdown and change **Equals** to **Contains**. Delete `Success`, including any quotes. Type `1` and nothing else. Success is the number 1. A warning is 2. The word Success never matches, so the flow takes Otherwise and Data Task 2 never starts.
   - Path 2 stays **Otherwise**. Do not type a value on Path 2.
   - Click empty canvas. The line label changes from "Equals Success" to "Contains 1". If Contains is not in the dropdown, use Equals and the value `1`, still with no quotes.
7. From the left palette, drag **Data Task** and drop it **on the Path 1 line** (the success curve), between Decision 1 and the merge diamond. Do not drop it on the Otherwise curve.
8. Click that new step. **Data Task** tab → **Select** → `mt_episode_north`. Same project. If it is not in the list, the mapping task was never saved or it is still Invalid. Go back to session 4. Do not pick `mt_clean_north` again.
9. Leave the Otherwise curve empty: Decision → merge → End, with no step on it. Both curves must still be connected to the merge, and the merge to End. An unconnected curve is why the header stays Invalid.
10. Do not add a Notification step unless mail is already set up. Do not email a distribution list.
11. **Save**, then **Publish**. Read the next section before you Run. A saved taskflow that was never published does not run. After you change the decision, Publish again. Run uses the last published copy, not the canvas you just saved.

## Save and Publish

| | Save | Publish |
|---|---|---|
| What it writes | The draft on the canvas | A frozen copy of that draft |
| What Run uses | Nothing, if you only saved | The published copy |
| What a schedule uses | Nothing | The published copy |
| Valid / Invalid | Checked on the draft | Publish is refused while Invalid |

Save stores your drawing so you can close the laptop. It does not change the job that already ran. Publish is the handoff: the agent executes that version, and a schedule can start only that version. If you fix the decision and only click Save, the next Run repeats the old published flow (one subtask, Otherwise path). Save, then Publish, then Run.

**Goal: one manual run. No clock yet.**

12. Click **Run** only after Publish. Do not schedule first.
13. Open **My Jobs**. The top row is the taskflow. **Subtasks** must say **2 tasks**, not 1 task. Click **View Subtasks**. You must see `mt_clean_north` and then `mt_episode_north` inside this instance.
14. Older rows named `mt_episode_north-1` or `mt_clean_north-1` with earlier start times are manual runs from sessions 3 and 4. They are not children of this taskflow. Ignore them when you count subtasks. If Subtasks is still 1, the decision is still not Contains `1`, or you did not Publish after the change.

**Goal: a schedule, then turn it off.**

A schedule is not in the New Asset list. Do not look for it under Orchestration or Other Assets. The Schedule radios on a Linear Taskflow ("Do not run" / "Run this") belong to the asset you are not using.

15. In **Explore**, select `tf_meridian_north` (it must be Published). **Actions → Schedule**. If Actions has no Schedule, open the taskflow and use the clock icon in the top toolbar.
16. Name: `sch_meridian_practice_DISABLED`. Bind this schedule to the **taskflow**, not to `mt_clean_north` alone.
17. Repeat: **Does not repeat** if you see it. Otherwise Weekly. Start: **tomorrow**, not the next few minutes. Your time zone, not UTC by accident.
18. If the last page has **Enabled**, clear it before you finish. If the product forces it on, finish, then open the schedule again and set status to **Disabled**.
19. Refresh Explore. Filter or look for the schedule. Status must be Disabled, and Monitor must show nothing queued for it. Write that status in the log. An enabled practice schedule is a failed session even if the taskflow run was green.

## What you should see

- Taskflow header **Valid**. The success line is labeled **Contains 1**, not Equals Success.
- Open the taskflow instance in My Jobs. Two rows, both Success: `mt_clean_north` first (4 rows), then `mt_episode_north` (3 rows). The episode start time is after the clean end time. Those 3 rows are Ava, Noah, and Mia.
- `sch_meridian_practice_DISABLED` exists and is **Disabled**. The chain is not finished until this is true.
- No Linear Taskflow left in the project.

## If it fails

| What you see | What it usually means |
|---|---|
| Header stays Invalid, both curves empty | The episode Data Task is not on Path 1, or a curve does not reach End. |
| Taskflow will not run | Not published. Publish, then Run. |
| `mt_episode_north` is missing from Select | Session 4 mapping task was not saved, or that mapping is still Invalid. |
| Subtasks says **1 task**, status Success | Otherwise ran. Decision is still "Equals Success". Change it to Task Status **Contains** `1`, then Save and **Publish** again. The older mapping-task rows in My Jobs are not this run. |
| You changed the decision but the next run is identical | You Saved and did not Publish. Run still uses the previous published copy. |
| Both ran, but episode started first | You ran the two mapping tasks by hand. Look at the taskflow instance in Monitor, not at old task runs. |
| You are on a page titled Linear Taskflow | Wrong asset. Close it. Use `tf_meridian_north`. |
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
