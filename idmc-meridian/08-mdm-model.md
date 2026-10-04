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

## Do this

**Goal: open the right console.**

1. **My Services**. Open **Business 360** or **MDM**. If you only see **Customer 360**, open that and stay in the admin/model area, not in live customer data.
2. If neither exists, write `MDM NOT LICENSED` and stop this session. Do not build a fake MDM in CDI. Sessions 9–12 wait until an org you admin actually has MDM SaaS.

**Goal: three source systems, before records exist.**

3. Find **Source systems** (Explore, or New, or the model section).
4. Create `EPIC_NORTH`, `EPIC_SOUTH`, and `CRED`. Display names can match the codes. Save each. These names must match the `source_system` values in the CSV later, same spelling, same case.

**Goal: the Patient entity, flat.**

5. **New** → **Business entity** (may be under Model). Name: `Patient`. Internal name if asked: `patient`.
6. Add fields. Use string unless noted. Mark the ones that must show on the create page.

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

7. Do **not** add a field called source primary key if the product already has a system field for it. MDM keeps source system and source primary key as system values on the cross-reference. You will supply them at load time in session 9.
8. Save the entity. If the release has **Publish** on the model, publish. An unpublished model often lets you save and then fails on import.

**Goal: Provider and Location, still flat, still no match rules.**

9. Entity `Provider` with fields `npi`, `full_name`, `specialty`, `hospital_code`.
10. Entity `Location` with fields `hospital_code`, `hospital_name`, `city`, `state`, `parent_code`.
11. Save and publish if required.

**Goal: an application so you can click Create.**

12. New **Business application** (or add to the existing sandbox application if you are not allowed to create one). Name: `Meridian Practice`.
13. Add Patient, Provider, and Location to it.
14. Open the application as a user would. Open **Create** on Patient. You should see first name, last name, birth date, and the other fields.
15. Create **one** patient by hand: first name `Test`, last name `DeleteMe`, birth date `2000-01-01`, mrn `MRNTEST`. Save. Search `DeleteMe` and find it. Then delete that record if the UI allows, or leave it and remember it is not Ava.

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
Source systems created:
Patient entity published: yes/no
Test record searched: yes/no
```

## Answer before session 9

1. What is the difference between `mrn` and the MDM system's own id?
2. Why are we not matching yet?

## Stop

Next sitting loads the files and searches: [09-mdm-load-and-search.md](09-mdm-load-and-search.md).
