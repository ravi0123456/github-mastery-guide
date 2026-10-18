# Session 7 — Data Quality: a rule you can reuse

**Time:** 60 minutes. **Requires:** sessions 2 and 3. **Skip rule:** if Data Quality and Data Profiling are both missing from My Services, write `NOT LICENSED` and stop. Session 3 already taught the logic.

## Fix the run that just failed

`pf_patients_north` failed in one second. Processed rows **0**. Error: `The DTM process terminated unexpectedly`.

The columns are fine. Rules **(0)** is fine. You have not added a rule yet, and this crash happens before any rule. The profiling engine on the agent never started.

Do this before you run the profile again. Do not create a new profile.

1. Open **Administrator** → **Runtime Environments**.
2. Open the group **CDIPC_APAC_Dev** (the group, not only the agent `APCDCINFDA191`).
3. Click the three dots → **Enable or Disable Services and Connectors**.
4. **Services:** check **Data Quality**. Leave **Data Integration** checked.
5. **Connectors:** check **Amazon S3 v2**. This org is on the AWS pod `usw3`. A flat-file profile still stages its results in that connector. If S3 v2 is off, the DTM process exits immediately and you get this exact error.
6. Save. Wait until the agent services show **Up**. That can take a few minutes. Then run `pf_patients_north` again on the same runtime.

If Data Quality or Amazon S3 v2 is missing from the list, or the box is locked, write `DQ ENGINE NOT ENABLED ON AGENT` in the log and stop. The Data Profiling screen can open even when this agent is not allowed to run a profile.

If both are enabled and the same error returns, uncheck columns until only `dob`, `phone`, and `state` remain, save, and run once more. If that also dies in one second, stop. The agent cannot run profiling. Continue at step 11 and build the date rule only.

## The idea in one minute

An Expression inside one mapping cleans one job. Next year someone writes a new mapping and forgets the phone rule, and Ava fails to match again. **Cloud Data Quality (CDQ)** puts the rule in one place: a dictionary, a cleanse, or a rule specification, then more than one job can call it.

**Profiling** is not cleaning. Profiling answers "what is in this column?" before you invent a rule. On the North file it should show 4 rows, one bad date, and several phone shapes.

The real Meridian program standardized data **before** match. Match on dirty phones is how duplicates survive.

## Meridian example

Profile `patients_epic_north.csv` and you should be able to say, without opening the CSV:

- `dob` is not always a date (`NOT_A_DATE`).
- `phone` has more than one pattern.
- `state` has `NJ` and `ZZ`.

Then a rule: a row is invalid when `dob` does not match `yyyy-MM-dd`. That is the same decision as `dob_ok` in session 3, stored as a rule instead of a private expression.

## Do this

Menus differ by release (Data Quality vs Data Profiling as separate services). Follow the goal.

**Goal: a profile of the raw North file.**

1. Open **Data Profiling** if it is its own service. Otherwise open **Data Quality** and find **Profile** / new profile.
2. New profile. Name: `pf_patients_north`. Location: `MERIDIAN_PRACTICE` if the service shares projects. If Data Quality uses its own folder tree, create `MERIDIAN_PRACTICE` there too.
3. Source connection: `FF_MERIDIAN_PRACTICE`. Object: the North patient file you already used, `patients_north.csv` (the asset you created may be named `pf_patients_north`). Do not create a second profile if that one already exists.
4. Runtime: `CDIPC_APAC_Dev`, the same agent as session 1.
5. Run the profile. A good run takes longer than one second and shows a row count. If it fails in one second with `DTM process terminated unexpectedly`, do the agent steps at the top of this file. Do not add a rule to fix that error.

**Goal: read the profile before you write a rule.**

6. Open the results. Confirm row count 4.
7. Open `dob`. Note the bad value.
8. Open `phone`. Note more than one pattern.
9. Open `state`. Note `ZZ`.
10. Write those three observations in the log in your own words. If you cannot, you did not read the profile.

**Goal: one reusable rule, then stop.**

11. In Data Quality, create a rule specification (or a cleanse asset, if that is what your release offers for a pattern check). Name: `rl_dob_is_iso`.
12. Input: a string. Logic: valid when the value matches a date pattern `YYYY-MM-DD`. Invalid otherwise.
13. If the UI wants a dictionary instead of a pattern: skip the dictionary. A date pattern is the right tool. A dictionary is the right tool for gender (`F`, `M`, `Female`, `Male`) — that is session extra, not required today.
14. Save. If there is a **test** box, test `1984-03-12` (valid) and `NOT_A_DATE` (invalid).
15. If you can attach the rule to a mapping as a Data Quality transformation, do that on a **copy** only if it takes under 15 minutes: source North file, DQ transformation, good output vs exception output. If the attachment is confusing, stop after the rule test. The profile plus a tested rule is a complete sitting.

Do not rebuild session 3 inside this session.

## What you should see

- A finished profile with 4 rows.
- You wrote three real observations.
- A saved rule that accepts a real ISO date and rejects `NOT_A_DATE`.

## If it fails

| What you see | What it usually means |
|---|---|
| No Data Quality service | License. Write `NOT LICENSED`. Do not invent a workaround project in another org. |
| Profile fails in about one second, 0 rows, `DTM process terminated unexpectedly` | The agent group is missing **Data Quality** or **Amazon S3 v2**. Rules being empty is not the cause. Follow the steps at the top. |
| Profile runs forever | Wrong runtime, or the agent cannot read the file. Same checks as session 2. |
| Profile says 5 rows | It counted the header. Fix the formatting (header present) and run again. |
| Rule test has no sample box | Open the asset and use **Run** / **Test** with a single value. If there is truly no test, record the rule name and move on. |

## Write this in the session log

```text
Profile run:
dob observation:
phone observation:
state observation:
Rule name: rl_dob_is_iso
Test NOT_A_DATE: invalid / not tested
```

## Answer before session 8

1. What does a profile tell you that a mapping does not?
2. Why is a shared rule safer than an expression copied into ten mappings?

## Stop

Next sitting starts MDM. You will not match anyone yet. You only build the model: [08-mdm-model.md](08-mdm-model.md).
