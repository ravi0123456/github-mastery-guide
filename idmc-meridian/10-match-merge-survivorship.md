# Session 10 — Match, merge, survivorship

**Time:** 80 minutes. **Requires:** session 9, North patients searchable, South file **not** loaded yet.

## The idea in one minute

**Match** answers "are these the same real-world person?" **Merge** collapses the matched records into **one master** and keeps every source row underneath as a cross-reference. **Survivorship** answers "when the two source rows disagree, which value do we keep?"

The real program went from 6.5 million rows to 3.2 million masters. Your version:

| Person | Before South load + match | After |
|---|---|---|
| Ava | 1 North master | Still **1** master, **2** source rows (North `N1001`, South `S2001`) |
| Noah | 1 North master | **1** master, **2** source rows, and the phone must be the North phone |
| Leo | not loaded | **1** new master from South only |

**Exact match** demands the same characters. **Fuzzy match** allows `Ava` vs `Ava S`, `Rd` vs `Road`. Ava will not exact-match on first name. She should fuzzy-match on last name + birth date, with first name as a fuzzy name, not as an exact token.

Do not match on phone. The whole lesson is that phones differ. Do not match on source primary key across systems. Those keys are supposed to differ.

**Survivorship for this practice:** rank `EPIC_NORTH` above `EPIC_SOUTH` for `phone`. Noah's master phone must remain `2015550102`, not `9735550199`. When both rows come from the same source system, **recency** (newer last-update) wins. You only have one row per source, so rank is the rule you are proving.

MDM id survivorship is separate: the system's own business id of the surviving master is chosen by the product (often the older consolidated id). You do not configure that today. You only check that search on either source still opens the same master.

## Meridian example

Ava North: `Ava`, `Shah`, `1984-03-12`, phone `2015550101`.  
Ava South: `Ava S`, `Shah`, `1984-03-12`, phone `2015550101`.  
Same person. Fuzzy name, exact birth date, exact last name.

Noah North phone `2015550102`. Noah South phone `9735550199`. Same person. North wins the phone because you ranked the source, not because the numbers are close.

## Do this

**Goal: a match model before the South file arrives.**

1. Business 360 → Match / **Match models** / declarative rules (the section name varies). New model for entity `Patient`. Name: `mm_patient_person`.
2. Match population: the person-name population if the list offers one (often a person or `Person_Name` style population). If the list is empty, use the default person population. Do not pick an organization population.
3. Add a declarative rule `rl_ava_style`:
   - `last_name`: exact
   - `birth_date`: exact
   - `first_name`: fuzzy (name), not exact
4. Do not add `phone`, `mrn`, or address as a **must match** field. Address can be a fuzzy supporting field only if the UI requires a minimum number of fields and you understand it will not block. If unsure, leave address out.
5. Save and **publish** the match model if publish is a separate button. An unpublished model does nothing on import.

**Goal: rank North above South for phone.**

6. Open **Survivorship** for `Patient` (sometimes inside the entity, sometimes its own tab).
7. Field: `phone`. Rule type: **source ranking**.
8. Rank 1: `EPIC_NORTH`. Rank 2: `EPIC_SOUTH`.
9. If the UI also asks for a tie-break, choose last update time.
10. Leave other fields on the default (often recency or rank you can set the same way). Set `first_name` ranking the same way if you want the master to display `Ava` not `Ava S`. That makes the demo easier to see.
11. Save. Apply the rank to the field. A rank that is not applied does nothing.

**Goal: load South, then run match and merge if import did not do it.**

12. File `patients_south_for_mdm.csv`:

```csv
source_pk,first_name,last_name,birth_date,gender,phone,address,city,state,postal_code,mrn
S2001,Ava S,Shah,1984-03-12,F,2015550101,10 River Road,Hackensack,NJ,07601,MRN1001
S2002,Noah,Patel,1979-11-02,M,9735550199,22 Oak Street,Edison,NJ,08817,MRN1002
S2003,Leo,Nguyen,1988-01-30,M,7325550144,80 Main St,New Brunswick,NJ,08901,MRN3003
```

13. File import. Entity `Patient`. Source system **`EPIC_SOUTH`**. Map `source_pk` to the source primary key again. Run. Wait for Success. Loaded 3, rejected 0.
14. If the import does not match by itself, open **Jobs** and run the **Match and merge** job (or Match job, then Merge job) for `Patient`. Wait for Success. This is a separate job type from the file import. Copy its id.
15. Some releases queue match automatically after ingress. If a match job already ran, do not run a second one until you have looked at the result.

**Goal: prove the story on screen.**

16. Search `Shah`. You want **one** Ava, not two.
17. Open her. Source / cross-reference section shows **two** systems: `EPIC_NORTH` / `N1001` and `EPIC_SOUTH` / `S2001`.
18. Master first name should be `Ava` if you ranked North for first name. If it says `Ava S`, survivorship on that field did not apply. Fix the rank and rerun the survivorship / recalculate trust job if one exists. Do not delete her and start over until you have read the job log.
19. Search `Patel`. One Noah. Phone on the master is `2015550102`. Open the South cross-reference and confirm `9735550199` is still stored on the **source** row. Survivorship does not delete the losing value. It chooses the master value.
20. Search `Nguyen`. Leo exists once, South only. He must not be merged to anyone.
21. Search `Chen`. Mia unchanged, North only.

## What you should see

- Ava: 1 master, 2 sources.
- Noah: 1 master, North phone wins, South phone still visible on the source row.
- Leo and Mia: unmerged.
- Bad Date: still not in MDM.

That is the 6.5 million → 3.2 million story at a size you can count.

## If it fails

| What you see | What it usually means |
|---|---|
| Two Avas | Rule not published, first name set to exact, or birth date types differ (string vs date) so exact match never hits. Check the match job pairs. |
| Ava merged with someone else | A rule is too loose (first name fuzzy alone). Tighten: last name exact AND birth date exact are both required. |
| Noah phone is the 973 number | Rank not applied to `phone`, or South was loaded as `EPIC_NORTH` by mistake. |
| South rows rejected as duplicates of the same source key | You imported South with source system North. The `S*` keys are new only inside `EPIC_SOUTH`. |
| Match job never appears | Look for wording **Match and merge**, **Consolidate**, or a checkbox on the import "run match". Use the job the console actually offers. Do not invent a CDI job for this. |

## Write this in the session log

```text
Match model published: yes/no
South import job id:
Match/merge job id:
Ava master count: 1
Ava source count: 2
Noah master phone:
Leo merged to anyone: no
```

## Answer before session 11

1. Why is fuzzy match required for Ava's first name?
2. Why is Noah's South phone still in the system if it "lost"?

## Stop

Next sitting is the hospital tree and the link from patient to doctor: [11-hierarchy-relationship.md](11-hierarchy-relationship.md).
