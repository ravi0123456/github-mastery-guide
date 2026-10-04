# Session 9 — Load patients and find them

**Time:** 60 minutes. **Requires:** session 8 create page works. **Do not** configure match in this sitting.

## The idea in one minute

**Ingress** means data going **into** MDM. The smallest ingress is a **file import**: you upload a CSV, map columns, pick a source system, and run a job. No CDI, no Application Integration, no `jobInstanceId`. Those belong to the automated ingress in session 12.

Until match exists, MDM will treat every source row as its own master. You **want** that today. If you loaded North and South now, you would see two Avas. That is the "before" picture. This sitting loads **North only** (3 good people — do not load `Bad Date`).

Search must find `Ava` after the import job is Success. If search is empty, the job did not commit, or you are searching the wrong application.

## Meridian example

Epic North sends three clean patients. Each gets a cross-reference: source system `EPIC_NORTH`, source primary key `N1001` / `N1002` / `N1003`. The master record is created because nobody has merged anything. One source row, one master. Boring on purpose.

## Do this

**Goal: a three-row file with no reject row.**

1. On your computer, make `patients_north_for_mdm.csv` from the clean people only:

```csv
source_pk,first_name,last_name,birth_date,gender,phone,address,city,state,postal_code,mrn
N1001,Ava,Shah,1984-03-12,F,2015550101,10 River Rd,Hackensack,NJ,07601,MRN1001
N1002,Noah,Patel,1979-11-02,M,2015550102,22 Oak St,Edison,NJ,08817,MRN1002
N1003,Mia,Chen,1991-07-19,F,2015550103,5 Bay Ave,Jersey City,NJ,07302,MRN1003
```

Use `birth_date` and `postal_code` as the headers so they line up with the entity. Phone is already digits.

**Goal: import as EPIC_NORTH.**

2. Business 360 → **New** → **File import** (or Explore → File import jobs). If the only path is a guided "Load data", use that.
3. Business entity: `Patient`.
4. Source system: `EPIC_NORTH`. This choice applies to every row in this file. Do not import the South file in the same job.
5. Upload `patients_north_for_mdm.csv`.
6. Map columns:
   - `source_pk` → source primary key (system field, not `mrn`)
   - `first_name`, `last_name`, `birth_date`, `gender`, `phone`, `address`, `city`, `state`, `postal_code`, `mrn` → the fields you created
7. If a preview shows 3 rows, continue. If it shows 4, you included Bad Date. Stop and fix the file.
8. Run the import. This creates a **job**. Copy the job id.
9. Wait until the job is **Success**. Open the counts. Loaded should be 3. Rejected should be 0. If rejected is not 0, open the reject reason before you import again. Importing again with the same source primary key should **update**, not duplicate — but only if you mapped `source_pk` to the source primary key. If you mapped it to a normal field, a second import creates 3 more masters. Do not run twice until the first result looks right.

**Goal: search proves the job.**

10. Open the Meridian Practice application.
11. Search `Shah`. Open Ava.
12. Confirm birth date `1984-03-12`, mrn `MRN1001`, and the source system is Epic North with key `N1001`. The source block may be called cross-reference, source record, or contributing system.
13. Search `Chen` and `Patel` the same way.

## What you should see

- Import job Success, 3 loaded, 0 rejected.
- Search returns Ava, Noah, Mia.
- Each record shows source `EPIC_NORTH` and the `N*` key.
- You still have **two files not loaded**: South patients, and the bad row. Good.

## If it fails

| What you see | What it usually means |
|---|---|
| No file import | Your release starts ingress only from CDI. Stop and do session 12's "simple connector" path **after** you read it, but do not also invent match rules. Or use Create three times by hand and write `FILE IMPORT NOT IN UI`. Hand entry still teaches search. |
| Job success, search empty | Wrong application, or the index has not caught up. Wait two minutes and search again. Also try the exact last name. |
| 3 rejected | Date format or a required field you did not map. Read the reject file. Fix the map. Re-run. |
| 6 patients after a second try | Source primary key was not mapped. Delete the practice records (only `MRN1001`, `MRN1002`, `MRN1003`, and `MRNTEST`) and import once. |

## Write this in the session log

```text
Import job id:
Loaded / rejected:
Ava source system and source pk:
South file loaded: no
```

## Answer before session 10

1. Why is this import a job even though you did not create a mapping task?
2. What would be wrong about loading `patients_epic_north.csv` with the Bad Date row?

## Stop

Next sitting is the point of the story: two Avas become one master: [10-match-merge-survivorship.md](10-match-merge-survivorship.md).
