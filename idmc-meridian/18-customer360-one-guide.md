# Customer 360, one guide

Source: Informatica University, **Customer 360 SaaS for Developers**, version **C360SaaS-OCT25-DEV-202510**.

Read this file for the course. Do not also study files 16 and 17. Section 7 of [15-concepts-admin-developer.md](15-concepts-admin-developer.md) is only the Ava load story. This file is the rest of the course, in the same words, with the same person.

The classroom PDF purges Person, edits the shared Person form, and imports class files. Those steps are for a training tenant. **Do not run them here.** Your Customer 360 already has real customers. Your Multidomain MDM tile is locked. Do not click **Enable**, **Start trial**, or **Purge**.

The sample project is the one you already have: `Test_Pract`, in the org whose My Services list shows **Business 360**. Do not create a second project. The clicks are at the bottom. Read the ideas first.

## The four seats

| Seat | What you open | What it is for |
|---|---|---|
| Business 360 Console | My Services → Business 360 Console | The workshop. You draw forms, lines, trees, match rules, ranks, and jobs. |
| Customer 360 | My Services → Customer 360 | The shop. A clerk searches, types one person, imports a file, opens a real tree, approves a change, or reads a chart. |
| Administrator | My Services → Administrator | Users, roles, the Secure Agent, connections, licenses, schedules. Not the form. |
| Multidomain MDM | Locked on your org | A second shop where every form is drawn from scratch. Do not turn it on. |

Business 360 is the workshop. Customer 360 is the shop. A save in the workshop does not show in the shop until that browser tab is refreshed. Ctrl+F5.

Your Patient form is in the workshop only. It was never added to the shop. That is why a clerk who opens Customer 360 cannot create a Patient. Do not add it. Adding it changes the live shop for every user.

Customer 360 already comes with two forms, **Person** and **Organization**, plus pages, search, a match model, and reports. Your org's shop also lists Policy, Claim, and Commercial Client. Leave those alone.

## One story for every idea

Ava Shah is born 12 March 1984.

Hospital North stores her as source key `N1001`, phone `2015550101`.
Hospital South stores her as source key `S2001`, name `Ava S`, same birth date, same phone.
Noah Patel is in both hospitals too. His phones do not match. North has `2015550102`. South has `9735550199`.

Those two rows are the same woman. Leo Nguyen, key `S2003`, is a different person. Dr Kim is not a patient. Hackensack hospital is not a person.

Every idea below uses only these people.

## What the product is for

Companies keep the same customer in many systems. The names do not match. The phones do not match. A clerk cannot see one person.

Customer 360 pulls those systems together, cleans the values, decides which rows are the same person, keeps one master, and lets a clerk search that master. Downstream systems can later receive the master. That last step is called egress.

## Customer 360 and Multidomain MDM

Same workshop. Different shop.

| | Customer 360 | Multidomain MDM |
|---|---|---|
| What the product loads once, when an admin first sets the application up | Person and Organization, with their fields, match model, pages, search, and reports | Nothing. You draw every form |
| What this company added later | Claim, Policy, and Commercial Client. They are live. You did not draw them | Still nothing, because the tile is locked |
| Your login | You see those five forms every time you open the shop. The product does not create them again | Locked. Do not enable it |

Person and Organization are not drawn by you, and they are not created again each time you open Customer 360. An admin sets the Customer 360 application up **once**. Informatica then loads those two forms into the org. After that, every user who opens the shop sees the same forms.

Your Workflow Inbox list is Claim, Commercial Client, Organization, Person, and Policy. The first, second, and last of those five are not part of the day-one pair. Someone in this company added them to the live Customer 360 application before you started `Test_Pract`. Leave all five alone. Do not open one and click Save.

Your Patient, Provider, and Location forms are the Multidomain style of work, done inside the workshop, without opening the locked shop. You drew those. They are not in the Workflow Inbox list. That is the proof. You can keep designing them in `Test_Pract`. You cannot open a clerk screen for them unless you attach them to live Customer 360. Do not attach them.

## Who is allowed to do what

A **user** is one IDMC login. A **role** is the set of buttons that login may press.

| Role | May do | May not do |
|---|---|---|
| Administrator | Users, roles, agent, connections, licenses | Draw the match rule |
| Developer, in Business 360 Console | Forms, rules, ranks, jobs, the tree shape | Approve a clerk's edit |
| Customer 360 Analyst | Create or edit a record in the shop | The edit can wait for a manager |
| Customer 360 Manager | Approve, or edit with no approval | Not the workshop canvas |
| Customer 360 Data Steward | Records, jobs, and approvals | Not a license change |
| MDM Business User | Look only | Cannot save a record |

A ticket that says "I cannot see New" is often a role. A ticket that says "these two Avas did not merge" is a match rule. Do not restart the agent for a rule, and do not change the rule because the agent is down.

## The data model is the whole design

A **data model** is not one screen. It is all of these together:

1. The forms, called business entities.
2. The fields on those forms.
3. The small lists, called reference data. Example: Female, Male.
4. The lines between forms, called relationships.
5. The tree shape, called a hierarchy model.
6. The match rules.
7. The source ranking, called survivorship.
8. The clean-up rules, called data quality.

Person is one form. Patient is one form. Neither one is the whole model.

## A business entity is one form

A business entity is one thing the company cares about, plus the fields the source systems will send.

Person is a form the product shipped. Patient is a form you drew in `Test_Pract`. Both are business entities. Person has live rows. Patient does not, unless you later load the sample file and the import screen really says Patient.

**Predefined** means the product shipped it. **Custom** means you drew it.

When you created Patient, the dialog asked for a **subscribed record type**. You chose Customer B2C. That means "this custom form is a kind of person." Provider was a health professional. Location was a custom domain. You cannot change that type after the first record exists. You still do not add a record in the shop.

A car company might also have supplier, factory, and product. Each of those would be another business entity. You do not add those here.

## One field, two names

Each box on the form has two names.

| Name | Who reads it | Example |
|---|---|---|
| Display name | A person on the screen | Birth Date |
| Internal id | A job, a file map, or an API | `birth_date` |

The value is a third thing. For Ava it is `1984-03-12`. The two names are the label and the code. The date is the data.

Same idea as a phone contact. The screen says **Ava**. The phone stores the number. You can change the screen label. If you change the internal id after a job exists, the job looks for a code that is no longer there.

On your Patient form you typed `birth_date` in both boxes. That is fine. They are still two settings.

If the canvas says **Invalid** and **Enter a value**, the display name box is empty. Type the name and save. Do not restart the agent.

## A field group is a repeating child

A normal field holds one value. Ava has one birth date.

A **field group** holds many rows under one person. The course adds Trip Details under Person: many trips, each with its own date. You do not add that group to Person, and you do not add it to Patient. Patient stays flat: one phone, one address.

A **smart field** is a ready-made box with rules already attached. The course uses these to go faster on Person. Do not add one to Person in this org.

## Search flags on a field

On a field you can turn on:

| Flag | What it does |
|---|---|
| Searchable | The word can be found by search |
| Facetable | The clerk can filter by it, such as gender |
| Reportable | A chart can count it |

You already turned Searchable on for Patient `first_name`, `birth_date`, and `mrn`. Leave Facetable and Reportable off. A green bar that says a reindex job started is normal. There are no rows, so the index is empty. Do not cancel that job.

Search does not read every row at the moment you type. It reads a copy called the **index**. The copy can be late. A load can succeed and search can still say zero for a short time. Do not import the file again.

## Reference data is a list, not a person

Reference data is a small allowed list: gender, country, salutation. The course also calls it a picklist.

If the file says `F` and the list only allows `Female`, the row fails. That is not a duplicate person.

Your Reference 360 screen has no **New** button. The lists you can see, such as Account Status, are shared. Do not open them. Do not edit them. Patient gender can stay a plain text box.

## A source system is a name card

A source system is the outside application that sends rows. The course examples are Salesforce and Workday. Yours are `EPIC_NORTH`, `EPIC_SOUTH`, and `CRED`.

The card stores the name of that application. It does not store Ava. You create the card once. You do not type patients into the card.

When a load runs, each row is stamped with that name plus the hospital's own key.

| What | Ava at North | Ava at South |
|---|---|---|
| Source system stamp | `EPIC_NORTH` | `EPIC_SOUTH` |
| Source key | `N1001` | `S2001` |
| Birth date | `1984-03-12` | `1984-03-12` |
| Phone | `2015550101` | `2015550101` |

`S2001` is not a spelling mistake of `N1001`. They are two keys from two hospitals. After a merge, both keys still exist, and both point at one master. The master's own id is the **business id**. You never type it. The product creates it.

The MRN `MRN1001` is a clue on the form. It is not the source key, and it is not the business id.

## Data quality cleans a value

Data quality runs on a field. It does not decide that two people are the same.

| Kind | What it is | Example |
|---|---|---|
| Basic rule | A simple check you write on the field | Birth date must not be empty |
| Advanced rule | A Cloud Data Quality rule | Standardize a phone |
| DaaS rule | A check against an outside service | Check an address |
| Validation that lowers trust | The value is kept, but it is trusted less | A phone with letters |

The course adds a rule on a trip field of Person. Do not. If you add a basic rule, add it on Patient only, and only in the sample project at the bottom.

Cleanse is not merge. Removing spaces from a phone does not make North Ava and South Ava one person.

## Match, then merge, then survivorship

These are three different steps. People mix them up. Do not.

**Match** asks: are these the same real person? North Ava and South Ava should be a pair, because the last name is Shah and the birth date is the same, even though the first name is `Ava` versus `Ava S`. Leo is not in that pair.

**Merge** makes one master and keeps both source rows underneath it. You do not delete `N1001` or `S2001`.

**Survivorship** runs after that. It does not decide who is the same person. It picks one value when the sources disagree. Ava's phones are the same number, so there is nothing to pick. Noah's phones differ. If `EPIC_NORTH` is rank 1, the master phone is `2015550102`. The South number `9735550199` stays on the South source row. A wrong phone is a rank problem. Do not reload the file with a new key to fix it.

You can rank one field differently from the rest of the form. The course calls the two choices **override field configuration** and **retain field configuration**. Override forces every field to use the form's rank. Retain leaves a field alone if that field already has its own rank.

A **decay** rule means an old value loses trust as time passes. You do not need one for Ava.

A **match model** is the saved set of rules. For a predefined form, the course copies the model that shipped with Person. For your custom Patient, you create the model yourself.

One declarative rule is enough for the sample:

- `last_name` exact
- `birth_date` exact
- `first_name` fuzzy

Do not mark phone as a must-match field. The phones are different on purpose.

Rules can be ordered. A later rule can be a fallback. The course also has Directed AI rules, which score pairs without you writing every comparison. You do not turn those on.

Publish the match model **before** the first real load. Rows already in the store are not matched by a model you publish afterwards. A published model cannot be put back to draft, and it cannot be deleted. Your Patient has no rows yet, so publishing the Patient model merges nobody. Never publish a change on the Person model.

Only one match-and-merge job may run for one business entity at a time.

**Machine learning match** is a later course module. You label pairs, the model learns, and you read accuracy, precision, and recall. Training wants on the order of 1000 records and uses whatever is already in the store. That store is live customer data. Do not start training.

## How a row gets in, and how it gets out

| Word | Meaning |
|---|---|
| Ingress | Rows coming in |
| Egress | The master going out to another system |
| File import | A person uploads a CSV in the shop, or a job does it |
| Create | A clerk types one record |
| Generate search index | Rebuild the copy that search reads |
| Match and merge | Run the match model, then merge, then survivorship |
| Generate merge tasks | Do not merge. Make a queue a person must confirm |

A clerk creates a record with **New**, picks Person or Organization, and types the name. An analyst's save can sit in the **Workflow Inbox** until a manager approves. A workflow is that queue. It is not a Data Integration taskflow.

File import reads a CSV, guesses which column maps to which field, and shows a confidence mark on each guess. You still check the mapping. You still set the source system. You still map the source key. If you import the same key again, the product updates that source row. A new key creates another source row.

**Match and export** is different. You do not load the file. You compare a CSV to the masters and download the pairs. The course uses this so a company can see who they already know before a campaign. Do not run it against Person.

## Relationships and hierarchies

These are not match, and they are not each other.

**Relationship.** A named line between two forms, or from a form back to itself. Organization already has Employs, Contact, and Subsidiary. A line from Person back to Person can mean spouse. Your line is Patient treated by Provider. Ava is a patient of Dr Kim. Do not merge the patient and the doctor. The line is not a column on Ava's form.

You draw the line in the workshop. A clerk later attaches two real records to that line in the shop. You will not do that second step. The shop is live.

**Hierarchy model.** The allowed tree, drawn in the workshop. One form is the top. Other forms hang under it. The course picture is Organization on top and Person underneath. Your picture is the health system on top and the two hospitals underneath. Hackensack and Jersey City are not duplicate hospitals. They are two buildings.

**Hierarchy instance.** One real tree of records, built in the shop: **New → Hierarchy**, pick the model, add the first nodes, add the children. It starts as a draft and can go through approval. Do not build one. The only shop you can open is live Customer 360.

A `parent_code` column in a file is only the file's claim. It becomes a tree only after the instance is built.

## Charts

The Customer 360 home page can hold up to six dashboards. A **widget** is the box. A **report** is the table behind the chart. The product ships some reports, such as a count of valid records by business entity. Do not add a widget. The home page is shared.

## REST, enough to read a ticket

A developer can do the same work by API. Each call needs a session id. The session ends after 30 minutes with no use. The format is usually JSON.

| API | What a ticket means |
|---|---|
| Search | Find rows in one form, or across forms |
| Business entity record | Create a master, read it by business id, or update it |
| Source record | Read one source row by form, source system, and source key |
| Relationship | Create or change a line between two records |
| Merge and unmerge | The same pair work as the screen |
| Job | Start or read a job |

The URL uses the internal id, such as `c360.person` in the class, not the words on the screen. Do not call these APIs against your org. A create call writes a live master.

## How you read a ticket

1. Did the job start? If not, the agent, the connection, or the schedule is the problem. The duplicates you see are old.
2. Did the rows land? A success count of zero is the file, the mapping, or the source system. It is not the match rule.
3. If match has not run, two Avas is normal.
4. If match ran and they are still two, compare the birth date and the name. An empty date does not match another empty date.
5. One person, wrong phone. Open both source rows and read the rank. Do not load a new key.
6. The log says the rows exist and search says no. The index is late, or you are in the wrong app. Patient is not on the Customer 360 door.

## The sample project

Project: `Test_Pract`. Org: the one with Business 360. Do not create another project. Do not open Customer 360 to save anything.

You already created the source systems, the three forms, the two Patient pages, and Patient Search. Do not create them again.

Do these four saves. Before every Save or Run, read the name. If it says Person, Organization, Policy, Claim, Commercial Client, or the Customer 360 application, click Cancel.

**1. Rank the phone.** Open Patient. Survivorship. Select `phone`. Rank 1 `EPIC_NORTH`. Rank 2 `EPIC_SOUTH`. Apply. Save. The header must still say Patient.

**2. Match rule.** Match tab. New model `mm_patient_person`. Last name exact, birth date exact, first name fuzzy. Do not match on phone. Save. If Publish is a separate button, publish only while the screen still says Patient.

**3. The line.** New → Model → Relationship. Location `Test_Pract`. From Patient to Provider. Display name `Patient treated by Provider`. Internal id `rel_patient_treated_by_provider`. If the list offers Person, cancel.

**4. The tree shape.** New → Model → Hierarchy. Location `Test_Pract`. Name `hh_practice_locations`. Top form Location. If a location can be the parent of a location, add that. Save the model. Do not build the instance.

**5. Rows, only if the import list contains Patient.** Stay in Business 360 Console. New → File import. Do not use the File Import menu inside Customer 360. If Patient is not in the target list, cancel and stop. You cannot see the masters on a page in this org.

If Patient is listed, load North first. Source system `EPIC_NORTH`. Map `source_pk` to the source primary key.

```csv
source_pk,first_name,last_name,birth_date,gender,phone,mrn
N1001,Ava,Shah,1984-03-12,F,2015550101,MRN1001
N1002,Noah,Patel,1979-11-02,M,2015550102,MRN1002
N1003,Mia,Chen,1991-07-19,F,2015550103,MRN1003
```

Wait for success count 3. Then load South. Source system must be `EPIC_SOUTH`.

```csv
source_pk,first_name,last_name,birth_date,gender,phone,mrn
S2001,Ava S,Shah,1984-03-12,F,2015550101,MRN1001
S2002,Noah,Patel,1979-11-02,M,9735550199,MRN1002
S2003,Leo,Nguyen,1988-01-30,M,7325550144,MRN3003
```

Success count 3 again. Six source rows. Not merged yet. That is correct.

Run Match and merge only if the job has a business-entity field and you set it to Patient before Run. If there is no entity field, do not run it. You still will not see four masters on a page. The job count is the proof. Do not search in Customer 360.

What that load means, if it is allowed to finish:

| Person | North key | South key | After match |
|---|---|---|---|
| Ava Shah | `N1001` | `S2001` | One master. The phones already match. |
| Noah Patel | `N1002` | `S2002` | One master. Phone `2015550102` if North is rank 1. |
| Mia Chen | `N1003` | none | Stays one master. |
| Leo Nguyen | none | `S2003` | Stays one master. |

Four masters. Six source rows. Two of those masters have two source keys each.

## What you do not practice here

| Idea | Why it stays on paper |
|---|---|
| Purge | Deletes live Person and Organization rows |
| Edit Person, its match model, or its rank | Live form |
| Add Patient to Customer 360 | Changes the live shop |
| Enable Multidomain MDM | Can start a bill |
| Hierarchy instance, workflow inbox, a new dashboard | Those screens are the live shop |
| A new Reference 360 list | No New button, and the lists are shared |
| REST create or update | Writes a master |
| Machine-learning training | Uses the live store |

## Say it back

1. Which app do you draw a form in, and which app does a clerk use to search a person?
2. Why is Patient a business entity, but not the data model?
3. What do match, merge, and survivorship each do to Ava and to Noah?
4. What is the difference between a hierarchy model and a hierarchy instance?
5. Why must the class purge lab never be run here?
