# Session 2 — Your first job: land the North file

**Time:** 60 minutes. **Requires:** session 1 test successful.

## The idea in one minute

In the real program, data does not jump from Epic into MDM in one click. Something **lands** it first: a job copies source rows into a place the next job can trust. You will land `patients_epic_north.csv` into a new file, unchanged, so you can see the smallest job that still counts.

Three objects, and only one of them runs:

| Object | What it is | Does it run? |
|---|---|---|
| Mapping `m_land_north` | The drawing: this file in, that file out | No |
| Mapping task `mt_land_north` | The job: which runtime, which connection | Yes |
| (Later) taskflow | Several jobs in an order | Yes |

People say "I created the mapping" and then wait. Nothing happens until the **task** runs. Monitor is where you see the run.

## Meridian example

Landing Epic North is the same pattern as landing Clarity extracts into a raw zone before match. Today the raw zone is `patients_north_landed.csv`.

## Do this

**Goal: a project that will hold every later asset.**

1. **My Services** → **Data Integration**.
2. **Explore**.
3. **New Project**. Name: `MERIDIAN_PRACTICE`. Description: `Fake Meridian practice. Do not deploy.` Save.

**Goal: a mapping that copies one file.**

4. **New** → **Mappings** → **Mapping** → Create.
5. Name: `m_land_north`. Location: `MERIDIAN_PRACTICE`. Save.
6. Click the **Source** transformation.
7. Connection: `FF_MERIDIAN_PRACTICE`.
8. Source type: single object / file. Object: `patients_epic_north.csv`.
9. Formatting if asked: delimiter comma, header present, date `yyyy-MM-dd`.
10. Open the **Fields** or preview. You want **4** data rows in preview if preview is available. Columns must include `source_pk` and `dob`.
11. Click the **Target**.
12. Connection: same flat file connection.
13. Target object: create `patients_north_landed.csv` (new target / create target — wording varies). Use the same columns as the source. Do not add columns yet.
14. Operation: insert / overwrite as a file replace if the dialog offers "truncate" or overwrite. For a file, replacing the file is correct.
15. Field mapping: automatic map. Every source field lines up. No unmapped field.
16. **Validate**. Fix red errors. Save. The mapping is still not a job.

**Goal: a mapping task, which is the job.**

17. From the mapping menu (often the three dots, or **New Mapping Task**), create a mapping task. If you do not see it: **New** → **Tasks** → **Mapping task**.
18. Name: `mt_land_north`.
19. Location: `MERIDIAN_PRACTICE`.
20. Mapping: `m_land_north`.
21. Runtime environment: the **same** runtime as the connection. A mismatch is the usual first failure.
22. Do not set a schedule.
23. Save, then **Run**.

**Goal: prove it in Monitor, not by hope.**

24. **My Services** → **Monitor** (or the Monitor icon). Open **My Jobs** / All Jobs.
25. Find `mt_land_north`. Wait until state is **Success**. Open it.
26. Success rows should be **4**. Failed rows **0**.
27. On the agent folder, open `patients_north_landed.csv`. Same 4 people, including `Bad Date`. You did not clean anything yet. That is correct.

## What you should see

- Project `MERIDIAN_PRACTICE`.
- Mapping valid. Task success. 4 rows in the target file.
- A run id in Monitor. Copy it into the log.

## If it fails

| What you see | What it usually means |
|---|---|
| Source object not listed | Folder path on the connection is wrong, or the file name does not match. |
| 0 rows | Header treated as data, or the file is empty on the agent. |
| Runtime error on file create | Agent user cannot write the directory. |
| Task fails, mapping was valid | Runtime on the task is not the runtime on the connection. |
| You cannot find the run | You ran nothing. A saved mapping has no run. |

## Write this in the session log

```text
Mapping: m_land_north valid: yes
Task: mt_land_north
Run id:
Rows success: 4
Target file seen on agent: yes/no
```

## Answer before session 3

1. What did you create that does **not** run?
2. What did you create that **does** run?
3. Why is `Bad Date` still in the landed file?

## Stop

Next sitting removes that bad row and fixes the phone: [03-cleanse-and-bad-rows.md](03-cleanse-and-bad-rows.md).
