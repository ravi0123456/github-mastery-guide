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

The job succeeded. My Jobs shows **Fetching the source rows: 4** and **Success**. The warning `Profile run successful but no data in the selected source` is because **View** is set to **Rules**, and this profile has **0 rules**. Rules do not hold the column statistics. Data Preview at the bottom is a sample of the file. It is not the profile result.

6. Open `pf_patients_north`. Stay on **Results**.
7. Change **View** from **Rules** to **Columns**. Leave **with** as **All Statistics**. The warning goes away. You should see one row per column: value distribution, % null, # distinct.
8. The banner should show **4** rows, not 0. If it still says **0 Rows** and the time is the old 12:43 AM run, click **Run** once more, wait until My Jobs is Success, then reopen Results and set View to **Columns**. **Fetching the source rows = 4** is the patient count. **Generating data profile = 0** only means there is no rule output yet. **Loading data from staging = 201** is the profiling warehouse, not 201 patients. Ignore 201.
9. Scroll the results grid to `dob`. Open it. You want the value `NOT_A_DATE` and the real dates. That is the bad row you already see in Data Preview: `N1004`, first name `Bad`, last name `Date`.
10. Open `phone`. Note more than one pattern.
11. Open `state`. Note `ZZ` as well as `NJ`.
12. Write those three observations in the log in your own words.

The profile is not an input to the next asset. Nothing is wired automatically. You read `NOT_A_DATE`, then you build a rule that rejects that shape. A later job can call that rule. The profile itself stays a report.

**Goal: one reusable rule, then stop.**

You already did the right start. `rl_dob_is_iso` exists. Input `dob` is String, length 50. Leave that. Do not add a second input. On Input Management, click **OK**.

**Convert to Advanced** failed because the rule is not valid. The message `Can't convert the rule specification` means the blank row is still there. Validate says `a rule statement with no action` and `a rule statement with no operator`. Informatica will not open Advanced until this screen is valid and saved. A blank row cannot be saved.

13. Click **OK** on that message.
14. Stay on **Configuration** → **Rule Logic**. Fill the blank first row. This row is only a key to open Advanced. It is not the date rule.
    - Input: `dob`
    - Operator: **is**
    - Condition: click **+ Add Input**, choose **Value**, type `1984-03-12`. Do not choose **func**.
    - Action: change **func** to **status**, then choose **Valid**.
15. On the second row, `No rule statement is valid`, set the action to status **Invalid**. The action box must not stay empty.
16. Click **Validate**. Both red messages must be gone. Then click **Save**.
17. Click **Convert to Advanced**. That opens a new Advanced rule. The badge **Advanced** next to the name means this part worked.
18. Do not click **Save** yet. The name `rl_dob_is_iso` is already used by the basic rule, so the red message says `An object with this name already exists at this location`. Change **Name** to `rl_dob_is_iso_new`. Leave Location as it is. Leave Dimension as **None**.
19. Open the **Configuration** tab. Confirm the input `dob` is still there and is a String. Do not add a second input.
20. The editor already contains a generated rule:

```
IF dob = dob THEN PrimaryRuleSet = 'VALID'
ELSE PrimaryRuleSet = 'INVALID' ENDIF
```

`dob = dob` is true for every value, so that rule would mark `NOT_A_DATE` as valid. Do not save it. The earlier expression `THEN 'Valid'` is also wrong for this screen. Advanced mode does not return a word. It assigns the rule set, and the status words are `VALID` and `INVALID`.

Select all three lines and replace them with only this:

```
IF IS_DATE(dob, 'YYYY-MM-DD') = 1 THEN PrimaryRuleSet = 'VALID'
ELSE PrimaryRuleSet = 'INVALID' ENDIF
```

`MM` is the month. Do not type `TO_DATE`. Do not type `Valid` or `Invalid`.
21. Click **Validate**. If it says `= 1` is not allowed, delete ` = 1` and validate again. The condition is then `IF IS_DATE(dob, 'YYYY-MM-DD') THEN`. Then click **Save**. **Run Test** stays gray until both succeed.
22. The expression on `rl_dob_is_iso_new` is already correct:

```
IF IS_DATE(dob, 'YYYY-MM-DD') = 1 THEN PrimaryRuleSet = 'VALID'
ELSE PrimaryRuleSet = 'INVALID' ENDIF
```

There must be no period after `ENDIF`. A blue dot beside the name means it is not saved. Click **Validate**. If it rejects `= 1`, delete ` = 1` and validate again. Then click **Save**. The blue dot must disappear. Do not click `rl_dob_is_iso` in the left list.
23. Stay on `rl_dob_is_iso_new`. The title must still say `rl_dob_is_iso_new` and the badge must still say **Advanced**. Open **Test** on that asset. Runtime: `CDIPC_APAC_Dev`.
    - `1984-03-12` must be **VALID**.
    - `NOT_A_DATE` must be **INVALID**.
24. If both rows are **VALID**, look at the title before you change the expression. A title of `rl_dob_is_iso` with **Convert to Advanced** still on the toolbar is the old rule. Its logic is `if dob is dob`, so every value is valid. Close it. Click `rl_dob_is_iso_new` in the left list and repeat step 23. Do not click **Convert to Advanced** again.

Do not rebuild session 3 inside this session. Do not add a rule just to clear the empty Rules view. Change the view to Columns first.

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
| Results say `no data in the selected source`, banner says **0 Rows**, but Data Preview shows 4 rows | **View** is **Rules** and you have 0 rules. Change View to **Columns**. The preview is the file. The Rules view is empty until you add a rule. |
| Profile says 5 rows | It counted the header. Fix the formatting (header present) and run again. |
| Test on `rl_dob_is_iso` shows **VALID** for both `1984-03-12` and `NOT_A_DATE` | The basic rule says `if dob is dob`. A value always equals itself, so the else row never runs. Do not edit it and do not click **Convert to Advanced** again. Open `rl_dob_is_iso_new` and use `IS_DATE`. |
| `An object with this name already exists at this location` after **Convert to Advanced** | The basic rule still owns the name `rl_dob_is_iso`. Rename this new rule to `rl_dob_is_iso_new`, then open **Configuration**. |
| Second row says **expre** and the box is empty | That else action must be status **Invalid**. You will not need this row after you switch to Advanced, because the expression already has `ELSE 'Invalid'`. |
| Rule test has no sample box | Open **Test** on `rl_dob_is_iso` after Validate and Save. Type one value in `dob`. |

## Write this in the session log

```text
Profile run:
dob observation:
phone observation:
state observation:
Rule name: rl_dob_is_iso_new
Test NOT_A_DATE: invalid / not tested
```

## Answer before session 8

1. What does a profile tell you that a mapping does not?
2. Why is a shared rule safer than an expression copied into ten mappings?

## Stop

Next sitting starts MDM. You will not match anyone yet. You only build the model: [08-mdm-model.md](08-mdm-model.md).
