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
