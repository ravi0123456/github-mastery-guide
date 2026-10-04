# Session 6 — Parameters: one mapping, many hospitals

**Time:** 45 minutes. **Requires:** session 3 expression skills. The taskflow can stay disabled.

## The idea in one minute

Copying `m_clean_north` into `m_clean_nj` and `m_clean_ny` feels fast and becomes a mess. A **parameter** is a blank you fill when the **job** runs, not when the mapping is designed.

In-out and input parameters are the ones you will use:

| Kind | Who fills it | Example |
|---|---|---|
| Input parameter | The mapping task, or a person at run time | Which state code to keep |
| In-out parameter | The engine, later, for MDM job instance id | Do not invent one today |

The mapping says "filter to `$p_state`". The task says "`p_state` = NJ". Tomorrow a second task can say NY without a second mapping.

## Meridian example

The real network has many hospitals. The practice file is all NJ, plus you will **add two NY rows** so the parameter has something to do. You are not changing Ava's story. You are proving the filter.

Append these two rows to a **new** file `patients_multi_state.csv` on the agent. Keep the North file untouched.

```csv
source_system,source_pk,first_name,last_name,dob,gender,phone,address,city,state,zip,mrn
EPIC_NORTH,N1001,Ava,Shah,1984-03-12,F,(201) 555-0101,10 River Rd,Hackensack,NJ,07601,MRN1001
EPIC_NORTH,N1002,Noah,Patel,1979-11-02,M,201-555-0102,22 Oak St,Edison,NJ,08817,MRN1002
EPIC_NORTH,N1003,Mia,Chen,1991-07-19,F,2015550103,5 Bay Ave,Jersey City,NJ,07302,MRN1003
EPIC_NORTH,N9001,Ruth,Adler,1972-05-05,F,2125550170,1 5th Ave,New York,NY,10003,MRN9001
EPIC_NORTH,N9002,Omar,Hassan,1980-08-08,M,2125550171,2 6th Ave,New York,NY,10003,MRN9002
```

No bad date in this file. This session is only about the parameter.

## Do this

**Goal: a filter whose value is not hard-coded.**

1. New mapping `m_by_state` in `MERIDIAN_PRACTICE`.
2. Source: `patients_multi_state.csv`.
3. Open the mapping's **Parameters** panel (often a tab, or the parameters icon, not inside a transformation yet).
4. New input parameter. Name: `p_state`. Type: string. Default: `NJ` if a default is required. Description: `Two-letter state to keep`.
5. Drag **Filter**. Connect Source → Filter → Target.
6. Filter condition: `state = $p_state` (the picker may insert the parameter token; use the token, do not type a quote around NJ inside the mapping).
7. Target file: `patients_by_state.csv`. Pass the source fields through. Validate. Save.

**Goal: two tasks, one mapping.**

8. Mapping task `mt_by_state_nj`. Mapping `m_by_state`. Runtime as usual. Set parameter `p_state` to `NJ`. Run.
9. Open the target. You want Ava, Noah, Mia. You do **not** want Ruth or Omar.
10. Mapping task `mt_by_state_ny`. Same mapping. Set `p_state` to `NY`. Run.
11. Open the target again (this task overwrites the same file). You want Ruth and Omar only.

If overwriting confuses you, set the NY task's target parameter instead — only if you already parameterized the target object. That is optional. The required proof is: NJ run keeps 3, NY run keeps 2, and you did **not** clone the mapping.

## What you should see

- One mapping.
- Two tasks.
- Different row counts.

## If it fails

| What you see | What it usually means |
|---|---|
| Both runs return all 5 rows | The filter still says `state = 'NJ'` as text, or the parameter is not in the condition. |
| Task has no place to type NJ | The parameter was created inside a mapplet or not marked as available to the task. Create it on the mapping. |
| NY run still shows Ava | You looked at the old NJ file and the NY task failed or wrote elsewhere. Check the run's target path in Monitor. |

## Write this in the session log

```text
NJ run id / rows:
NY run id / rows:
Mapping cloned: no
```

## Answer before session 7

1. Why is the state value on the task, not in the filter as the letters NJ?
2. What would you duplicate by mistake if you copied the mapping instead?

## Stop

Next sitting is Data Quality, the reusable version of "this date is wrong": [07-data-quality.md](07-data-quality.md). If **Data Quality** is not in My Services, write `NOT LICENSED`, answer the questions from what you already did in session 3, and go to session 8 another day.
