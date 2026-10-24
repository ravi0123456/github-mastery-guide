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

Read this section slowly. It uses the same fake rows you already put in the CSV files. You do not need a second example.

You are supporting this as an administrator. You will not be asked to invent a match rule on a Friday night. You will be asked why search is empty, why two Avas exist, or why the phone is wrong. Those tickets look like infrastructure. Most of them are not. The development knowledge below is how you tell a developer which screen to open, instead of restarting the Secure Agent and closing nothing.

Do not click these steps in Customer 360. Multidomain MDM is locked. This section is the paper version of the job you could not run.

### The morning the ticket is about

Ava Shah goes to the Hackensack hospital on Monday. Epic North stores her as:

```text
source_pk N1001
name      Ava Shah
dob       1984-03-12
gender    F
phone     (201) 555-0101
address   10 River Rd, Hackensack, NJ 07601
mrn       MRN1001
```

On Friday she goes to a different hospital in the same company. Epic South stores her as:

```text
source_pk S2001
name      Ava S Shah
dob       1984-03-12
gender    Female
phone     2015550101
address   10 River Road, Hackensack, NJ 07601
mrn       MRN1001
```

A doctor on Friday cannot see Monday's lab. The computer thinks these are two people. A human can see they are one person: same birth date, same house, same medical-record number, and a first name that was abbreviated.

That is the whole of MDM. Every other word in this section is a piece of that morning.

### Master data is the noun, not the event

A visit, a lab, a payment, and a claim line are events. They pile up. Master data is the noun those events point at.

| Kind | In this story | What you do with it |
|---|---|---|
| Master data | Ava the person, Dr Kim Alvarez, Hackensack hospital | One trusted copy. Match duplicates. |
| Transaction | Ava's Monday visit, Friday visit | Keep them. Hang them off the one Ava. Do not match the visits to each other. |
| Reference data | `F` means Female, `NJ` means New Jersey | A short shared list. Not a person. |

A bank has the same shape. "Priya Rao" is the noun. Her card swipe at 2pm is an event. The bank does not want two Priyas because the savings system and the card system spelled her name differently. The bank still wants every swipe. MDM is that idea, sold as a product.

If a ticket says "merge these two claims," stop. Claims are events. You match the people, the doctors, or the hospitals. You keep the claims.

### Four pieces, and only one of them is the form you built

People say "MDM" and mean four different things. Separate them or every ticket sounds the same.

1. **The model.** The empty form. Patient has first name, last name, birth date, phone, `mrn`. You built this in `Test_Pract`, inside Business 360 Console. An empty form stores no Ava.
2. **The application.** The door a business user walks through to type and search. Customer 360 is one door. Multidomain MDM would have been another. Your Patient form is not behind either door. That is why a user cannot create a patient. The form exists. The door was not opened. You left Customer 360 shut on purpose.
3. **The jobs.** Load, match, and merge. These are what turn the two CSV rows into one Ava. You have not run them.
4. **The trust rule.** When North and South disagree, which value wins. Noah's two phones are this rule. You have not configured it.

An admin ticket almost always names piece 2 or piece 3 and the user thinks piece 1 is broken. Open the right piece.

### What you built, said as the form on the desk

Business 360 Console is the back office. It is not the screen a nurse uses.

**Source system** is a stamp, not a connection. `EPIC_NORTH`, `EPIC_SOUTH`, and `CRED` do not log into Epic. They are the words a future load will write on each row so you can still answer "who sent this?" Creating the stamp reads no file. `CRED` is the stamp for the doctor file, because doctors are not patients and they did not come from Epic.

**Business entity** is the blank form. Patient, Provider, and Location are three forms because a doctor and a hospital are not patients. If you had put hospital name on the Patient form, a search for a person would also return buildings.

Each field has two names. The **display name** is what a person reads, such as Birth Date. The **internal ID** is what jobs store, such as `birth_date`. You saw the canvas say **Invalid** when a display name was empty. That is a form error, not an agent error. Fill the name. Do not restart anything.

**Required** means the nurse cannot save a patient with that box empty. **Searchable** means "include this box in the search index." Turning it on starts a reindex job in the background. A green bar about reindex is normal. Cancelling it is why the next search finds nobody.

**Pages** (Patient Create, Patient View) are the paper layout of that form. **Search layout** is the columns in the result table. They sit in the drawer until an application lists Patient and a role is allowed to open them.

### Why the door matters more than the form

Customer 360, Supplier 360, Product 360, and Multidomain MDM are applications. They sit on the same MDM base. Customer 360 already has Person, Organization, Policy, and Claim, and people at this company use those. Attaching Patient to that list would put Patient on the live New menu. A test Ava would be stored next to real customer masters. You did not attach it. That was correct.

Multidomain MDM is the door for a noun the prebuilt apps do not cover. It is grey and locked. Administrator cannot assign a product the org has not bought. Do not click Enable. Do not click Start trial.

When a user says "I cannot see Patient," the admin checks, in this order:

1. Are they in Business 360 Console, looking at the model, or in an application, looking for a New button? The model will show Patient. The application will not.
2. Is Multidomain MDM grey for every admin? Then it is a license. Stop. Do not rebuild the entity.
3. If the application exists and is orange, is Patient added on its Model tab, and is the user's role on the Create page and the Search layout? A missing role looks exactly like a missing patient.

### Three ids, because the ticket will use the wrong one

Take Ava's North row only.

| What people say | The actual value | Who owns it | Second load of the same North row |
|---|---|---|---|
| MRN | `MRN1001` | The hospital. You stored it as an ordinary field. | Nothing special. A second load can create a second master that also says `MRN1001`. |
| Source primary key | `N1001` | Epic North's own key for this row. | If the load mapped `N1001` to the source primary key, MDM **updates** the same source record. If it mapped `N1001` to a normal field, MDM **inserts** a new master. |
| Business id | a long id you never type | Informatica, after the row exists | This is the master's id. It is not the MRN and not `N1001`. |

South's key is `S2001`, not `N1001`. That is not a bug. Two source systems are allowed to number the same human differently. Match is the step that says both keys belong to one business id. The **cross-reference** is the sentence MDM stores: "this business id came from Epic North `N1001` and from Epic South `S2001`."

The most common load ticket is "we ran it twice and now there are two Avas from North." Ask the developer one question: on the second run, was `source_pk` mapped to the source primary key, and was the value still `N1001`? If they generated `N1001-new` or mapped the column to `mrn`, MDM did what it was told. It created another person. Do not merge those two by hand until you know which key is the practice row.

### Load, walked with the real rows

Load is also called **ingress**. It means "into MDM." It does not mean "figure out they are the same person."

Imagine the North file arrives with four rows: Ava, Noah, Mia, and Bad Date. The Data Integration job should already have sent Bad Date to the error file. MDM should receive three people.

After a correct North load, search shows three masters:

| What a user sees | Why |
|---|---|
| Ava Shah, from `EPIC_NORTH` / `N1001` | One source row became one master, because nobody has matched yet. |
| Noah Patel, from `N1002` | Same. |
| Mia Chen, from `N1003` | She exists only in North. She will never match. That is correct. |

Then the South file is loaded with source system `EPIC_SOUTH`. Search now shows more masters, not fewer:

| New master | Key | Why she is not folded into the North person yet |
|---|---|---|
| Ava S Shah | `S2001` | Load does not compare names. |
| Noah Patel | `S2002` | A second Noah. Match has not run. |
| Leo Nguyen | `S2003` | South only. No pair. |

A ticket opened at this moment that says "MDM duplicated Ava" is early. The load succeeded. Match has not been asked to run. Restarting the agent does not merge them.

The small ingress is a person uploading the CSV in the MDM screen and mapping columns. The production ingress is a Data Integration mapping that uses a Business 360 connection. An Application Integration process often starts that mapping and passes a `jobInstanceId`. If the task demands that id and you are clicking Run yourself, do not type one. A made-up id is a job nobody can trace. Leave Process Server on Restart Required until a real process needs it. Mappings do not use Process Server.

### Match, which is the first time the product agrees with the human

A **match rule** is a sentence the developer writes. For this story the sentence is: birth date is the same, and the name is close.

Compare the pairs yourself.

| Left | Right | Birth date | Name | Should match? |
|---|---|---|---|---|
| Ava Shah `N1001` | Ava S Shah `S2001` | both `1984-03-12` | Ava versus Ava S | Yes. Propose a pair. |
| Noah Patel `N1002` | Noah Patel `S2002` | both `1979-11-02` | exact | Yes. |
| Mia Chen | anyone in South | no | no | No. Leave her alone. |
| Leo Nguyen | anyone in North | no | no | No. |

A **match population** is the method used to compare the text. Person names and company names are not compared the same way. "Ava S" against "Ava" needs person-name logic. Company logic can miss that pair, or it can pair two different companies that share a short word. If a ticket says "obvious duplicates are not pairing," ask which population the rule uses before anyone loosens the rule. Loosening a rule is how two different babies with the same birthday get merged.

A **match job** runs the comparison. It does not always merge. The rule can say "if the score is very high, merge" or "put the pair in a queue." The person who works the queue is a **steward**. The queue exists so a human can refuse a bad pair. An admin does not clear that queue by deleting masters.

### Merge, which keeps the history

Merge turns the pair into one master. It does not delete Monday's row or Friday's row.

After Ava's merge, a user who opens the master sees one person. Underneath, MDM still holds:

```text
business id   (generated, you never type it)
name          Ava Shah
cross-ref     EPIC_NORTH  N1001
cross-ref     EPIC_SOUTH  S2001
```

Both visits can now be listed on that one person. That is the original question, answered. The real hospital's 6.5 million rows became 3.2 million masters for the same reason: the extra rows were duplicates, and the history stayed as cross-references.

Mia stays one master with one cross-reference. Leo stays one master with one cross-reference. They are not failures.

If a third Ava appears after this, someone loaded North or South again and the source primary key was new. Stop. Do not merge at random to tidy the screen. A wrong merge of two real people is harder to undo than a duplicate. Write down the new key and ask.

### Survivorship, which is Noah's phone and not Ava's name

Ava's two phones are the same number written two ways, `(201) 555-0101` and `2015550101`. A cleanse step should have made those identical before load. Noah is the interesting case, because the numbers really differ.

```text
North N1002   201-555-0102
South S2002   9735550199
```

The master has one Current Phone box. It cannot show both and still be one box. **Survivorship** is the rule that picks the winner. A source rank that says Epic North beats Epic South writes `201-555-0102` on the master. `9735550199` is not deleted. It remains on the South source record. Open the master and you see the winner. Open the source records and you see both.

Other rules exist. "Prefer the most recently updated value" is one. "Prefer a value that is not blank" is another. The admin's job on a "wrong phone" ticket is to ask which rule is published, not to reload the file. Reloading with a new source key creates a third Noah. A developer changes the rank. An admin confirms the match-and-merge job that applies the rank actually finished, and that a later load was not allowed to overwrite the winner.

### Search, which lies when the index is behind

The search box does not walk every patient at the moment you type. It reads an index of fields marked Searchable. Three empty searches have three different causes.

| What you see | What it usually is | What you do not do |
|---|---|---|
| Import job says 3 success, search says 0, a reindex bar is still running | The index is late. The rows exist. | Do not import again. |
| Import succeeded yesterday, search in Customer 360 says 0 | You are in the wrong door. Patient was never added to Customer 360. | Do not add Patient to Customer 360 to prove the row. |
| Import job says 0 success | The file, the column mapping, or the source system on the job is wrong. | Do not restart the agent until the session log names the agent. |

### Hierarchy, relationship, and lookup are three different "links"

Use one sentence each, with the people you already know.

**Hierarchy** answers "belongs to." Hackensack Meridian is the parent. Hospital North and Hospital South are the children. You do not match the two hospitals. They are not the same building. A ticket about "the org chart of hospitals" is a hierarchy, not a match rule.

**Relationship** answers "is connected to," across two forms. Ava is a patient of Dr Kim Alvarez. Both are already masters. The relationship is the line between them. It is not a column you type on Ava's form, and it is not a parent-child tree.

**Lookup**, in Data Integration, answers a file job's dictionary question. `NJ` becomes `New Jersey` while the CSV is being written. Nothing about that lookup is stored as a golden person. If a developer says "we looked up the patient in MDM" they mean a different transformation, one that calls MDM. The state-file lookup you built does not.

### Egress, the file that leaves

After one Ava exists, some other system still wants a file. **Egress** is "out of MDM." The file `patients_golden.csv` should contain one Ava, not two, and Noah's winning phone `201-555-0102`. The other system never learns about Epic.

If that mapping asks for a `jobInstanceId` and you are not starting it from the MDM job, stop. Do not invent the id. The process creates it so the run can be found later.

### What you check, and what you hand to a developer

Keep this list. It is the admin's half of MDM support.

| The user says | You check | You hand to a developer when |
|---|---|---|
| I cannot open the MDM tile | My Services. Grey lock for every admin means license. | The tile is orange for you and locked for one user. That is a role or an assigned service. |
| I cannot create a Patient | Which application they opened. Patient is in `Test_Pract` only. | The right application is open and the Create page is not assigned to their role. |
| The load job failed before any row | The ingress job's session log, then the Business 360 connection test, then Data Integration Server. | The log names a column, a source primary key, or a validation error on the entity. |
| We have two Avas | Has match been run? Are both keys from North, or one North and one South? | One North and one South, and match did not propose them. That is the rule or the population. Two North keys means the source primary key mapping. |
| The phone on the golden record is wrong | Which source rank is published, and whether a later load overwrote it. | The rank itself is wrong. Do not reload. |
| Search is empty | Reindex still running, wrong application, or the import success count is 0. | The field is not marked Searchable, or the search layout does not include it. |

You do not fix a match rule by restarting the Secure Agent. You do not fix a stopped Data Integration Server by editing the match rule. That split is the development knowledge an administrator uses.

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
