# Session 6 — Parameters: one mapping, two jobs

**Time:** 45 minutes. **Requires:** session 3 filter skills. Leave the taskflow schedule disabled.

## Fix the run that just failed

The mapping header says **Invalid**. Validation says: expression in field `kept_state` is not valid, `$$P_STATE` invalid symbol reference.

`$$` is only for an **in-out** parameter. `p_state` is an **input** parameter. Typing `$$p_state` makes the designer look for an in-out parameter named `P_STATE`. That parameter does not exist, so the expression is red. Do not type dollar signs in front of `p_state`.

1. Open mapping `m_by_state`. Click the Expression.
2. On the `kept_state` row, delete `$$p_state`. Set the expression to the incoming field `state`. Type stays **string**. The filter already drops the other state, so this column will show `NJ` or `NY` on the rows that remain. You can delete the `kept_state` row instead if you do not want the extra column.
3. Leave `count_step` as type **Integer** and expression `SetCountVariable($$p_kept_count)`. Two dollar signs belong here, because `p_kept_count` is the in-out parameter. Expand **In-Out Parameters** and confirm `p_kept_count` is still listed as integer. If it is missing, add it again: integer, default `0`, aggregation **Count**, retention **On success**.
4. Open the Filter. The condition must use the parameter picker, not typed text. Field `state`, operator `=`, then choose **p_state** from the parameter list in that value box. If the box shows `$$p_state`, `$p_state`, or `'NJ'`, clear it and pick `p_state`.
5. Validation must say no errors. The header must say **Valid**. Save. Run the existing task `mt_by_state_nj`. Do not create a new task. On the task, `p_state` stays `NJ`.

A good run writes Ava, Noah, and Mia. The `state` column is `NJ`. The in-out value is **3**. Then continue at step 21 for the NY task.

Ignore these log lines. They are warnings, not the failure: Error Log DB Connection, Thousand Separator, Decimal Separator, Datetime Format, Output is repeatable, and "The value entered is not a valid integer."

## The idea in one minute

A mapping is a recipe. A parameter is a blank in that recipe. You fill the blank when the **job** runs, not by copying the recipe.

You will make **one** mapping and **two** tasks. One task keeps New Jersey. The other keeps New York. You will also make a counter that the mapping itself updates, so you can feel the difference.

## Two kinds, in plain English

Think of a hospital form.

| | Input parameter | In-out parameter |
|---|---|---|
| Plain meaning | A blank the job fills in. The mapping only **reads** it. | A blank the mapping can **change**, and the next run can see the new value. |
| Who writes it | You, on the mapping task | The mapping, while it runs |
| Does it remember what the mapping wrote? | No | Yes, if the run succeeds |
| This session | `p_state` = `NJ` or `NY` | `p_kept_count` counts the rows that passed the filter |
| What you should remember | The **task** says which state. | The **mapping** says how many rows it kept, and that number sticks. |

Example. The filter says "keep the state I was given." It does not say "keep NJ." The NJ task writes `NJ` in `p_state`. The NY task writes `NY` in the same blank. If you had typed `NJ` inside the filter, the NY task would still return Ava.

`p_kept_count` starts at 0. Each kept row adds 1. After a successful NJ run the job details show **3**. Run that same NJ task again and the details show **6**, because it remembered 3 and added 3 more. The output file still has only 3 people. The counter grew. The state blank did not.

There is no button named **Set parameter**. If the mapping has no parameter, the task has nowhere to type one.

## Meridian example

The North file stays untouched. Make a **new** file on the agent, in the same folder as `patients_north.csv`. Name it `patients_multi_state.csv`.

```csv
source_system,source_pk,first_name,last_name,dob,gender,phone,address,city,state,zip,mrn
EPIC_NORTH,N1001,Ava,Shah,1984-03-12,F,(201) 555-0101,10 River Rd,Hackensack,NJ,07601,MRN1001
EPIC_NORTH,N1002,Noah,Patel,1979-11-02,M,201-555-0102,22 Oak St,Edison,NJ,08817,MRN1002
EPIC_NORTH,N1003,Mia,Chen,1991-07-19,F,2015550103,5 Bay Ave,Jersey City,NJ,07302,MRN1003
EPIC_NORTH,N9001,Ruth,Adler,1972-05-05,F,2125550170,1 5th Ave,New York,NY,10003,MRN9001
EPIC_NORTH,N9002,Omar,Hassan,1980-08-08,M,2125550171,2 6th Ave,New York,NY,10003,MRN9002
```

Five data rows. Three NJ, two NY. No bad date. This session is only the blanks.

## Do this

**Goal: the blanks live on the mapping.**

1. Data Integration → **New** → **Mappings** → **Mapping**. Name `m_by_state`. Location `MERIDIAN_PRACTICE`. Do not click **Switch to Advanced**. In advanced mode a text in-out parameter will not update. This mapping stays in the normal designer.
2. **Source.** Connection: the existing `FF_MERIDIAN_PRACTICE`. Do not create a connection. Object: `patients_multi_state.csv`. Not `patients_north.csv`. If the file is missing from the list, it is not in the agent folder yet. Copy it there and refresh the object list.
3. Open the **Parameters** panel from the mapping toolbar (the canvas toolbar, not a transformation, and not the mapping task). If you do not see it, click empty canvas and check the icons above the canvas for **Parameters**.
4. Add an **input** parameter.
   - Name: `p_state`
   - Type: string. Precision 10.
   - Default: `NJ` if the dialog requires one.
   - Description: `State to keep`.
5. Add an **in-out** parameter. Same panel, the in-out section, not a second input. The dialog often opens on **String** because `p_state` was a string. Change it. Count only accepts an integer.
   - Name: `p_kept_count`
   - Data type: **Integer**. Not String, not Text, not Decimal, not Number.
   - Precision: 10. Scale: 0, or leave scale empty.
   - Default value: `0` (the digit, not a word).
   - Retention policy: **On success**
   - Aggregation type: **Count**
   - Description: `Rows that passed the filter`.
6. **Save the mapping now**, before you look at a task. A task opened earlier will not grow a parameter section until this save exists.

**Goal: the filter reads the input. The expression updates the in-out.**

7. Drag **Filter**. Connect Source → Filter.
8. Filter condition: field `state`, operator `=`, then pick parameter `p_state` from the value list. Do not type `$$p_state`, `$p_state`, or `'NJ'`. `$$` is only for the in-out parameter. If both runs later return all 5 rows, this condition is still the letters NJ.
9. Drag **Expression**. Connect Filter → Expression. Add one output field for the state you were given. Do not put the count function on this field.
   - Name: `kept_state`
   - Type: **string**. Precision 10.
   - Expression: `state`
   Pick the incoming field `state`. Do not type `$$p_state`. The designer turns that into `$$P_STATE` and reports `invalid symbol reference`, because `p_state` is an input parameter, not an in-out parameter. The filter already keeps one state, so this column shows `NJ` or `NY`.
10. On that same Expression, click **+** and add a **second** row. This is the field that updates the counter. The canvas can say Valid while this row is still wrong. The agent rejects it later.
    - Name: `count_step`
    - Type: **Integer**. Not string. Precision 10. Scale 0.
    - Expression: `SetCountVariable($$p_kept_count)`
    Two dollar signs, no quotes. `$$` belongs only on `p_kept_count`. `kept_state` stays the incoming field `state`. Do not put `SetCountVariable` on `kept_state`. A string field cannot hold a count.
11. Drag **Target**. Connect Expression → Target. Connection: existing `FF_MERIDIAN_PRACTICE` again. Object: new file `patients_by_state.csv` (create it at runtime, same way as earlier targets). Format: flat file, comma, header row. Automap. `kept_state` should be mapped. Do not point this target at `patients_north_clean.csv` or `episode_north_summary.csv`.
12. Validate. **Save.** Header must be Valid.

**Goal: two tasks, one mapping. This is where you type the value.**

13. **New** → **Mapping task** (Data Integration, not a taskflow). Name `mt_by_state_nj`. Location `MERIDIAN_PRACTICE`. Runtime: the same Secure Agent as session 1. Mapping: `m_by_state`. No schedule.
14. **Sources.** Connection `FF_MERIDIAN_PRACTICE`. Object `patients_multi_state.csv`. You choose the existing connection and the existing file. A parameter does not replace the connection.
15. **Targets.** Same connection. Object `patients_by_state.csv`.
16. **Input Parameters.** This section is the "set parameter" place. It appears only after step 6. Type `NJ` for `p_state`. No quotes.
17. **In-Out Parameters.** You should see `p_kept_count` with current value `0`. Do not type `3`. The mapping writes this one. Leave it.
18. Save the task. Run it. Do not schedule it.
19. Open `patients_by_state.csv`. You want Ava, Noah, Mia, and `kept_state` = `NJ`. You do not want Ruth or Omar.
20. My Jobs → open that run → **In-Out Parameters**. `p_kept_count` should be **3**.
21. New mapping task `mt_by_state_ny`. Same mapping, same connection, same source file, same target file. Input parameter `p_state` = `NY`. In-out starts at `0` because this is a different task. Save. Run.
22. Open the target again. This task overwrites the file. You want Ruth and Omar only, `kept_state` = `NY`. Job details for **this** run: `p_kept_count` is **2**.
23. Run `mt_by_state_nj` a second time. The file goes back to Ava, Noah, Mia (still 3 rows). Job details for this second NJ run: `p_kept_count` is **6**. It remembered 3 and counted 3 more. `p_state` is still just `NJ`. It did not become `NJNJ`. That is the whole difference.

## What you should see

- One mapping, `m_by_state`. You did not clone it.
- NJ task: 3 rows, `kept_state` NJ, in-out 3, then 6 on the second run.
- NY task: 2 rows, `kept_state` NY, in-out 2.
- Source and target both use `FF_MERIDIAN_PRACTICE`.

## If it fails

| What you see | What it usually means |
|---|---|
| The mapping task has no Input Parameters section | The parameter is not on the mapping, or the mapping was not saved, or you opened the task before the save. Save `m_by_state`, then open the task again. There is still no button named Set parameter. |
| Both runs return all 5 rows | The filter says `state = 'NJ'` as text. Pick parameter `p_state` in the filter value box. |
| Header **Invalid**. Expression `kept_state` says `$$P_STATE` invalid symbol reference | You typed `$$p_state` on an input parameter. `$$` is only for in-out `p_kept_count`. Change `kept_state` to the incoming field `state`. |
| NY run still shows Ava | You opened an old file, or the NY task wrote somewhere else. Check the target path on that run in Monitor. |
| In-out stays 0 | The Expression does not call `SetCountVariable($$p_kept_count)`, the aggregation is not Count, or you switched the mapping to Advanced. |
| Job **Failed** before any rows. Log says `Invalid datatype for aggregate COUNT for mapping variable $$p_kept_count`, then `Failed to Import: Mapping0`. The page says `Invalid mapping import`. | `p_kept_count` or the Expression field `count_step` is String (or Decimal). Count needs Integer on both. Fix it with the steps below. Do not create a new task. |
| Log also says Error Log DB Connection, Thousand Separator, Decimal Separator, Datetime Format, or Output is repeatable | Ignore those. They are warnings. The job failed on the COUNT line above them. |
| Second NJ run still shows 3, not 6 | Retention policy is not **On success**, or you clicked reset on the in-out parameter before the run. |
| You created a new connection | You do not need one. Pick `FF_MERIDIAN_PRACTICE`. |

## Fix the failed NJ run

`mt_by_state_nj` failed at import. No rows were written. The only line that matters is `Invalid datatype for aggregate COUNT for mapping variable $$p_kept_count`. Count cannot run on a string.

1. Open mapping `m_by_state`. Do not open the task yet.
2. Parameters panel → in-out `p_kept_count`. Set data type to **Integer**, scale 0, default `0`, aggregation **Count**, retention **On success**. If the type dropdown will not change, delete this parameter and create it again with those settings. Save.
3. Open the Expression. You need **two** rows.
   - `kept_state`: type **string**, expression is the incoming field `state`. If this row says `$$p_state`, `$p_state`, or `SetCountVariable`, replace it with `state`.
   - `count_step`: click **+** if it is missing. Type **Integer**, precision 10, scale 0, expression `SetCountVariable($$p_kept_count)`.
   - Filter: field `state`, operator `=`, pick parameter `p_state` from the value list. Not `$$p_state` and not `'NJ'`.
   The header can say Valid before this change. Valid does not check the count type. The agent does, when the task runs.
4. Validate. Save the mapping. The header must say Valid.
5. Open the existing task `mt_by_state_nj`. Do not create another one. Confirm input `p_state` is `NJ`. Run it again.

A successful rerun shows 3 rows (Ava, Noah, Mia) and in-out value 3. Then continue at step 21 for the NY task.

## Write this in the session log

```text
NJ run id / rows / in-out:
NJ second run in-out (expect 6):
NY run id / rows / in-out:
Mapping cloned: no
```

## Answer before session 7

1. Why does the NY task return Ruth without a second mapping?
2. Why did `p_kept_count` become 6 while the file still had 3 rows?
3. What would you duplicate by mistake if you copied the mapping instead?

## Stop

Next sitting is Data Quality, the reusable version of "this date is wrong": [07-data-quality.md](07-data-quality.md). If **Data Quality** is not in My Services, write `NOT LICENSED`, answer the questions from what you already did in session 3, and go to session 8 another day.
