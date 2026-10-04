# Session 4 — Lookup, joiner, aggregator: Ava's episode of care

**Time:** 80 minutes. **Requires:** session 3 clean file has 3 rows.

## The idea in one minute

A patient row is not the story. The story is the **episode of care**: office visit, lab, follow-up, possibly in different hospitals, under different doctors. Those facts live in other files. You attach them.

| Transformation | Question it answers | Meridian use |
|---|---|---|
| Lookup | What is the name for this code? | `NJ` → `New Jersey`. Also `HMH-HACK` → hospital name |
| Joiner | Which rows belong together? | Patient `MRN1001` + encounters `E1, E2, E3` |
| Aggregator | What is the total per person? | Ava has 3 encounters in 2 hospitals |

Lookup is not a join. Lookup expects the lookup table to return **one** match per incoming row (a code list). Joiner is for when **many** child rows are correct. If you lookup encounters, you silently keep one visit and lose the rest. That bug hides patients.

## Meridian example

After this job, one row per patient:

| Person | Encounters | Hospitals |
|---|---|---|
| Ava Shah `MRN1001` | 3 | 2 |
| Noah Patel `MRN1002` | 1 | 1 |
| Mia Chen `MRN1003` | 0 | 0 |

Mia has no encounter. Use an outer join so she still appears. An inner join would delete her. Deleting a patient because they had no visit this month is wrong.

## Do this

Build one mapping, `m_episode_north`, in `MERIDIAN_PRACTICE`.

**Goal: bring the clean patient, not the raw file.**

1. Source 1 name `src_patient`. Connection `FF_MERIDIAN_PRACTICE`. Object `patients_north_clean.csv`. This file exists only because session 3 ran.

**Goal: translate the state code. One code, one name. Lookup.**

2. Source is not the right tool for a code list you only want to attach. Drag **Lookup** (connected lookup).
3. Connect `src_patient` → Lookup.
4. Lookup connection: same flat file. Lookup object: `state_codes.csv`.
5. Condition: `state` = `state_code`.
6. Return `state_name`.
7. On multiple matches: report error (a code list must be unique). On no match: keep the row, `state_name` null. `ZZ` on the reject file is not in this flow. North clean rows are all `NJ`, so you expect `New Jersey` on every row.

**Goal: attach every encounter. Joiner, not lookup.**

8. Add Source 2 named `src_encounter`. Object `encounters.csv`.
9. Drag **Joiner**. Connect Lookup output (the patient side, now with `state_name`) into the Joiner **master**. Connect `src_encounter` into the **detail**.
10. Join type: **Master outer** (left outer / master outer). Master is the patient. Detail is the encounter. Patients with no visit survive.
11. Join condition: `mrn` = `mrn`.
12. You now have one row per encounter, and Mia still appears once with empty encounter fields.

**Goal: also name the hospital. Second lookup, after the joiner, because the hospital code is on the encounter.**

13. Drag Lookup 2. Connect Joiner → Lookup 2.
14. Lookup object `locations.csv`. Condition `hospital_code` = `hospital_code`. Return `hospital_name`.
15. Multiple match: error. No match: continue (Mia).

**Goal: one summary row per patient.**

16. Drag **Aggregator**. Connect Lookup 2 → Aggregator.
17. Group by: `mrn`, `first_name`, `last_name`.
18. Add aggregate outputs:

| Field | Expression |
|---|---|
| `encounter_count` | `COUNT(encounter_id)` |
| `hospital_count` | `COUNT(hospital_code, true)` or distinct count if the function picker has `COUNT DISTINCT` style. If there is no distinct flag, use a nested approach the editor allows, often a second argument or a checkbox **Distinct**. |

19. If distinct count is awkward in your release, skip `hospital_count` and instead pass a sorted list. The required proof is `encounter_count`. For Ava it must be **3**. For Noah **1**. For Mia **0** or null treated as 0. If Mia shows 1 because `COUNT` counts a null, use `COUNT(encounter_id)` only when `encounter_id` is not null — the function usually ignores nulls. Check Mia.

20. Target: `episode_north_summary.csv`. Map `mrn`, `first_name`, `last_name`, `state_name`, `encounter_count`, and `hospital_count` if you built it.
21. Validate. Save. Mapping task `mt_episode_north`. Same runtime. Run. No schedule.

## What you should see

Open `episode_north_summary.csv`.

- 3 data rows.
- Ava / `MRN1001` / `New Jersey` / encounter_count **3**.
- Noah / encounter_count **1**.
- Mia / encounter_count **0** or blank. She is **present**. If she is missing, the join was inner. Fix the join type and run the task again. Do not start session 5 on a wrong file.

This is the small version of "show the whole episode of care". The real program did it after MDM so the MRN was already one person across EMRs. You joined only North's clean file, so South-only Leo is not here. That is expected. MDM in later sessions is what makes North Ava and South Ava the same person **before** the episode is counted.

## If it fails

| What you see | What it usually means |
|---|---|
| Ava count is 1 | You used Lookup for encounters. Rebuild that step as a Joiner. |
| Mia is missing | Inner join. Change to master outer. |
| `state_name` empty | Join condition uses the wrong fields, or `state_codes.csv` is not in the agent folder. |
| Aggregator error on group by | Every non-aggregated output must be in the group by. Add `first_name` and `last_name` to the group. |
| Duplicate hospital count equals 3 for Ava | You counted rows, not distinct hospitals. Ava has 3 visits and 2 hospitals. Distinct is the one that matches the story. |

## Write this in the session log

```text
mt_episode_north run id:
Ava encounter_count:
Noah encounter_count:
Mia present: yes/no
```

## Answer before session 5

1. Why is a lookup the wrong tool for encounters?
2. Why must Mia survive a join even with zero visits?

## Stop

Next sitting chains the jobs you already have and puts a clock on them — then turns the clock off: [05-taskflow-schedule-the-job.md](05-taskflow-schedule-the-job.md).
