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

The canvas you want for this piece only: `src_patient` → **Lookup** → (later steps). A temporary Target after the Lookup is fine while you test. Do not look up `encounters.csv` here.

2. Click the mapping name (`Mapping841` or whatever the default is). In **General**, set the name to `m_episode_north` and the location to `MERIDIAN_PRACTICE`.
3. Drag **Lookup** onto the canvas. Connect `src_patient` → Lookup. This is a connected lookup. Do not use an unconnected lookup.
4. Click the Lookup. Use the tabs on the left in this order.

**Lookup Object**

5. Connection: `FF_MERIDIAN_PRACTICE`. Source type: Single Object. Object: `state_codes.csv`.
6. If the field list does not show `state_code` and `state_name`, the file is not in the agent folder from session 0. Stop and copy it there. Do not pick the patient file as the lookup object.

**Lookup Condition**

7. Lookup Condition: **Simple**. Not Advanced.
8. One row only:

| Lookup Field | Operator | Incoming Field |
|---|---|---|
| `state_code` | `=` | `state` |

9. Click the **Operator** cell. It is empty until you pick `=`. An empty operator is why the mapping stays **Invalid** even when both field names look right. Lookup Field is the column in `state_codes.csv`. Incoming Field is the column on the patient row. Do not reverse them.

**Return Fields**

10. Leave **Retain existing fields at runtime** unchecked.
11. Check **only** `state_name`. Leave `state_code` unchecked. The patient row already has `state`. You are adding the name.
12. Patient fields (`first_name`, `mrn`, and the rest) pass through on their own. You do not re-check them on this tab.

**Advanced**

13. On this screen Advanced is only the lookup cache. Leave it:
    - Tracing Level: **Normal**
    - Lookup Cache Directory Name: `$PMCacheDir`
    - Lookup Cache Persistent: unchecked
    - Lookup Data Cache Size: **Auto**
14. Do not type a cache file name. Do not switch cache size to Value. There is no "multiple matches" dropdown on this tab in this release. A one-row code list (`NJ` once) does not need it.
15. Unmatched rows still flow, and `state_name` is null. North clean rows are all `NJ`, so you should see `New Jersey` on every row.

**Goal: clear the 3 Target errors so the mapping is valid.**

Validation that says only this is the Target, not the Lookup. Do not go back and edit the cache.

- Field mapping must contain at least one mapped field
- Connection is not specified
- An object is not specified for the connection

16. Click the **Target** box on the canvas, not the Lookup.
17. Connection: `FF_MERIDIAN_PRACTICE`. This removes "Connection is not specified."
18. Target object: create `patients_north_with_state.csv` (new flat-file object, comma-delimited, header row). This removes "An object is not specified." You will change this file name at the end of the session, after the aggregator. Do not point this target at `patients_north_clean.csv`. That file is your source.
19. Open **Field Mapping**. Click **Automap**. You need at least `mrn`, `first_name`, `last_name`, `state`, and `state_name` mapped. This removes "Field mapping must contain at least one mapped field."
20. If `state_name` is not in the incoming list, the return field was not saved. Go back to Lookup → **Return Fields**, check `state_name`, Save, then Automap again.
21. Click **Save**. Validation should be **0**. Rename the mapping from `Mapping841` to `m_episode_north` if you have not already. Location: `MERIDIAN_PRACTICE`.
22. Optional proof before the joiner: mapping task `mt_state_lookup_check`, same runtime, no schedule. Run once. `patients_north_with_state.csv` must have 3 rows and `state_name` = `New Jersey` on all of them. Then come back to the mapping. Do not start the joiner on a file that still says `NJ` only.

**Goal: attach every encounter. Joiner, not lookup.**

Your canvas can already be `src_patient` → Lookup → Joiner → Target, Join Type **Master Outer**, and the mapping **Valid**. That is not finished. If the Detail dropdown says **Fields (0 of 0)**, the encounter file is not connected. Do not pick **New Input Parameter**.

23. Drag a second **Source** onto the canvas. Name it `src_encounter`. Connection `FF_MERIDIAN_PRACTICE`. Object `encounters.csv`. It is not on the canvas yet. The Lookup is not a substitute for it.
24. The Joiner has two input dots. One is already linked from Lookup. Connect `src_encounter` to the **other** input dot. Do not connect `src_encounter` to the Target.
25. Click the Joiner → **Join Condition**.
    - Join Type: **Master Outer**. Leave it. Master is the patient, so Mia survives with no visit.
    - Join Condition: **Simple**.
    - The Master dropdown must say **Lookup**. The Detail dropdown must say **src_encounter**. If they are swapped, swap them. Master is not the encounter file.
26. One condition row:

| Master | Operator | Detail |
|---|---|---|
| `mrn` | `=` | `mrn` |

27. Click the **Operator** cell and pick `=`. It stays blank until you do.
28. Open the **Detail** dropdown. It must list fields from `encounters.csv`, including `mrn`. Pick `mrn`.
    - **Fields (0 of 0)** means step 24 missed the open input dot. Delete that link and connect `src_encounter` again.
    - **New Input Parameter** is the wrong row. Do not create a parameter.
29. Open **Incoming Fields**. Leave both rules as Include → All Fields. Expand **Included Fields**. You should see a Master group (about 18 fields, including `state_name`) and a Detail group (`encounter_id`, `mrn`, `hospital_code`, and the rest).
30. The yellow line **Field name conflicts detected** is not optional. Both sides have `mrn`. The mapping can still say Valid, but do not Run until the warning is gone. Two output columns cannot both be named `mrn`.
31. Fix it on the **Detail** row only. Do not rename Master. Patient fields must stay `mrn`, `first_name`, `last_name`, `state_name`.
    - Click **Rename...** on the Detail rule (the link after "All Fields").
    - Choose a prefix. Type `enc_`. Apply.
    - Detail outputs become `enc_mrn`, `enc_encounter_id`, `enc_hospital_code`, and so on.
    - Or click the blue **Resolve Field Name Conflicts** link and accept a rename of the Detail copies only. If it offers to rename Master, cancel and use the prefix instead.
32. The join condition does not change. It stays Master `mrn` = Detail `mrn`. Do not put `enc_mrn` in the condition. That name exists only on the way out of the Joiner.
33. Save. The yellow warning should disappear. You now have one row per encounter. Mia still appears once, with empty encounter fields. Ava appears three times. Do not run the final summary yet if the Target is still `patients_north_with_state.csv`.

**Goal: also name the hospital. Second lookup, after the joiner, because the hospital code is on the encounter.**

34. Drag a second **Lookup**. Connect Joiner → this Lookup. Do not connect it to `src_patient`.
35. Same tabs as the first lookup:
    - **Lookup Object:** `FF_MERIDIAN_PRACTICE` / `locations.csv`. That file has `hospital_code`, `hospital_name`, `city`, `state`, `parent_code`.
    - **Lookup Condition:** Simple. Add one row. Lookup Field `hospital_code`, Operator `=`, Incoming Field `enc_hospital_code`. Use incoming `hospital_code` only if you did not prefix the Detail fields. The condition row must not be left blank.
    - **Return Fields:** check **only** `hospital_name`. Uncheck `hospital_code`, `city`, `state`, and `parent_code`. The patient row already has `city` and `state`. The encounter row already has the hospital code. Returning those again is the yellow warning "Field name conflicts detected" between incoming fields and return fields.
    - Leave **Retain existing fields at runtime** unchecked.
    - **Advanced:** leave the cache on **Auto** and `$PMCacheDir`.
36. Unchecking `city` and `state` is not enough. Validation can still say "lookup return fields: city, state" while those boxes are clear. `locations.csv` contains `city` and `state`, and the patient row already has those names, so the lookup object itself clashes.
37. On Lookup_2, at the top of **Return Fields**, uncheck **Retain existing fields at runtime** if it is checked. Only `hospital_name` stays checked.
38. Click the blue **Resolve Field Name Conflicts** link in the yellow box on Lookup_2. Let it rename the lookup copies (`city` and `state` from `locations.csv`). Do not rename the patient `city` or `state`. After it finishes, confirm `hospital_name` is still the only checked return field. The renamed `city` and `state` from the location file stay unchecked.
39. **Lookup Condition** is still empty until you add a row. Lookup Field `hospital_code`, Operator `=`, Incoming Field `enc_hospital_code` (or `hospital_code` if you did not prefix Detail). Click the Operator cell and pick `=`.
40. The other red rows are not this lookup. **Joiner** and **Target** still have duplicate names, usually `mrn`. On the Joiner, **Incoming Fields**, Detail rule, click **Rename...**, prefix `enc_`. Do not rename Master. Do not change the join condition. That clears the Joiner error and the Target error together.
41. **Save.** Click the refresh icon on Validation. It should drop to 0. Mia has no encounter, so her `hospital_name` stays empty. That is correct. Do not drop her.

**Goal: one summary row per patient.**

42. Drag **Aggregator**. Connect the second Lookup → Aggregator. Disconnect the Target from the Joiner and connect it after the Aggregator. Change the target object from `patients_north_with_state.csv` to `episode_north_summary.csv`. Automap again. The old state-check file can stay on disk.
43. Group by: `mrn`, `first_name`, `last_name`.
44. Add aggregate outputs:

| Field | Expression |
|---|---|
| `encounter_count` | `COUNT(enc_encounter_id)` — use `COUNT(encounter_id)` only if you did not prefix Detail fields |
| `hospital_count` | `COUNT(enc_hospital_code, true)` or the same name without the `enc_` prefix |

45. If distinct count is awkward in your release, skip `hospital_count`. The required proof is `encounter_count`. For Ava it must be **3**. For Noah **1**. For Mia **0** or blank. If Mia shows 1 because `COUNT` counts a null, check that the function ignores nulls. `COUNT(enc_encounter_id)` usually does.

46. On the Target, confirm the object is now `episode_north_summary.csv`. Map `mrn`, `first_name`, `last_name`, `state_name`, `encounter_count`, and `hospital_count` if you built it.
47. Validate. Save. Mapping task `mt_episode_north`. Same runtime as session 1. Run. No schedule.

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
| Lookup_2 still says city, state after those boxes are unchecked | Uncheck **Retain existing fields at runtime**, then click **Resolve Field Name Conflicts** on Lookup_2. Do not check `city` or `state` again. |
| Validation lists Target and Joiner as well as Lookup_2 | The Joiner Detail fields were not prefixed. Detail rule → **Rename...** → prefix `enc_`. |
| Mia is missing | Join Type is not Master Outer. Change it. Master must be Lookup, not the encounter source. |
| Validation (3) all say Target | The Lookup is already fine. Click the Target. Set connection `FF_MERIDIAN_PRACTICE`, object `patients_north_with_state.csv`, then Automap. Do not change Lookup Advanced. |
| `state_name` empty | Operator points at the wrong fields, `state_name` is not checked on Return Fields, or `state_codes.csv` is not in the agent folder. |
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
