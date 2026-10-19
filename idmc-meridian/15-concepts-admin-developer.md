# Concepts — the whole path, in plain English

Read this file from the top. It is written for someone who has never used Informatica MDM. The numbered session files are the buttons you click. This file is why those buttons exist, how they connect, and what you say when a ticket arrives.

You do not need a second course. When a word here is not enough for a live org, use Informatica's own current-version book, not a blog:

- [Administrator](https://docs.informatica.com/cloud-common-services/administrator/current-version.html) — users, licenses, the agent, connections
- [Cloud Data Integration](https://docs.informatica.com/integration-cloud/cloud-data-integration/current-version.html) — mappings and jobs
- [Data Quality](https://docs.informatica.com/data-governance-and-quality-cloud/data-quality/current-version.html) — rules and profiles
- [Business 360 Console](https://docs.informatica.com/master-data-management-cloud/business-360-console/current-version.html) — the MDM model
- [Monitor](https://docs.informatica.com/cloud-common-services/monitor/current-version.html) — proof a job ran

## Contents

1. One person, two hospitals
2. What the platform actually is
3. The path of one row
4. Administrator, one idea at a time
5. Data Integration, walking Ava's file
6. Data Quality
7. MDM SaaS from the beginning
8. Reference 360, and why you closed it
9. The same Ava, every tool, in order
10. Tickets, worked in full
11. What you finished, and what you only studied

## 1. One person, two hospitals

Hackensack Meridian Health grew by joining hospitals. Each hospital kept its own electronic medical record, often Epic. The same human was typed in more than once.

Ava Shah is the practice version of that problem. All of these people are fake.

- Epic North stores her as `Ava`.
- Epic South stores her as `Ava S`.
- The date of birth matches. The address is the same house, written two ways.
- Noah Patel is in both systems too, with two different phones. North has `201-555-0102`. South has `9735550199`.
- Mia Chen exists only in North. Leo Nguyen exists only in South. They are not duplicates.
- One North row has the birth date `NOT_A_DATE`. That row must never become a patient.
- Dr Kim Alvarez is a doctor, not a patient. A hospital is a place, not a person.

The question the whole guide is trying to answer is this: **after the jobs run, can you list every visit Ava had, in every hospital, on one person?**

That question needs more than one product. A file job can clean a spreadsheet. It cannot decide that `Ava` and `Ava S` are the same human and then remember both hospital visits underneath one trusted record. That decision is master data management.

## 2. What the platform actually is

IDMC means Informatica Intelligent Data Management Cloud. Older screens say IICS. It is the same website. You open a browser, pick an org, and land on **My Services**. Each tile is a product: Data Integration, Data Quality, Business 360 Console, Customer 360, Reference 360, Administrator, Monitor.

An **org** is one company's space in that website. Users, licenses, and saved work belong to the org. This practice used two orgs on purpose. The jobs that move files stayed in the org that already had them. The Patient model was built in the other org, in a project named `Test_Pract`. Nothing was copied from one org into the other. Production was not used.

Think of the website as an office. The org is your company's floor. Each tile is a department. The **Secure Agent** is a clerk who sits on a server your company owns, not inside the browser. When a job must read a CSV, the cloud sends the instructions to that clerk. The clerk reads the file on that server. A file that exists only on your laptop is invisible to the job.

A **license** is what the company paid for. A grey tile with a lock can mean "this user was not given the product" or "the product is not on this org." **Multidomain MDM** on your screen is the second kind. You confirmed it cannot be assigned. That is not a mistake in the Patient entity. Do not click **Enable** or **Start trial**. Those buttons can start a bill.

## 3. The path of one row

Ignore product names for a minute. Ava's North row travels like this.

1. Someone exports Epic into a CSV. The row says `N1001`, `Ava`, a birth date, a phone, a state code `NJ`.
2. The file is placed in a folder the Secure Agent can see.
3. A job reads the file. It rejects `NOT_A_DATE`. It turns `NJ` into `New Jersey`. It attaches Ava's visits. It writes a clean file.
4. A later job, or a person clicking Import, gives that clean file to MDM and says "these rows came from Epic North."
5. MDM stores each row as a **source record**. At this moment Ava North and Ava South are still two people. That is correct. Nobody has asked "are they the same human?" yet.
6. A match job compares names and birth dates. It proposes the pair.
7. A merge makes **one master**. Both source keys stay attached, so you can still see the North visit and the South visit.
8. A survivorship rule picks one phone when the two sources disagree.
9. A search finds that one Ava.
10. An outbound job writes a file of masters for some other system.

Steps 1 to 3 are what you ran. Steps 4 to 10 are what the locked license stopped you from clicking. They are still part of the job you will be asked to support. The rest of this file walks both halves with the same names.

## 4. Administrator, one idea at a time

An administrator does not fix a wrong formula by redrawing the mapping. An administrator answers: can this person open the product, is the clerk awake, and can the clerk see the folder?

### The org, the user, and the role

You log in as a user. A **role** is a bundle of permissions. A designer role can create mappings and entities. A business-user role can search and edit rows only if a screen was assigned to that role.

**Assign Services** is a separate switch. A person can be an admin of Data Integration and still see a lock on Business 360 if nobody assigned that service to them. You checked this. Multidomain MDM stayed locked because the org does not have the product, not because a checkbox was missed.

When a ticket says "I cannot see the tile," look in this order. Is the tile missing for every user, or only this user? If everyone, it is a license. If only this user, open the user and look at assigned services and roles. Do not build a second copy of the asset to "test the permission."

### The Secure Agent

In **Administrator → Runtime Environments**, open the agent. Two lines matter.

**Data Integration Server** runs mappings, mapping tasks, and taskflows. It must say **Up and Running**. If it is stopped, every file job sits in a queue or fails the same way. That is an agent problem, not twenty mapping problems.

**Process Server** runs Application Integration processes. Those are the flows that can start an MDM load and pass it an id. Your screen has said **Restart Required**. That does not stop Data Integration. Leave it. Restart Process Server only when you are about to run a process, or when Informatica support told you to. Do not restart the whole agent because one mapping failed validation. A restart drops every job on that agent.

### The connection

A **connection** is a saved name for a place the agent can reach. Your practice connection is a flat-file connection. It is a folder path, not a database. **Test** asks the agent, not your laptop, "can you see this folder?"

If the test fails, the usual causes are: the path is wrong, the agent runs as a Windows or Linux user that cannot read the folder, or you tested a different runtime from the one that holds the files. Copying the CSV onto your desktop does not fix it. The file has to be on the agent machine, in the folder the connection names.

### Save, publish, and the version a job runs

**Save** keeps your draft. **Publish** says "this version is the one jobs may run."

This is why a ticket says "I fixed it and it is still wrong." The picture on the screen is the draft. The mapping task is still running the last published version. Publish, then run once, then read the new job in Monitor. Do not create `m_land_north_new` beside the old mapping. You will forget which one the task points at.

The same rule applies to a taskflow and to an Application Integration process.

### Monitor

**Monitor** is the proof. Open the job. Read **Success**, **Warning**, or **Failed**. Then open the session log and read the first error. Later errors are often the same cause repeated per row.

A screenshot of the mapping canvas is not a log. A ticket needs the org name, the asset name, and the job id.

## 5. Data Integration, walking Ava's file

Data Integration, also called CDI, moves and reshapes files and tables. It does not decide that two people are the same human. That is MDM. CDI's job in this story is to make a clean, trustworthy file so MDM is not handed garbage.

### A mapping is a drawing. A mapping task is the job.

You open a canvas and place boxes left to right. The left box is the source file. The middle boxes change rows. The right box is the target file. That picture is a **mapping**. It does not run by itself.

A **mapping task** points at one mapping, one runtime (the agent), and the connections. When you click **Run**, Monitor shows a job. `m_land_north` was the drawing. The task was the thing that actually read the file.

If a field disappears on the next box, it was not linked across, or the name changed. Follow one field with your finger from source to target before you change a formula.

### Expression: one row at a time

An **expression** is a formula. It sees the current row only. `UPPER(first_name)` turns `Ava` into `AVA`. It does not look at Ava's other visits.

The bad row has birth date `NOT_A_DATE`. A date formula cannot parse that. The right design sends that row to an error file and lets the good rows continue. Deleting the bad row inside the formula, with no error file, hides the problem. The next person cannot see that Epic sent garbage.

### Lookup: a dictionary beside the row

The patient file says `NJ`. A small state file says `NJ` means `New Jersey`. A **lookup** is that dictionary. For each patient row, Informatica searches the state file on the code and adds the name.

If the state name comes out empty and the job is still Success, the usual cause is "no row in the dictionary had that code," not "the agent is down." An agent that is down fails the job before the first row. A code with a trailing space (`NJ `) also misses. The dictionary file must sit in the agent folder, same as the patient file.

### Joiner: two files become one wider file

A patient file has one row per person. An encounter file has one row per visit, and many visits per person. A **joiner** lines them up on a key, usually the patient id.

Ava has three visits in two hospitals. After a correct join you can see those three visits next to her name. If you then count them, the count is 3.

A **normal** join drops a patient who has no visit. A **master outer** join keeps the patient and leaves the visit columns empty. Use the outer join when "no visit" is a real answer, not a mistake.

If the output suddenly has dozens of Avas, the key was not unique. You joined on a name, or on a code that repeats, and every left row matched every right row. That is a developer bug. The agent is fine.

### Aggregator: many rows become one

An **aggregator** collapses rows. Group by the patient id, and ask for a count of visits. You get one row per patient. Forget the group-by and you get one row for the whole file, which is a different question.

Joiner and aggregator answer different questions. The joiner says "show me each visit." The aggregator says "how many visits?" The business question wants both: the count, and the ability to list the visits. You do not throw the visit rows away if a later step still needs them.

### Filter and router

A **filter** keeps some rows and drops the rest. A **router** sends rows down more than one path at once: good rows to the clean file, bad dates to the error file. Prefer a router when you must keep the bad rows somewhere. A filter that drops them leaves no evidence.

### Taskflow: a job made of jobs

A **taskflow** calls one or more mapping tasks and can branch when one fails. You built one. If the canvas shows a single subtask, that is expected when you only dropped one mapping task on it. It is not a broken taskflow.

**Publish** the taskflow before you run it. Run it once from the designer or from Monitor. Then open the schedule and make sure it is not left on. A schedule is only a clock. A clock left on runs the practice job again tonight, against the same files.

### Parameters: one mapping, two runs

A parameter is a value you do not hard-code into the drawing.

An **input** parameter is read-only inside the mapping. Example: `state_filter` is `NJ` for this run. The filter keeps New Jersey rows. Tomorrow the task passes `NY` and the same mapping keeps New York rows. You did not draw a second mapping. If the task leaves the parameter blank, the job fails at the start or writes the wrong file. That is configuration. It is not a broken agent.

An **in-out** parameter can be changed by the mapping and read by the next step. Example: the mapping counts the rows it wrote and sets `row_count` to `6`. The taskflow reads `6`. If `6` is `0`, it can send a failure mail instead of calling the next job. The mapping both receives a starting value and hands a new value back. That is the whole difference. Input is "please use this." In-out is "please use this, and tell me what you changed it to."

## 6. Data Quality

A formula inside one mapping is private to that mapping. A **rule** in Data Quality is a named check you can reuse. "Birth date must be a real date" should live once, then be dropped onto the North mapping and the South mapping. If the rule is wrong, you fix it once.

A **profile** does not fix anything. It photographs a column: how many nulls, which values appear, which patterns. You run it to see the shape of the file before you write the rule. A score is not the same as an error file. The error file is the rejected row. The profile is the summary.

If the profile will not run, check the Data Quality license and that Data Integration Server is up. A rule that fails its own test is a rule problem. Do not restart the agent for a formula that rejects the sample row you typed.

## 7. MDM SaaS from the beginning

This is the part that felt missing. Read it even though you could not load a patient.

### What "master data" means

Not every file is master data. A visit, a claim line, and a payment are transactions. They happen, and they pile up. **Master data** is the noun those transactions point at: the person, the doctor, the hospital, the product, the supplier. The business wants one trusted copy of each noun, because every other system refers to it.

Ava is master data. Her three visits are transactions that should hang off the one Ava. If you match the visits to each other, you have asked the wrong question. You match the people. You keep the visits.

### Three layers, and they are not the same thing

Use Ava and only Ava.

| Layer | What it is | Ava's example |
|---|---|---|
| Source system | The application that sent the row. A label, not a Secure Agent and not a connection. | `EPIC_NORTH` and `EPIC_SOUTH` |
| Source record | That application's own row, stored under the master so you can still see it. | North row key `N1001`, name `Ava`. South row key `S2001`, name `Ava S`. |
| Master, also called the golden record | The one trusted person MDM shows to a user after match and survivorship. | One Ava Shah. Both visits still listed underneath. |

Until a match job runs, MDM treats the North row and the South row as two masters. That looks wrong to a human and it is still the correct product behavior. Match is a separate step. Loading data does not silently merge people.

### What you actually created in `Test_Pract`

You did not create patients. You created the empty form and the labels.

**Source systems** `EPIC_NORTH`, `EPIC_SOUTH`, and `CRED` are the labels a future load will stamp on each row. Creating the label does not connect to Epic and does not read a file.

A **business entity** is the form. Patient has fields such as first name, last name, birth date, and `mrn`. Provider and Location are two more forms because a doctor and a hospital are not patients. Putting a hospital name on the Patient form would mix the nouns.

Each field has a **display name** (what a person reads) and an **internal ID** (what the system stores). You saw the entity go **Invalid** when a field's display name was empty. Fill the name before you add the next field. **Required** means a user cannot save a patient without it. **Searchable** means the search index will include it. Turning Searchable on starts a reindex job. That job is expected. Do not cancel it.

A **page** is the screen: Patient Create, Patient View. A **search layout** is the table of results. These do nothing for a business user until some **business application** lists the Patient entity and your role is allowed to open that screen.

### The application is not the entity

This distinction is the one that blocked the rest of the practice.

The entity is the form, sitting in Business 360 Console. The application is the product a user opens to create and search records. Customer 360, Supplier 360, Product 360, and Multidomain MDM are applications on the same MDM base. Customer 360 arrives with its own forms already: Person, Organization, Policy, Claim, and others. Those are in use at this company.

Your Patient form is not on that list. You could have clicked **Add Business Entity** and attached Patient to Customer 360. The moment you saved, Patient would show up on the live **New** and **Search** menus, and a test row would be stored in the customer master the company already uses. You did not do that. That was the right stop.

**Multidomain MDM** is the application meant for a domain the prebuilt apps do not cover, such as a patient who is not a Customer 360 person. It is grey and locked here. The license cannot be added from Administrator. Sessions 9 through 12 stay closed. Do not practice them inside Customer 360.

### Three different ids, because tickets mix them up

| Name | Example | Who made it | What happens if you load the same person again |
|---|---|---|---|
| `mrn` | a hospital medical-record number you put on the form | the hospital, stored as an ordinary field | Nothing special. It is just a value. A second load can create a second master that happens to show the same mrn. |
| Source primary key | `N1001` inside Epic North | Epic | If you mapped this column to the source primary key, the second load **updates** the same source record. If you mapped it to an ordinary field, the second load **inserts** another master. This is the usual "we loaded it twice" ticket. |
| Business id | a long id Informatica generates | MDM | You never type it. It identifies the master. It is not the mrn. |

`N1001` and `S2001` are allowed to be different. They are keys inside two different source systems. Match is what ties them to one business id. The cross-reference is that tie: "business id X came from Epic North key `N1001` and from Epic South key `S2001`."

### Load, also called ingress

Ingress means "into MDM."

The small version is a file import. You upload the clean North CSV, pick the source system `EPIC_NORTH`, map `N1001` to the source primary key, map the name and birth date to the Patient fields, and run. Search then finds Ava from North. Ava from South is not there yet. You repeat the import with the South file and source system `EPIC_SOUTH`.

The production version is a Data Integration mapping that uses the Business 360 connection. An Application Integration process often starts that job and passes a `jobInstanceId`. If the mapping refuses to run without that id, do not invent one. The process is supposed to create it. Inventing an id is how a practice load becomes an untraceable job.

A bad date must already have been removed by the CDI job. MDM should not be the first place that discovers `NOT_A_DATE`.

### Match

A **match rule** says which fields must agree. For this story: birth date is equal, and the name is similar enough that `Ava` and `Ava S` propose a pair. Mia does not pair with anyone. That is the result you want.

A **match population** is the comparison method. Person-name logic is different from company-name logic. Using the company population on patients misses obvious pairs or pairs strangers.

A **match job** does the comparison. It does not always merge. A rule can be "auto-merge when the score is very high" or "put the pair in a queue for a person." That person is a **steward**. The queue is how a hospital avoids merging two different babies who share a birthday and a similar name.

### Merge

Merge collapses the pair into one master. It does not delete the source rows. North's `N1001` and South's `S2001` both remain as cross-references. The visits remain. This is why a real project can turn 6.5 million source rows into 3.2 million masters. The extra 3.3 million were duplicates. The history did not disappear.

If a third Ava appears after a merge, someone loaded the same Epic row again with a new source primary key. Do not merge at random to "clean up." Note the key. A wrong merge of two real people is harder to undo than a duplicate.

### Survivorship

After the merge, Noah has two phones. The master can hold only one "current phone" if that is how the form was built. **Survivorship** is the rule that picks the winner.

A source ranking that puts Epic North above Epic South makes the golden phone `201-555-0102`. The South phone `9735550199` stays on the South source record. A user who opens the master sees the winner. A user who opens the source records still sees both.

A ticket that says "MDM has the old phone" is usually this rank, or a later load that was allowed to overwrite the winner. It is rarely a failed agent. Read the rank before you reload the file. Reloading with a new key makes a third Noah.

### Search

Search does not read the database row by row in front of you. It reads an index of the fields you marked **Searchable**. If you search before the reindex job finishes, you get no rows even though the patient exists. If you search inside Customer 360 for a Patient that was never added to Customer 360, you also get no rows. Both look like "the load failed." Check the import job, then the index, then that you opened the application that actually lists this entity.

### Hierarchy is not match, and it is not a relationship

A **hierarchy** is a tree of "belongs to." Meridian Health sits at the top. Hospital North and Hospital South sit underneath. You use this when the question is "which hospitals are in this system?" You do not match Hospital North and Hospital South. They are not the same place.

A **relationship** links two masters of different kinds. Ava is a patient of Dr Kim Alvarez. Both already exist as masters. The relationship is the link. It is not a column you typed on the Patient form, and it is not a CDI lookup.

A **lookup** in Data Integration is only a dictionary used while a file job runs. `NJ` becomes `New Jersey`, and the result is written to a file. Nothing about that lookup is a golden record.

### Egress

Egress means "out of MDM." After one Ava exists, a job reads masters and writes `patients_golden.csv`. That file should contain one Ava, not two, and Noah's winning phone. Another system can load that file without knowing anything about Epic.

If the egress mapping demands a `jobInstanceId` and you are not running it from the MDM job, stop. Do not type a random id. Use the file-export path the product allows for a one-off read, or let the process start the job.

## 8. Reference 360, and why you closed it

Reference 360 governs small shared lists: country codes, status codes, gender codes. A **reference data set** is the folder. A **code list** inside it holds the values, such as code `F` and name `Female`. A **crosswalk** says "this code in list A equals that code in list B," which you need when two systems use different codes for the same idea.

This is stricter than a CDI lookup. A lookup file is a CSV you can overwrite. A code list can have a draft, an approver, and a published version. People across the company subscribe to it. Changing `AccountStatus` would change whatever live process already uses that list.

Your Explore page listed those live sets and showed no **New** button. You can look. You cannot safely create. You closed it. That is complete for this practice. Do not open `AccountStatus`, `AddressType`, or `BusinessType`.

## 9. The same Ava, every tool, in order

Read this as one chain. The sentence after each step says whether you did it.

1. **Administrator.** The agent is up and the flat-file connection test succeeds. Without this, nothing else can see the CSV. You did this.
2. **Data Integration, first mapping.** Land the North file into a practice folder. You did this.
3. **Expression and router.** `NOT_A_DATE` goes to the error file. Ava's real birth date goes forward. You did this.
4. **Lookup.** `NJ` becomes `New Jersey`. You did this.
5. **Joiner and aggregator.** Ava's three visits, in two hospitals, sit on her row and can be counted. You did this. This answers the business question **in a file**. It does not yet answer it **as one master**.
6. **Taskflow and a schedule that is off.** One published run, not a job that repeats tonight. You did this.
7. **Parameter.** The same mapping can be told `NJ` or `NY` without a second drawing. You did this.
8. **Data Quality.** A reusable rule and a profile, so the check is not buried in one expression. You did this.
9. **Business 360 Console.** Labels `EPIC_NORTH`, `EPIC_SOUTH`, `CRED`, and empty forms for Patient, Provider, and Location, plus pages and a search layout. You did this. No patient row exists.
10. **Business application.** The form is attached to an application a user can open. You correctly refused Customer 360. Multidomain MDM is locked. This step is not done, and it must not be forced.
11. **Ingress, match, merge, survivorship.** North Ava and South Ava become one master. Noah's North phone wins. Both visits remain. This is on paper only.
12. **Egress.** A golden file leaves MDM with one Ava. On paper only.

The file you produced in step 5 can already list Ava's visits. A support ticket will still call that "not MDM." MDM's extra promise is the single trusted person, the memory of both sources, and a rule for which value wins. Step 5 does not keep a cross-reference. Step 11 does.

## 10. Tickets, worked in full

**"The user cannot see MDM."** Ask which tile. Business 360 Console can be open while Multidomain MDM is locked. If the lock is on every admin, it is a license. Do not rebuild Patient. Do not add Patient to Customer 360 so the user "has somewhere to click."

**"The job failed at once and no row was read."** Open the agent. If Data Integration Server is not Up and Running, stop. You are done diagnosing the mapping. If the server is up, test the connection. If the test fails, fix the folder or the runtime. Only if the test succeeds do you open the mapping.

**"The job succeeded and the state name is blank."** The agent is fine. Open the lookup. Check the code, including spaces, and check that the dictionary file on the agent is the one you think it is. Then check the field link into the target. A field that was never connected looks the same as a failed lookup.

**"Ava appears three times as three people, and also we expected three visits."** Decide which output you are looking at. Three visit rows after a joiner can be the correct answer. Three masters after a load means match has not run, or the source primary key changed on each load and created new masters. Do not "fix" the joiner because the MDM screen looks duplicated.

**"I changed the expression and the next run is the same."** Look for an unpublished draft. Publish. Run. Compare the new job id to the old one. If they match, you ran the old task.

**"Search finds nobody after a successful import."** Confirm the import job's success count is not zero. Then see whether a reindex is still running. Then confirm you are searching in the application that lists this entity. Patient was never added to an application here, so an empty search is expected.

**"The golden phone is the South phone."** Open survivorship rank before you reload. Reloading with a new source key creates another Noah. The South number is not lost. It is on the South source record.

**"It worked yesterday."** Ask what changed outside the mapping. An agent restart, an expired password on a connection, or someone else's publish will fail every job that shares that agent or that connection. One mapping "suddenly" failing, while others on the same agent succeed, is much more likely to be that mapping's file, parameter, or a new published version.

For every ticket, write the org, the asset name, and the job id before you change anything. Fix the first error in the session log. Do not create a near-copy asset. Do not test the fix in Production.

## 11. What you finished, and what you only studied

You can already explain and support the file half of the story: agent, connection, mapping, task, cleanse, lookup, joiner, aggregator, taskflow, parameters, data-quality rule, and the MDM **model** in `Test_Pract`. You can also explain match, merge, survivorship, hierarchy, relationship, ingress, and egress, because they are the same Ava you already cleaned. You have not clicked those last ones. Do not click them in Customer 360.

Come back to sessions 9 through 12 only in an org where Multidomain MDM is already orange, and still do not attach practice entities to an application the company is using.

When you reread a session file, come back to section 9 of this page and point at the step number. If you can say what the step changes for Ava, you understand it. If you can only say which button you clicked, read that section again.
