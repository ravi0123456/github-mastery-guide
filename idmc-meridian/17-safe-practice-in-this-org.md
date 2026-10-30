# What you can practice in this org

No new license. No trial. No change to live Customer 360.

You cannot practice the full MDM path on a screen. The only unlocked front door is Customer 360, and that door already holds Person, Organization, Policy, Claim, and Commercial Client. A custom Patient does not appear there until someone adds Patient to that application and saves. That save changes the live application for every user. Do not do it.

Multidomain MDM, Product 360, Supplier 360, and Human Tasks are locked. Do not click the tile, **Enable**, or **Start trial**.

What you can practice is the designer work, inside `Test_Pract`, in Business 360 Console. A few of those steps write a rule onto the empty Patient form. They do not write a customer.

Before every **Save** or **Run**, read the name on the screen. If it says Person, Organization, Policy, Claim, Commercial Client, or the Customer 360 application, click **Cancel**.

## Already done

Confirm these, and do not create them again.

- Source systems `EPIC_NORTH`, `EPIC_SOUTH`, `CRED`
- Entities Patient, Provider, Location
- Pages `Patient Create` and `Patient View`
- Search layout `Patient Search`

## 1. Rank the phone on Patient only

This is survivorship. It picks a winning value later. It does not merge people, and it does not run, because Patient has no rows.

1. Business 360 Console → Explore → `Test_Pract` → open **Patient**.
2. Open the **Survivorship** tab. If a yellow bar sent you here, that is expected.
3. Select the `phone` field. If the screen only lets you rank the whole entity, rank the entity and write that in the note.
4. Source ranking. Rank 1: `EPIC_NORTH`. Rank 2: `EPIC_SOUTH`. Apply.
5. **Save**. The header must still be Patient.
6. Close it. Do not open Person.

## 2. A match rule on Patient only

1. Stay on Patient. Open the **Match** tab.
2. New model. Name `mm_patient_person`.
3. One declarative rule: `last_name` exact, `birth_date` exact, `first_name` fuzzy. Do not mark `phone` as a must-match field.
4. **Save**.
5. If **Publish** is a separate button, publish only after the screen still says Patient. There are no Patient rows, so the rule has nothing to merge.
6. Do not run a match job in this step.

## 3. A line, and a tree shape

1. **New → Model → Relationship → Create.** Location `Test_Pract`. From Patient to Provider. Display name `Patient treated by Provider`. Internal id `rel_patient_treated_by_provider`. Save only if both entities are yours. If the list offers Person or Organization, cancel.
2. **New → Model → Hierarchy → Create.** Location `Test_Pract`. Name `hh_practice_locations`. Top entity Location. If the screen can say a location is the parent of a location, add that. Save the model.
3. Do not open Customer 360. A hierarchy **instance** is the real tree of records, and the only place to build it is the live app.

## 4. Load three fake patients, only if Patient is the target

This is the one step that writes rows. The rows belong to Patient. They are not customers. Skip the step if the screen cannot target Patient.

1. Stay in **Business 360 Console**. **New → File import**, or the file-import job in Explore. Do not use Customer 360 → File Import. That menu loads Person and Organization.
2. Open the target list. If **Patient** is not in the list, cancel. Load practice is over. You also cannot see a search result, a merged master, or a hierarchy instance in this org.
3. If Patient is listed, import only this file. Source system `EPIC_NORTH`. Map `source_pk` to the source primary key.

```csv
source_pk,first_name,last_name,birth_date,gender,phone,mrn
N1001,Ava,Shah,1984-03-12,F,2015550101,MRN1001
N1002,Noah,Patel,1979-11-02,M,2015550102,MRN1002
N1003,Mia,Chen,1991-07-19,F,2015550103,MRN1003
```

4. Run. Wait until the job says success and the count is 3. If the count is not 3, stop. Do not import again.
5. Import the South file the same way. Source system must be `EPIC_SOUTH`, not North.

```csv
source_pk,first_name,last_name,birth_date,gender,phone,mrn
S2001,Ava S,Shah,1984-03-12,F,2015550101,MRN1001
S2002,Noah,Patel,1979-11-02,M,9735550199,MRN1002
S2003,Leo,Nguyen,1988-01-30,M,7325550144,MRN3003
```

6. Success count 3 again. Six source rows now exist on Patient. They are not merged yet. That is correct.
7. Run **Match and merge** only if the job screen has a business-entity field and you set it to **Patient** before Run. If there is no entity field, do not run the job.
8. Do not search in Customer 360. Patient is not on that door. A search there shows live people.

You still will not see the four masters on a page. The job success count is the proof this org can give you.

## Do not practice these here

| You want to try | Why it is not safe or not possible |
|---|---|
| Add Patient to Customer 360 | Changes the live application. Patient shows up in New and Search. |
| Enable Multidomain MDM | Can start a bill. The tile is locked. |
| Edit Person, its match model, or its survivorship | Live customer form. |
| File import, purge, or match while the target is Person or Organization | Writes or deletes live masters. |
| Hierarchy instance, workflow inbox, a new dashboard | Those screens are the live app. |
| New list in Reference 360 | There is no New button. The lists you can see are shared. |
| REST API create or update | Writes a master. Do not call it. |
| Machine-learning match training | Uses the data already in the store, which is live customer data. |

## Note

```text
Survivorship saved on Patient phone: yes/no
Match model saved on Patient: yes/no
Relationship saved in Test_Pract: yes/no
Hierarchy model saved in Test_Pract: yes/no
File import offered Patient: yes/no
North success count:
South success count:
Match job run, entity was Patient: yes/no/not run
Live application saved: no
```
