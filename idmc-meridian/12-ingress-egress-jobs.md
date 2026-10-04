# Session 12 — Ingress and egress jobs

**Time:** 90 minutes. **Requires:** session 10 complete. **Skip rule:** if you cannot create a Business 360 connection, do the "file-only egress" check at the bottom and stop. Do not spend the day fighting licenses.

## The idea in one minute

Session 9 was a **manual** ingress (file import clicked by you). A production hospital cannot do that every night. The night path is:

```text
Source files or Epic extract
  → CDI mapping task (the transform)
  → Business 360 connection (writes source rows into the MDM store)
  → MDM ingress / match / merge job
  → CDI mapping task reads masters back out (egress)
  → a file other systems can load
```

Two connectors exist. Do not mix them up.

| Connector | Use it when | Watch-out |
|---|---|---|
| **Business 360 Connector** | You can read or write a whole asset in **one** mapping flow | Ingress of dynamic field values is limited. Egress can include field groups. |
| **Business 360 FEP Connector** (flat end point) | Ingress and egress jobs that split root fields and child groups into **more than one** mapping, then a taskflow | Your Patient is flat, so you can keep this to one mapping. The mapping task's in-out parameter **`jobInstanceId`** must exist and you must not edit it. A Cloud Application Integration process passes that id when a real ingress job starts. |

Also from the connector guides:

- Do **not** choose a Hosted Agent or a serverless runtime for a Business 360 connection. Use the Secure Agent from session 1.
- When an ingress file contains two rows with the **same** source primary key, the job keeps one of them at random. Your practice keys are unique. Keep them unique.
- FEP taskflows that load a child field group need a second mapping. You have no child group. One mapping is correct.

You already matched Ava. This sitting does **not** load her a third time. You will egress the master, and you will ingress **one new** fake patient so you can see a CDI-driven load.

## Meridian example

Egress file is what other EMRs would receive so every hospital shows the same Ava. One row, the surviving phone, and both source keys if the connector returns them.

Ingress of `N1005` / `Imani Wells` is a new person, not a duplicate of Ava. She proves the CDI path without disturbing the match demo.

## Do this

**Goal: a Business 360 connection on the Secure Agent.**

1. Administrator → **New Connection**.
2. Type: **Business 360** if it is listed. If only **Business 360 FEP** is listed, use FEP and follow the FEP notes below. If neither is listed, jump to the file-only section.
3. Name: `B360_MERIDIAN_PRACTICE`.
4. Runtime: the session 1 **Secure Agent**. Not hosted. Not serverless.
5. Fill the org URL and the user the platform uses for MDM API calls. Use a DEV user. Do **not** paste the password into a mapping, a file, or git. The password stays in the connection.
6. Test. Save. If test fails on URL or role, stop. Write the error in the log. Do not try Production credentials.

**Goal: egress. Masters out to a file.**

7. Data Integration → new mapping `m_egress_patient` in `MERIDIAN_PRACTICE`.
8. Source connection: `B360_MERIDIAN_PRACTICE`. Source object: business entity `Patient` (wording: asset / business entity).
9. Target: `FF_MERIDIAN_PRACTICE` file `patients_golden.csv`.
10. Map `first_name`, `last_name`, `birth_date`, `phone`, `mrn`. Map source-system fields too if they appear as repeatable cross-reference fields. If the source shows many cross-reference rows per master, you will get more than one output row per person. That is honest. Do not "fix" it with a lookup.
11. If you used **FEP**: open the mapping's input-output parameters and confirm `jobInstanceId` exists if the connector added it. If you are running this mapping **by hand** as a one-off export and not from an MDM egress job, use the connector path that allows a direct read. If the task demands `jobInstanceId` and will not run without the Application Integration process, do not type a random id. Stop this mapping and use file-only egress below.
12. Mapping task `mt_egress_patient`. Runtime = Secure Agent. Run.
13. Open `patients_golden.csv`. Find one Ava. Phone for Noah, if present, is `2015550102`.

**Goal: ingress one new person through CDI. Do not reload Ava.**

14. Add `patients_one_new.csv` on the agent:

```csv
source_pk,first_name,last_name,birth_date,gender,phone,address,city,state,postal_code,mrn
N1005,Imani,Wells,1995-04-04,F,2015550160,9 Elm St,Newark,NJ,07102,MRN1005
```

15. New mapping `m_ingress_one`. Source: that file on `FF_MERIDIAN_PRACTICE`. Target connection: `B360_MERIDIAN_PRACTICE`. Target object: `Patient`. Operation: insert / upsert as the connector's create-or-update. Map `source_pk` to the source primary key. Set the source system to `EPIC_NORTH` in the target fields or the connection's object properties, wherever the connector asks. It must not be `EPIC_SOUTH`.
16. FEP only: keep `jobInstanceId` if the UI created it, and do not rename it. If a hand-run is refused, this ingress must be started as an MDM ingress job that calls your taskflow. Create that job in Business 360 only if the wizard lets you pick an existing mapping task. If the wizard demands a new Cloud Application Integration process and you have not done session 13, **stop**. Imani can wait. Your match demo is already the core of the story.
17. If the task runs: mapping task `mt_ingress_one`, run it, then search `Wells`. One new master. She must not merge with Ava.

**Goal: leave schedules off.** No schedule on egress or ingress.

## File-only check (when the connector is missing)

You already ingressed by file import and you already have masters. Export from the Business 360 UI if it has **Export** on the search results. Download the file. Confirm Ava appears once. Write `B360 CONNECTOR NOT LICENSED` in the log. That is a valid finish for this session.

## What you should see

Either:

- `patients_golden.csv` contains one Ava and Noah's North phone, or
- a UI export that shows the same, plus `B360 CONNECTOR NOT LICENSED`.

Imani is optional proof of CDI ingress. Ava's match from session 10 is **not** optional and must still be true after this sitting.

## If it fails

| What you see | What it usually means |
|---|---|
| Connection test says hosted agent | You picked the wrong runtime. Switch to the Secure Agent. |
| Egress task asks for jobInstanceId and fails | This connector path is meant to be called by the MDM job, not by Run. Use UI export. |
| Imani merged to Ava | Match rule is too loose, or you reused Ava's last name and birth date. Check the CSV. |
| A third Ava appeared | You ingressed North or South again with a new source primary key. Stop. Do not "clean up" by merging random records. Note the new key in the log and ask before delete if you are unsure which record is practice data. |
| Password in the mapping | Remove it. Put it only in the connection. If you already saved it in a mapping, edit it out and do not export that mapping to git. |

## Write this in the session log

```text
Connector used: Business 360 / FEP / none
Egress run id or UI export: 
Ava rows in golden file:
Noah phone in golden file:
Imani loaded: yes/no/skipped
```

## Answer before session 13

1. What is the difference between a file import and an ingress mapping task?
2. Why must `jobInstanceId` stay untouched when you see it?

## Stop

Next sitting is **one** other service, not all of them: [13-other-idmc-services.md](13-other-idmc-services.md).
