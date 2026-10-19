# Session 8 — MDM model: Patient, Provider, Location

**Time:** 70 minutes. **Requires:** you can explain Ava in one sentence. CDI jobs can stay as they are.

## The idea in one minute

CDI moves rows. **MDM SaaS** (Multidomain MDM on IDMC, the **Business 360** console) decides which rows are the same real-world thing and what the trusted values are.

A **business entity** is the type: Patient, Provider, Location. A **record** is one patient. A **source system** is where a row came from (`EPIC_NORTH`, `EPIC_SOUTH`, `CRED`). A **source primary key** (`source_pk`) is that system's id. Two rows with different source primary keys can still be the same person. That is the whole point of match, and you will **not** turn match on in this sitting.

A **field** is a simple attribute (first name). A **field group** is a repeating child (many addresses). Keep Patient **flat** today: one phone, one address. Child groups force extra ingress mappings later. Flat is the right first model.

**Reference data** is a code list (gender, state). **Reference 360** is the service for those lists when you need them governed. For this sitting, gender can be a simple picklist or a text field. Do not block the model on Reference 360.

A **business application** is the screen set a user opens (search, create, view). An entity that is not in an application is easy to "lose" even when it saved.

Customer 360 is a **prebuilt** application on the same MDM base, aimed at customers. You are modeling a **patient** in Business 360. Do not shove Ava into Customer 360 unless that is the only MDM UI your org has. If your org only licensed Customer 360, use it as the console but name the practice clearly and still use fake people.

## Meridian example

| Entity | Why it exists in the story | Key you control |
|---|---|---|
| Patient | The person who was duplicated across EMRs | `source_pk` + source system, plus `mrn` |
| Provider | The doctor on the encounter | `npi` |
| Location | The hospital, later a hierarchy | `hospital_code` |

## Which org

Do not migrate sessions 1–7 into the org that has Business 360. Do not rebuild the mappings, the taskflow, or `rl_dob_is_iso_new` there. Those assets run on the runtime in the org where you already saved them. An export would make you repeat the same clicks.

Use both orgs:

| Work | Where |
|---|---|
| Sessions 1–7 | The org that already has `MERIDIAN_PRACTICE` |
| Sessions 8–11 | The org whose service list contains **Business 360**. Customer 360 is allowed only if Business 360 is missing. |
| Session 12, the connector job | Only if that MDM org also has Data Integration and a Secure Agent. If it does not, use the file-only finish in session 12. Do not move the CDI project to force the connector. |

In the MDM org, start at step 1. You do not need the mappings. You also do not copy any CSV onto this org's Secure Agent for sessions 8 or 9. Session 8 is typed on the screen. Session 9 uploads `patients_north_for_mdm.csv` from your laptop in the file-import dialog. The agent folder matters only in session 12, and only if you build the Business 360 connector. Skip that copy until then.

## Do this

**Goal: open the right console.**

1. Leave the org where you built the mappings. Log into the org from the service list that shows **Business 360**, **Customer 360**, and **Data Quality**. That is the MDM org. Do not import `MERIDIAN_PRACTICE`. Do not recreate `m_land_north`, the taskflow, or `rl_dob_is_iso_new`.
2. In that org, open **My Services**. Open **Business 360**. Do not open Customer 360 unless Business 360 is absent. Do not open Data Integration.
3. If Business 360 is not in this org either, write `MDM NOT LICENSED` and stop. Do not build a fake MDM in CDI.
4. The project `Test_Pract` is the folder for this org. An empty page that says **Nothing to display** is correct. Do not click **New folder**. Do not create `MERIDIAN_PRACTICE` here, and do not rename `Test_Pract`. If a save dialog asks for a project, pick `Test_Pract`. If **New** does not ask for a project, that is normal for source systems and entities. Leave `Test_Pract` empty of mappings.

**Goal: three source systems, before records exist.**

5. Stay inside `Test_Pract`. Do not click the green **New folder** button. Open **New** (the asset menu, not New folder). If you only see **New folder**, use the **Explore** menu or the asset-type list and choose **Source system**. Create the source system in `Test_Pract`.
6. Create `EPIC_NORTH`, `EPIC_SOUTH`, and `CRED`. Display names can match the codes. Save each. These names must match the `source_system` values in the CSV later, same spelling, same case.

**Goal: the Patient entity, flat.**

7. **New** → **Model** → **Business Entity** → **Create**. Fill the dialog, then click **OK**. Do not click OK until Subscribed Record Type is set. After the first patient exists, that value cannot be changed.

| Field on the dialog | What to enter |
|---|---|
| Display Name | `Patient` |
| Internal ID | `patient`. Do not edit it after it fills in. It cannot be changed later. |
| Description | Leave blank. |
| Location | `Test_Pract` |
| Icon | Any person icon. Optional. |
| Subscribed Record Type | `Customer B2C`. A patient is a person. Do not pick Healthcare Professional (that is the doctor). Do not pick Assign Later. |
| Data Domain Tags | Leave blank. Tags are labels only and do not change the data. |
| Error Remediation | Leave **Enable** unchecked. A bad value should not save from the screen. |

The yellow warning only means the record type locks after you add a record. That is expected. Click **OK**.

8. On the blank canvas, add fields. Use text unless noted. Mark `first_name` and `last_name` required. Make `last_name` searchable if the field properties have a search checkbox. Without a searchable field, search in session 9 finds nothing.

| Field | Notes |
|---|---|
| `first_name` | Required |
| `last_name` | Required |
| `birth_date` | Date, not string, if the type exists |
| `gender` | Text or picklist: F, M |
| `phone` | Text |
| `address` | Text |
| `city` | Text |
| `state` | Text |
| `postal_code` | Text |
| `mrn` | Text. This is the hospital number, **not** the MDM id |

9. Do **not** add a field called source primary key if the product already has a system field for it. MDM keeps source system and source primary key as system values on the cross-reference. You will supply them at load time in session 9.
10. Save the entity. If the release has **Publish** on the model, publish. An unpublished model often lets you save and then fails on import.

**Goal: Provider and Location, still flat, still no match rules.**

11. Repeat the same dialog for `Provider`. Internal ID `provider`. Subscribed Record Type: `Healthcare Professional`. Same blank description, same location `Test_Pract`, Error Remediation unchecked. Fields: `npi`, `full_name`, `specialty`, `hospital_code`. Make `full_name` searchable.
12. Repeat for `Location`. Internal ID `location`. Subscribed Record Type: `Custom Domain`. The product lists locations as a custom domain, not as a customer. Fields: `hospital_code`, `hospital_name`, `city`, `state`, `parent_code`. Make `hospital_name` searchable.
13. Save each. Publish if the button is there. Do not create the test patient until all three entities are saved. The record type locks on the first record.

**Goal: an application so you can click Create.**

14. New **Business application** (or add to the existing sandbox application if you are not allowed to create one). Name: `Meridian Practice`. Location: `Test_Pract` if asked.
15. Add Patient, Provider, and Location to it.
16. Open the application as a user would. Open **Create** on Patient. You should see first name, last name, birth date, and the other fields.
17. Create **one** patient by hand: first name `Test`, last name `DeleteMe`, birth date `2000-01-01`, mrn `MRNTEST`. Save. Search `DeleteMe` and find it. Then delete that record if the UI allows, or leave it and remember it is not Ava.

Do not import the CSV in this sitting. Do not open Match.

## What you should see

- Source systems `EPIC_NORTH`, `EPIC_SOUTH`, `CRED`.
- Three entities.
- A create page that shows your fields.
- One hand-entered test patient you can search, or a deleted one.

## If it fails

| What you see | What it usually means |
|---|---|
| No MDM in My Services | License or a different org. Stop. |
| Entity saved but Create does not show fields | Entity is not on the application, or the page layout was not updated. Add it to the layout, not only to the model. |
| Cannot publish | A required system field or privilege. You need a role that can configure the model. Admin usually can. Read the red message; do not create a second entity with a near name. |
| You started adding match rules | Stop. That is session 10. Rules before data make failures harder to read. |

## Write this in the session log

```text
MDM console used:
Project: Test_Pract
Source systems created:
Patient entity published: yes/no
Test record searched: yes/no
```

## Answer before session 9

1. What is the difference between `mrn` and the MDM system's own id?
2. Why are we not matching yet?

## Stop

Next sitting loads the files and searches: [09-mdm-load-and-search.md](09-mdm-load-and-search.md).
