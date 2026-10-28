# Customer 360 developer course, for this org

Source: Informatica University training guide **Customer 360 SaaS for Developers**, version **C360SaaS-OCT25-DEV-202510**.

That PDF is a classroom course. Its labs log into a training tenant such as `S01_NA_Admin` and then purge Person, edit the shared Person form, import class CSV files, and run match on live class data. **Do not do those labs in your company org.** Your Customer 360 already has real customers. Your Multidomain MDM tile is locked. Do not click **Enable**, **Start trial**, or **Purge**.

Read this file for the words the course uses. The only clicks are in the sitting at the bottom, and they are look-only plus one optional line inside `Test_Pract`.

## The two apps, in one sentence each

| App | Who uses it | What it is |
|---|---|---|
| **Business 360 Console** | The designer. This is the developer seat. | Draw the forms, the lines between forms, the tree shape, the match rules, the survivorship ranks, and the batch jobs. |
| **Customer 360** | The business user. Analyst, manager, steward. | Search, type one record, import a file, open a hierarchy of real records, approve a workflow, read a dashboard. |
| **Administrator** | The admin seat. | Users, roles, the Secure Agent, connections, licenses, schedules. Not the form canvas. |
| **Multidomain MDM** | A second front door. | Same Business 360 platform, but you build every form yourself. No Person, no Organization, no pages, no match model, no reports until you make them. Locked on your org. Do not turn it on. |

Business 360 is the workshop. Customer 360 is the shop floor. The course says they work together. A change you save in the console does not show in Customer 360 until that app is refreshed. A hard refresh is Ctrl+F5.

Your Patient form lives in the workshop, in project `Test_Pract`, in the org whose My Services list shows Business 360. It was never attached to the Customer 360 shop floor. That is why a user who opens Customer 360 cannot create a Patient. Do not attach it so they can.

## What the course calls a data model

The **data model** is the whole design, not one screen.

The course says you define it by doing all of these:

1. Pick the business entities. Customer 360 already has **Person** and **Organization**. You may add a custom one. Patient is a custom one, and it stays in `Test_Pract`.
2. Put fields on those entities. A field is one box. A **field group** is a repeating child, such as many trips under one person. Your Patient was kept flat on purpose: one phone, one address, no child group.
3. Add reference data for picklists: gender, country, salutation.
4. Draw relationships between the entities.
5. Attach data-quality rules, a match model, and survivorship.

A car company in the course might have customers, employees, suppliers, factories, materials, and products. Each of those is a business entity. Together they are the data model.

**Predefined** means the product shipped it. Person and Organization are predefined. They also ship pages, search, a match model, and reports.

**Custom** means you drew it. Patient, Provider, and Location are custom. Multidomain MDM would make you draw everything. Customer 360 lets you start from Person and Organization and still add a custom entity. Adding that custom entity into the live Customer 360 application is a production change. You do not do it here.

## Business entity, the form

A business entity is one thing the company cares about, plus the fields the source systems will send.

Open Person in the console, read only, and you see a canvas full of fields the product already drew. Open Patient in `Test_Pract` and you see only the boxes you added: `first_name`, `last_name`, `birth_date`, and the rest. Both are business entities. Person has data. Patient does not.

Display Name is the label a person reads. Internal ID is the code a job or an API uses. On Patient both say `birth_date` because you typed the same word. One box, one value. The value is empty until a load.

**Subscribed record type** on your Patient dialog was `Customer B2C`. That tells the product "this custom form is a kind of person," not a doctor and not a custom object. Provider used Healthcare Professional. Location used Custom Domain. You cannot change that type after the first record exists. You have no records, and you still do not add one.

## Source system

The course definition: a source system is an external application that sends data into the Business 360 data store. Salesforce and Workday are the examples in the lab. Your three cards are `EPIC_NORTH`, `EPIC_SOUTH`, and `CRED`.

The card stores the name of that application. It does not store Ava. After a load, which you are not running, Ava's Patient row would carry a copy of the name so the product knows which application sent `N1001`.

Survivorship ranks those applications. The course ranks CRM above HR, or similar, after match has found a pair. Rank is how the master phone is chosen. It does not merge people. Match merges. Survivorship picks the winning value. Your North phone `201-555-0102` would outrank South `973-555-0199` if you ranked `EPIC_NORTH` first. You do not configure that on Person.

## Relationship and hierarchy

These are two different assets in **New → Model**.

**Relationship.** A named line. Organization already has Employs, Contact, and Subsidiary. A recursive relationship is a line from Person back to Person, such as spouse. Your line would be Patient treated by Provider. The line is not a column on the form, and it is not a duplicate match.

**Hierarchy model.** The allowed tree. One business entity is the top. Other entities hang under it through hierarchy relationships. The course picture is Organization on top and Person underneath. Your picture is the health system on top and the two hospitals underneath. You draw this in Business 360 Console.

**Hierarchy instance.** One real tree, made of records. The course creates it in Customer 360: **New → Hierarchy**, pick the model, add first-level nodes, then add children. A draft is submitted and may go through approval. You do not create an instance. There is no harmless place in Customer 360 to hang practice hospitals.

`parent_code` in a CSV is only the file's claim. It becomes a hierarchy instance only after a person or a job builds that tree in the application.

## Match, merge, survivorship, jobs

Match asks "are these the same real person?" Merge makes one master and keeps every source row under it. Survivorship runs after that and picks one value when the sources disagree.

The course says:

- Publish the match model **before** the first ingress. Data already in the store is not matched by a model you publish later.
- A published match model cannot be put back to draft, and it cannot be deleted.
- Only one match-and-merge job may run for a business entity at a time.
- A **Generate Merge Tasks** job does not merge. It creates review tasks for pairs a person must confirm.
- Jobs you define in Business 360 Console include ingress, egress, generate search indexes or match keys, and match and merge.

Directed AI match training needs on the order of 1000 records and uses whatever is already in the store. Do not start training. You have no practice load, and the store that does have records is the live customer data.

A workflow is a queue of tasks. A **Customer 360 Analyst** who creates or edits a record can be forced to wait for a **Customer 360 Manager**. A **Data Steward** can create, edit, run jobs, and approve. An **MDM Business User** can only view. An admin who can open the console is not automatically a steward.

## What each role does on a ticket

| Role | Seat | A normal ticket |
|---|---|---|
| Administrator | Administrator | User cannot see the tile. Check the user, the role, and whether the service is assigned. Do not redraw the form. |
| Developer | Business 360 Console | The form, the match rule, the rank, the relationship, or the job definition is wrong. |
| Analyst | Customer 360 | Creates or edits one record. May wait for approval. |
| Manager | Customer 360 | Approves, or edits without approval. |
| Data steward | Customer 360 | Clears the merge queue, runs the job the console defined. |
| Business user | Customer 360 | Can search and cannot save. An empty **New** button for them is a role, not a broken entity. |

## Course modules, and what you do with each

| Module | Course lab | In your org |
|---|---|---|
| 0 Getting started | Download class files, open Customer 360 | You already have the practice CSVs from session 00. Do not download the class files into this org. |
| 0 Purge | Purge all Person and Organization records | **Never.** Purge deletes business-entity records, source records, relationships, and history. |
| 1 Overview | Learn the two apps and the roles | Do the look-only sitting below. |
| 2 Data model | Add a Trip Details field group on Person, add a rule, rank source systems, copy the match model | **Do not open Person and save.** Compare Person to Patient with your eyes only. |
| 3 Onboarding | Create Anthony Lomax and CITIGROUP. Import Person and Organization CSV files from Customer 360 File Import | **Do not.** File import writes master records into the app you have open. |
| 4 Jobs and events | Match and merge Person. Generate merge tasks. Match a CSV and export | **Do not.** One job can merge real customers. |
| 5 Machine learning match | Train on all data in the store | **Do not.** |
| 6 Hierarchies and dashboards | Build an Organization hierarchy instance. Add reports | Look at the Hierarchies menu. Do not add a node. |
| 7 REST APIs | Search, create, and update records with a session id | Study the list below. Do not call the API against this org. A session id expires after 30 minutes of no use. |
| 8 Workshop | Create a custom Family entity for the class story | You already created custom entities in `Test_Pract`. Do not create another one in the Customer360 folder. |

REST resources the course lists, so you can recognise them on a developer ticket:

- Search, across entities or inside one entity.
- Business entity record: create a master, read by business id, read by source key, update.
- Source record: read one source row by entity, source system, and source primary key.
- Relationship: create, read, update, delete.
- Merge and unmerge.
- Job instance.

The internal id of the entity is what the URL uses, such as `c360.person` in the class, not the words on the screen.

## The one sitting you do now

**Time:** 45 minutes. **Org:** the one that has Business 360, not the CDI org with `MERIDIAN_PRACTICE`. **Project you may save into:** `Test_Pract` only.

1. My Services. Open **Business 360 Console**. This is the designer. If a menu shows the organization, copy the org id into your note. Do not click Purge, Jobs, or Match.
2. Use the service switcher. Open **Customer 360** in a new tab. This is the front door. Read the left menu: New, Home, Search, File Import, Workflow Inbox, Hierarchies, My Jobs, Reports and Dashboards. Do not click New. Do not click File Import.
3. Look at the search dropdown. Person and Organization are the predefined business entities. Close the search. Do not type `*` and do not open a record.
4. Switch back to Business 360 Console. Explore. If a `Customer360` folder is there, open **Person**, look at the canvas, and close it without Save. If the header is already dirty, click Close and discard.
5. Open `Test_Pract` and the **Patient** entity. Say the difference out loud: Person was shipped. Patient was drawn. Both are business entities. The data model is the set, not the one canvas.
6. Optional, and only if Location stays `Test_Pract`. **New → Model → Relationship → Create.** Display name `Patient treated by Provider`. Internal id `rel_patient_treated_by_provider`. From Patient to Provider. If the dialog will not keep the relationship inside `Test_Pract`, or if it offers Person or Organization, click Cancel.
7. Do not create a hierarchy instance. You may open **New → Model → Hierarchy** only to see the dialog, then Cancel. The instance would have to be built in Customer 360, and that app is live.

## Write this in the note

```text
Org id seen in Business 360 Console:
Customer 360 opened, no New, no File Import, no record saved: yes
Person canvas opened and closed with no Save: yes/no
Relationship created in Test_Pract: yes/no/cancelled
Purge clicked: no
```

## Answer in your own words

1. Which app do you draw Person in, and which app does a clerk use to search a person?
2. Why is Patient a business entity but not the data model?
3. What is the difference between a hierarchy model and a hierarchy instance?
4. Why must the class purge lab never be run here?

## Stop

Next time you support a ticket, start from the role table. An admin fixes access and the agent. A developer fixes the model, the rule, or the job. A steward fixes one pair or one record. Do not purge, and do not edit Person, to prove you understand any of those.
