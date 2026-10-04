# Session 3 — Clean the good rows, reject the bad row

**Time:** 70 minutes. **Requires:** `mt_land_north` success.

## The idea in one minute

Master data is only useful if the values are comparable. Ava's phone is `(201) 555-0101` in North and `2015550101` in South. A match rule that demands an exact phone will miss her. A date that says `NOT_A_DATE` must not enter MDM and create a person.

You will do both in one mapping:

- **Expression** transformation: calculate a new value from old values (digits-only phone, upper-case name).
- **Router** transformation: send rows down different paths. One path is "good enough to keep". One path is "reject".

A Filter would drop the bad row on the floor. A Router keeps it in an error file so you can show a steward. In a hospital story, dropped rows are how patients vanish. Keep the reject file.

## Meridian example

| Incoming | After this job |
|---|---|
| `(201) 555-0101` | `2015550101` |
| `Ava` | `AVA` (compare key, you still keep the original name) |
| `Female` / `F` | not fixed yet — South file comes in session 4's union mindset; today only North |
| `NOT_A_DATE` | error file, not the clean file |

## Do this

**Goal: a mapping with two targets.**

1. Data Integration → **New** → Mapping. Name: `m_clean_north`. Location: `MERIDIAN_PRACTICE`.
2. Source: `FF_MERIDIAN_PRACTICE` / `patients_epic_north.csv`. Same format as session 2.
3. Delete the default link from Source straight to Target if the canvas already drew one. You will insert transformations between them.

**Goal: an expression that adds compare fields and does not destroy the original.**

4. On the canvas palette, drag **Expression** onto the canvas. Connect Source → Expression.
5. In Expression, add output fields (names exactly):

| New field | Type | Expression (use the function picker if the syntax differs slightly) |
|---|---|---|
| `phone_digits` | string | `REG_REPLACE(phone, '[^0-9]', '')` |
| `first_key` | string | `UPPER(LTRIM(RTRIM(first_name)))` |
| `last_key` | string | `UPPER(LTRIM(RTRIM(last_name)))` |
| `dob_ok` | integer | `IIF(IS_DATE(dob, 'YYYY-MM-DD'), 1, 0)` |

6. If `IS_DATE` or `REG_REPLACE` is named differently in the expression editor, open the function list and pick the date-test function and the regex-replace function. The **intent** is: digits only, trimmed upper name, 1 when the date parses.
7. If `IS_DATE` does not exist, use a pattern test: `IIF(REG_MATCH(dob, '^[0-9]{4}-[0-9]{2}-[0-9]{2}$'), 1, 0)`. `NOT_A_DATE` fails that test. A real date validator is better later; this is enough for this file.
8. Pass through all original fields. Do not delete `phone` or `first_name`.

**Goal: good rows and bad rows leave on different pipes.**

9. Drag **Router**. Connect Expression → Router.
10. Add a group named `GOOD` with condition `dob_ok = 1`.
11. Use the default group as `REJECT` (the rows that fail every condition). If the UI asks you to name the default group, name it `REJECT`.
12. Drag a **second** Target onto the canvas.
13. Connect Router group `GOOD` → Target 1. Name the target object `patients_north_clean.csv`. Map original fields plus `phone_digits`, `first_key`, `last_key`. You do not need `dob_ok` in the file.
14. Connect `REJECT` → Target 2. Object: `patients_north_reject.csv`. Map the same fields plus `dob_ok` so you can see the 0.
15. Validate. Save.

**Goal: run it as its own job.**

16. New mapping task `mt_clean_north`. Runtime = session 1 runtime. No schedule. Run.
17. Monitor: success. Then open the two files on the agent.

## What you should see

`patients_north_clean.csv` has **3** rows: Ava, Noah, Mia.

- Ava's `phone_digits` is `2015550101`.
- `first_key` is `AVA`.
- Original `first_name` is still `Ava`.

`patients_north_reject.csv` has **1** row: Bad Date, `dob_ok` 0.

Total 3 + 1 = 4. You did not lose a row. You sorted it.

## If it fails

| What you see | What it usually means |
|---|---|
| Expression will not validate | Function name differs. Use the function list. Do not guess a second name twice — read the help text in the picker. |
| All 4 rows in clean | `dob_ok` is always 1. The test never fails `NOT_A_DATE`. |
| All 4 rows in reject | Condition is inverted or `dob_ok` is a string `'1'` compared wrong. |
| Router has only one output | The default group is not connected. Connect it. |

## Write this in the session log

```text
mt_clean_north run id:
Clean rows: 3
Reject rows: 1
Ava phone_digits: 2015550101
```

## Answer before session 4

1. Why keep the original `phone` and also store `phone_digits`?
2. Why is a reject file safer than a Filter for patient data?

## Stop

Next sitting joins encounters so one person shows a whole episode of care: [04-lookup-joiner-aggregator.md](04-lookup-joiner-aggregator.md).
