# Session 8 — MDM model: Patient, Provider, Location

**Time:** 70 minutes. **Requires:** you can explain Ava in one sentence. CDI jobs can stay as they are.

## The idea in one minute

CDI moves rows. **MDM SaaS** decides which of those rows are the same real person, and which value to trust when the hospitals disagree.

A **source system** (`EPIC_NORTH`, `EPIC_SOUTH`, `CRED`) is only a stamp. It does not hold the CSV. It is not a folder and not a table. The file stays on the Secure Agent, or you upload it later in a load screen. A job reads the file and writes the rows into the model. The stamp on each row answers "which system typed this?"

A **business entity** is the empty form: Patient, Provider, Location. You design that form once in Business 360 Console. You do not type every patient into the UI. A **record** is one patient, and it appears only after a load job, or after someone creates one row by hand. A **source primary key** (`source_pk`, such as `N1001`) is that system's own id. Two different keys can still be the same person. That decision is match, and you will **not** turn match on in this sitting.

A **field** is a simple attribute (first name). A **field group** is a repeating child (many addresses). Keep Patient **flat** today: one phone, one address. Child groups force extra ingress mappings later. Flat is the right first model.

**Reference data** is a code list (gender, state). **Reference 360** is the service for those lists when you need them governed. For this sitting, gender can be a simple picklist or a text field. Do not block the model on Reference 360.

A **business application** is the door a user opens to search and create. The form can exist with no door. Customer 360 is a **prebuilt** door. On a shared org it already holds live entities such as Person, Policy, and Claim. Do not add Patient to it. Use **Multidomain MDM** when that service is in My Services. If it is locked, leave the practice assets in `Test_Pract` and do not attach them.

The full explanation of stamp, form, job, load, match, merge, and the ticket order is in [15-concepts-admin-developer.md](15-concepts-admin-developer.md), section 7. Read that. The steps below are only the clicks for this sitting.

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

8. You are on the Patient canvas. The green **Valid** mark is correct. Stay on **Model** → **Attributes**. Do not open **Data Quality**, **Match**, **Survivorship**, or **Events**.

The left list is **Basic Fields**. It shows **Text** and **Integer** first. Scroll that list until you see **Date**. Do not use **Date and Time**.

A new field lands on the canvas with an empty name. The header turns **Invalid** and **Properties** says **Enter a value.** That is expected. Do not delete the field. Do not open the top **Data Quality**, **Match**, or **Survivorship** tabs.

Click the new box. In **Properties**, stay on **General**.

1. **Status** stays **Enabled**.
2. **Display Name:** type the name from the table, for this first Date field `birth_date`. The red X goes away when you leave the box.
3. **Internal ID:** type the same name, `birth_date`. If the box fills itself from the display name, leave that value. Do not add spaces. Lower case and the underscore only.
4. Scroll **General** if you see **Required**. Turn it on only for `first_name` and `last_name`. Leave it off for `birth_date`.
5. Do not open **Data Quality** or **Survivorship** inside Properties.
6. For `last_name` only, after the name is set, click **Search and Reports** in the left of Properties and turn on **Searchable**. Leave Facetable and Reportable off. Every other field stays not searchable.
7. Click **Save**. The header must say **Valid** before you double-click the next type. If it stays Invalid, one field still has an empty Display Name. Click that box and fill it. Do not add the next field while the header says Invalid.

| Display Name | Double-click | Required | Search |
|---|---|---|---|
| `first_name` | Text | yes | no |
| `last_name` | Text | yes | yes |
| `birth_date` | Date | no | no |
| `gender` | Text | no | no |
| `phone` | Text | no | no |
| `address` | Text | no | no |
| `city` | Text | no | no |
| `state` | Text | no | no |
| `postal_code` | Text | no | no |
| `mrn` | Text | no | no |

To mark required, select the field and turn on **Required** (or **Mandatory**) in Properties. Leave the text length at the default.

To make `last_name` searchable: select that field, click **Search and Reports** on the left of Properties, and turn on **Searchable**. One searchable field is enough. Do not turn on Facetable or Reportable.

`mrn` is the hospital number. It is not the MDM id. Do not add a field named source primary key, source system, or business ID. MDM already stores those.

Leave the text length alone. Do not add a picklist for gender. `F` and `M` are plain text.

9. Click **Save**. The header must still say **Valid**, and a green bar says **The business entity was saved successfully.** There is no separate Publish button on this canvas. Save is the publish for the model.

A yellow bar may say **Ensure that you configure survivorship before you start to use the business entity.** Close it. Do not open the **Survivorship** tab. Survivorship decides which source wins when two hospitals disagree. You have no second source yet, so there is nothing to rank. That work is session 10, after the load. The same yellow bar will appear on Provider and Location. Ignore it there too.

**Goal: Provider and Location, still flat, still no match rules.**

10. Repeat the same dialog for `Provider`. Internal ID `provider`. Subscribed Record Type: `Healthcare Professional`. Same blank description, same location `Test_Pract`, Error Remediation unchecked. Fields: `npi`, `full_name`, `specialty`, `hospital_code`. Make `full_name` searchable.
11. Repeat for `Location`. Internal ID `location`. Subscribed Record Type: `Custom Domain`. The product lists locations as a custom domain, not as a customer. Fields: `hospital_code`, `hospital_name`, `city`, `state`, `parent_code`. Make `hospital_name` searchable.
12. Save each. Publish if the button is there. Do not create the test patient until all three entities are saved. The record type locks on the first record.

**Goal: a Create page, or the screen has no form.**

13. Do not open Match or Survivorship. Close Patient if it is still open.
14. **New** → **User Interface** → **Page** → **Create**. If the menu says **Business Application** instead of **User Interface**, use that, then **Page**.
15. Fill the dialog and click **OK**.

| Field | Value |
|---|---|
| Display Name | `Patient Create` |
| Internal ID | `patient_create` |
| Description | blank |
| Business Entity | `Patient` |
| Page Type | **Create** |
| Location | `Test_Pract` |

16. A blank page opens. On the left, open **Page Components**. Drag **Page Header** into the header area. Then drag each Patient field onto the page: `first_name`, `last_name`, `birth_date`, `gender`, `phone`, `address`, `city`, `state`, `postal_code`, `mrn`. If a component is named **Basic Fields** or **Field Group** and it can hold all of them, use that once instead of ten drags.
17. Click the checkmark (**Validate**). Fix any red field. Click **Save**. The page must say valid.
18. Repeat steps 14–17 for a second page. Display Name `Patient View`, Internal ID `patient_view`, Page Type **View and edit**. Open the **Business Entity** dropdown and select `Patient`. Do not select `Provider` or `Location`. The red **Enter a value** goes away only after `Patient` is selected. Location stays `Test_Pract`. Click **OK**, then drag the same Patient fields. Validate and save.

**Goal: search can show a row.**

19. **New** → **User Interface** → **Component** → **Create**. Pick **Search Layout** (it may be named **Search Result Layout**).
20. Business entity `Patient`. Location `Test_Pract`. Display Name `Patient Search`. Internal ID `patient_search`.
21. The layout has several boxes. Click a box, set it in the **Properties** panel on the right, then click **Apply**. The blue **Save** is last, after every Apply.

| Box | What to set |
|---|---|
| Title | **Title Field** = `last_name`. Click **Apply**. |
| Basic Fields | `first_name`, `birth_date`, `mrn`. Separator **Comma**. Click **Apply**. |
| Hierarchy Filter, Facets, Thumbnail, System Fields, Additional Fields | Leave empty. Do not open them. |

If **Basic Fields** has no names except `last_name`, the other fields are not searchable yet. Close this layout. Open the **Patient** entity. Select `first_name`, open **Search and Reports**, turn on **Searchable**. Do the same for `birth_date` and `mrn`. `last_name` is already searchable. Leave **Auto Suggest**, **Facetable**, and **Reportable** off on every field. Do not open **Match**, **Survivorship**, **Data Quality**, or **Events**.

Save Patient. A green bar that says **The business entity was saved successfully, and the reindex job ... has started** is correct. Do not cancel that job and do not open **My Jobs** unless the bar is still there after several minutes. There are no patient records yet, so the job is only rebuilding an empty index.

Then check the three fields, not only `mrn`. Click `first_name` and confirm **Searchable** is on. Click `birth_date` and confirm the same. `mrn` already shows **Searchable** on. Close Patient. Reopen **Patient Search**, click the **Basic Fields** box, select `first_name`, `birth_date`, and `mrn`, click **Apply**, then the blue **Save**. Do not add a hierarchy filter.

**Goal: use the entities you already created. Do not create them again.**

These assets already exist in `Test_Pract`. Stop if you are about to click **New** → **Business Entity**.

| Already created | Do not create another |
|---|---|
| Source systems `EPIC_NORTH`, `EPIC_SOUTH`, `CRED` | a fourth source system |
| Business entities `Patient`, `Provider`, `Location` | a second Patient |
| Pages `Patient Create` and `Patient View` | a third page |
| Component `Patient Search` | a second search layout |

This org cannot create a second application from **New**. The licensed application is what you open. **Customer 360** on this screen already has Person, Organization, Policy, Claim, and Commercial Client. Those are in use. Do not add Patient to that list.

Adding Patient would not rewrite those five entities. It would still change the live application the moment you click **Save**: Patient would appear in **New** and **Search** for every role you assign, and a test record would be stored in the Customer 360 master. Do not do that in this org.

**Goal: leave Customer 360 untouched.**

22. You are on the right designer (tabs **Model**, **Pages**, **Search**). Do not click **+** next to **Business Entities (5)**. Do not click **Save**. Close the Customer 360 tab.
23. Open **My Services**. **Multidomain MDM** is grey, with a lock. The license is not on this org, and it cannot be assigned from **Administrator**. Do not click the tile. The same lock is on **Product 360**, **Supplier 360**, and **Human Tasks**. Do not click those either. Do not accept a trial. Do not open **Customer 360**.
24. Session 8 is complete. The assets in `Test_Pract` are the practice. Do not attach them to Customer 360, and do not create a test patient. Sessions 9, 10, 11, and 12 stay closed. Those sittings load and match records, and the only open application that can hold them is the live Customer 360.

**Goal: do not create reference data. This org will not let you, and the lists on screen are live.**

25. You are on Reference 360 **Explore**. There is no **New** button. The left list already contains live assets such as `AccountStatus`, `AddressType`, and `BusinessType`. Do not open them. Do not click the three-dot menu. Do not search for a gender list and edit it.
26. Close Reference 360. Write `SKIP` in the log. A missing **New** means this user can look, not create. Creating a practice code list here is not required for the model you already built, and it is not a substitute for loading patients.

Do not import the CSV. Do not open Match. Do not configure survivorship. Do not open sessions 9, 10, 11, or 12.

## What you should see

- Source systems `EPIC_NORTH`, `EPIC_SOUTH`, `CRED`.
- Three entities, two pages, and `Patient Search` in `Test_Pract`.
- Customer 360 still lists only its original five entities.
- No test patient.
- Reference 360 left closed. No code list created. `SKIP`.

## If it fails

| What you see | What it usually means |
|---|---|
| No MDM in My Services | License or a different org. Stop. |
| Create shows no fields | You are in the wrong application. Close it. Do not add Patient to Customer 360. |
| Cannot publish | A required system field or privilege. You need a role that can configure the model. Admin usually can. Read the red message; do not create a second entity with a near name. |
| Yellow bar about survivorship | Expected. Close it. Do not configure survivorship in this sitting. Session 10 does that after two sources exist. |
| Green bar says a reindex job started | Expected after you turn **Searchable** on. Do not cancel it. Go back to Patient Search. |

## Write this in the session log

```text
MDM console used: Business 360 Console
Project: Test_Pract
Source systems created: EPIC_NORTH, EPIC_SOUTH, CRED
Patient entity published: yes
Customer 360 changed: no
Multidomain MDM: cannot enable
Test record searched: no
Code list: SKIP, Explore has no New
```

## Answer before the next sitting

1. What is the difference between `mrn` and the MDM system's own id?
2. Why are we not matching yet?
3. Why was Patient not added to Customer 360?

## Stop

Do not open [09-mdm-load-and-search.md](09-mdm-load-and-search.md). The license cannot be added on this org. The next clicks are steps 25 to 30 above, then stop.

Leaving the lock in place is safe. It does not finish the story. The finish line in [00-start-here.md](00-start-here.md) is one Ava, with both hospitals, after match. That needs sessions 9 through 12, and those need Multidomain MDM. Read those chapters in [15-concepts-admin-developer.md](15-concepts-admin-developer.md). Do not click them in this org. Do not use Customer 360 to force it.
