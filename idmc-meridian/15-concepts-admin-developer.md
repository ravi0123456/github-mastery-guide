# Concepts — the whole path, in plain English

Read this file from the top. It is written for someone who has never used Informatica MDM. The numbered session files are the buttons you click. This file is why those buttons exist, how they connect, and what you say when a ticket arrives. Section 7 is the full MDM lesson in simple English. Read that section. You do not need the video.

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

Read this section instead of the video. It is the same lesson, in simple English, using the same fake person. Ava Shah is typed once at Epic North and again at Epic South. The rest of this section is what those two rows mean.

You have not loaded the CSV into MDM. Multidomain MDM is locked, so do not click load, match, or merge. Read this so that, when a ticket arrives, you know which screen is actually broken.

### What a source system actually contains

You already created three source systems. Open one of them. This is the whole thing you saved. There is no second page and no hidden table.

```text
name           EPIC_NORTH
description    blank
```

The other two are the same kind of card:

```text
name           EPIC_SOUTH
name           CRED
```

Count what is on the card. One name. No first name, no phone, no birth date, no MRN, and no CSV row. Ava is not inside `EPIC_NORTH`. She was never stored there. If the screen looks empty apart from the name, that is correct.

These three cards are only an allowed list of hospital names. Like a dropdown that offers three choices. A dropdown of hospital names does not contain the patients.

The CSV from the earlier file jobs is still a file. It belongs to the Data Integration practice. It was not copied into these cards. You are not going to run an MDM load on this org, because Multidomain MDM is locked. Do not try to force one. The picture below is what you study, not what you click.

### What you have now, before any MDM load

This is your screen today.

| What you open | What is inside it |
|---|---|
| Source system `EPIC_NORTH` | the name `EPIC_NORTH` only |
| Source system `EPIC_SOUTH` | the name `EPIC_SOUTH` only |
| Source system `CRED` | the name `CRED` only |
| Patient form | empty boxes only: `first_name`, `last_name`, `birth_date`, `gender`, `phone`, `address`, `city`, `state`, `postal_code`, `mrn`. Zero people. |
| Search for Shah | nobody. That is correct. |

The North file, which is **not** inside MDM, looks like this. It stays a file.

```text
source_pk   first_name   last_name   birth_date    phone
N1001       Ava          Shah        1984-03-12    201-555-0101
N1002       Noah         Patel       1979-11-02    201-555-0102
N1003       Mia          Chen        1991-07-19    2015550103
```

A bad row with birth date `NOT_A_DATE` must never be in this file when it reaches MDM. That row was already rejected by the file job.

### What a load would add, and what it would not touch

You will not run this load. Read it so a future ticket makes sense.

After a North load, the three source-system cards are **unchanged**. Open `EPIC_NORTH` and it is still only the name. Ava is still not inside it.

The new rows appear under **Patient**, not under the source system:

```text
first_name       Ava
last_name        Shah
birth_date       1984-03-12
phone            201-555-0101
mrn              MRN1001
source system    EPIC_NORTH
source key       N1001
```

Noah and Mia would be two more Patient rows, also labelled `EPIC_NORTH`, with keys `N1002` and `N1003`. Search would show 3 people. The source-system list would still show 3 names.

The words `EPIC_NORTH` then exist in two places. They are not the same object.

| Place | What `EPIC_NORTH` is there | Does Ava live here? |
|---|---|---|
| The source-system card you created | the allowed name itself | No. Never. |
| One box on Ava's Patient row | a copy of that name, so you can see who sent her | Yes. This row is Ava. |

A South load would add three more Patient rows. It would not add rows into the source-system card.

```text
first_name       Ava S
last_name        Shah
birth_date       1984-03-12
phone            201-555-0101
source system    EPIC_SOUTH
source key       S2001
```

Search would show 6 people. Still only 3 name cards if you open Source Systems. Six is correct, because load does not merge.

`CRED` is the name card for a doctor file, not for Ava. A doctor would be a Provider row with source system `CRED`. You have not loaded doctors. The `CRED` card is still only the name `CRED`.

North can call her `N1001` and South can call her `S2001`. The copied name on the Patient row is how MDM tells those keys apart. A later North file that uses `N1001` again updates Ava's North row. It does not update the South row, and it does not type her into the source-system card.

### One field, two names, one value

This is not two pieces of data. Ava does not get two birth dates because the field has two names.

When you added the date field, Properties showed two boxes. The steps told you to type the same word in both, so on your screen they look identical:

```text
Display Name     birth_date
Internal ID      birth_date
```

Same word. Two different settings. One box. The titles of the boxes are what matter, not the fact that you typed `birth_date` in both.

Use a phone contact, not a shop.

You save a doctor on your phone:

```text
Name on the screen     Dr Kim
Number the phone dials 201-555-0140
```

You can rename the contact to `Dr Kim Alvarez`. The next call still goes to `201-555-0140`, because the phone dials the number. It does not dial the name.

If someone edits the number and leaves the name as `Dr Kim`, you call the wrong person. The screen still looks right.

| On the phone | On the Patient field | What it is |
|---|---|---|
| Name on the screen, `Dr Kim` | Display Name, `birth_date` | Words a person reads. Safe to change. |
| Number, `201-555-0140` | Internal ID, `birth_date` | Code the machine uses. Do not change it after a job exists. |
| The call itself | The date `1984-03-12` | The value. One value. You have none yet. |

Your two boxes both say `birth_date` for the same reason a contact could be saved with the name `201-555-0140`. The name and the number would look alike, but the phone would still dial the number and show the name. Two settings. One contact.

### What you already clicked

1. You dropped a Date box on the Patient canvas. Display Name was empty.
2. The header turned **Invalid**. Properties said **Enter a value.**
3. That sentence means "this box has no label." It does not mean the agent stopped. It does not mean a birth date was deleted. No patient is in the form.
4. You typed `birth_date` in Display Name. The red mark went away.
5. Internal ID became `birth_date` too, because you typed it or the screen copied the label.
6. You clicked **Save**. The header said **Valid**.

If you see **Invalid** and **Enter a value** again, click the box with the empty Display Name, type `birth_date` or `Birth Date`, and save. Do not restart the agent. The agent runs jobs. This message is only a missing label.

### What a person sees, and what a job uses

You are not loading patients. Read this as the screen a ticket will describe.

Today the box is an empty line under a label:

```text
birth_date
[ empty ]
```

The word above the line is the Display Name. The empty line is the value.

After a North load, that same box would show one date:

```text
birth_date
[ 1984-03-12 ]
```

The job does not read the word above the line. In the mapping, a line is drawn from the CSV column `dob` to the code `birth_date`. That code is the Internal ID.

Now change only the label, from `birth_date` to `Birth Date`, and save. The clerk sees:

```text
Birth Date
[ 1984-03-12 ]
```

The mapping still points at `birth_date`. The date still arrives. This change is safe. It is the same as renaming `Dr Kim` to `Dr Kim Alvarez`.

Now the unsafe change. Someone renames the Internal ID from `birth_date` to `date_of_birth` and leaves the label as `Birth Date`. The old mapping still looks for `birth_date`. That code is gone. The job can say Success, and the box stays empty:

```text
Birth Date
[ empty ]
```

The screen looks like a normal form, so this ticket is easy to misread. The label is fine. The code the job dials was changed. Put the Internal ID back to `birth_date`, or point the mapping at the new code. Do not restart the agent. Do not load Ava again under a new key. A new key would create a second Ava, and her date would still be empty.

Every other box is the same. The phone label can say `phone`. The code is also `phone`. The value, after a load, would be one number, `(201) 555-0101`, not two numbers.

### The model is the empty form those values would fill

The product name for the form is **business entity**. Yours is Patient. Provider and Location are two other empty forms, because a doctor and a hospital are not patients.

Today the form has the boxes and no values. You draw the boxes once in Business 360 Console. You do not open the form again to type every patient. A job would fill the values. You are not running that job here.

**Required** means a person cannot save a patient with that box empty. **Searchable** means "put this box in the search index." Turning it on starts a reindex in the background. A bar that says reindex is running is normal. Cancelling it is why the next search finds nobody.

### When you use the UI, a job, or a script

These three do not do the same job. Mixing them up is why people retype a thousand patients by hand, or restart an agent that was never the problem.

| You want to | Use this | Do not use this |
|---|---|---|
| Draw the Patient form the first time, or add a field | The UI, in Business 360 Console | A job. A job cannot invent a new field. |
| Fix one record, or confirm one match pair | The UI | A new load with a new key. That creates another person. |
| Bring in a file of hundreds or thousands of rows | A job: ingress, or a mapping | The UI. Do not open every patient and type. |
| Run that same load from a schedule or another system | A script or a process. It sends data into the form that already exists. | A script that tries to redraw the form |

A script does the same work as a job. It does not draw the form again. It sends data into the model that is already published.

The **application** is a different thing from the form. It is the door a business user walks through to search and type. Customer 360 is one door. Multidomain MDM would have been another door. Your Patient form is not behind either door. That is why a user cannot create a Patient. The form exists. The door was not opened. You left Customer 360 shut on purpose, so a test Ava is not stored next to real customers.

### Changing the form is not the same as filling data

Suppose you need a new field, blood group. You add that field once on the model screen, and you publish. Only the shape of the form changes.

Old records still have blood group empty. You do not open every patient in the UI and type it. The next job sends that column from the CSV, and those rows get filled. Until that job runs, empty is correct.

### Save and publish

**Save** means a draft. You can see the draft. A running job cannot.

**Publish** means "this is the version the job will follow."

This is why a ticket says "I fixed it and it is still wrong." The picture on the screen is the draft. The job is still using the last published version. Publish, then run once, then read the new job in Monitor.

Do not publish the practice form into live Customer 360. Real users are already on that door. Do not click **Enable** or **Start trial** on Multidomain MDM. Those buttons can start a bill, and the product is not on this org.

### How one CSV row travels

The North file has Ava, Noah, and Mia. A bad date row should already have been rejected by the Data Integration job. MDM should receive three people.

1. The job reads the file through a connection. In the mapping you say: this column is the name, this column is the birth date, this column is the source key.
2. If you map that key as the **source primary key**, the same file can come again and it updates Ava. If you map it as a normal field, every run creates a new person.
3. When the row comes in, the name `EPIC_NORTH` is stamped on it. Now it is a **source record**. It is the proof of what North typed.
4. It is not yet the **master**. The master is the one answer the company trusts. That comes only after match and merge.

The small way to load is a person uploading the CSV on the MDM screen and mapping columns. The production way is a Data Integration mapping that uses a Business 360 connection. An Application Integration process often starts that mapping and passes a job id. If the task demands that id and you are clicking Run yourself, do not invent one. A made-up id is a job nobody can trace. Leave Process Server on Restart Required until a real process needs it. Mappings do not use Process Server.

### The count goes from 3 to 6

After a correct North load, search shows three masters: Ava Shah from `N1001`, Noah Patel from `N1002`, and Mia Chen from `N1003`. Mia exists only in North. She will never have a pair. That is correct.

Then the South file is loaded with the stamp `EPIC_SOUTH`. It has Ava again (`S2001`), Noah again (`S2002`), and Leo (`S2003`). Search now shows six masters, not three.

Six is the correct number. Load does not merge people. Load only adds rows. A ticket that says "MDM duplicated Ava" at this moment is early. The load succeeded. Match has not been asked to run. Restarting the agent does not merge them.

Leo has no pair. Never merge Mia or Leo into someone else.

### Match, then merge

Match asks one question: are these two the same person?

The rule for this story is simple. Same birth date, and the name is close. Then propose a pair. Do not glue them immediately. A person can confirm the pair in a queue. That person is called a **steward**. An admin does not clear that queue by deleting masters.

| Left | Right | Should they be a pair? |
|---|---|---|
| Ava Shah `N1001`, born 1984-03-12 | Ava S Shah `S2001`, same date | Yes. Propose the pair. |
| Noah Patel `N1002`, born 1979-11-02 | Noah Patel `S2002`, same date | Yes, even though the phones differ. The phone is decided later. |
| Mia Chen | Anyone in South | No. Leave her. |
| Leo Nguyen | Anyone in North | No. Leave him. |

A **match population** is the method used to compare the text. Person names and company names are not compared the same way. "Ava S" against "Ava" needs person-name logic. If a ticket says "obvious duplicates are not pairing," ask which population the rule uses before anyone loosens the rule. Loosening a rule is how two different babies born on the same day get merged.

Merge means the two cards become one card. It does not delete Monday's row or Friday's row.

After Ava's merge, one person is left, and both keys are still attached:

```text
business id    created by the product, you never type it
name           Ava Shah
cross-ref      EPIC_NORTH   N1001
cross-ref      EPIC_SOUTH   S2001
```

Monday's visit stays. Friday's visit stays. A doctor can see Monday's lab because both charts now point to one person.

The count goes from six to four: Ava, Noah, Mia, and Leo. If you see five, a single person was merged by mistake. If you see two, too many people were merged.

### Which phone is shown, and the three ids

Survivorship is a different question from match. Look at Noah.

```text
North N1002   201-555-0102
South S2002   973-555-0199
```

The master has one phone box. It cannot show both and still be one box. Survivorship picks the winner. If North's rank is higher, the golden record shows `201-555-0102`. The South number is not deleted. It stays on the South source row. Open the master and you see the winner. Open the source records and you see both.

Ava's two phones are the same number written two ways, `(201) 555-0101` and `2015550101`. A cleanse step should have made those identical before load. Noah is the real disagreement.

A ticket that says the phone is wrong is usually this rank, or a later file overwrote the winner. Do not load Noah again with a new key. A new key creates a third Noah.

The word **id** is used for three different numbers. Do not mix them.

| Name | Example | What it is |
|---|---|---|
| MRN | `MRN1001` | A field a clerk typed. It is only a clue. A second load can create another master that also says `MRN1001`. |
| Source key | `N1001` and `S2001` | The hospital's own key. Map this as the source primary key if a second file should update the same row. `S2001` is not a spelling mistake of `N1001`. |
| Business id | a long id you never type | Created by the product after the row exists. After merge, both hospital keys point to this one id. |

### Three kinds of link

These are not duplicates, and they are not the same as each other. The official Customer 360 course (October 2025 developer guide) uses four designer assets. [16-customer360-developer-course.md](16-customer360-developer-course.md) is the full map. The short version is here.

**Business entity** is the form. In Customer 360 the product already gives you **Person** and **Organization**. Patient in `Test_Pract` is a custom form you drew. The **data model** is the whole set: those forms, their fields, the reference lists, the relationships, the match rules, and the survivorship ranks. One form is not the whole model.

**Relationship** is a named line between two forms, or from a form back to itself. Official examples on Organization are Employs, Contact, and Subsidiary. In your story the line is Patient treated by Provider. You design the line in Business 360 Console. A user later attaches two real records to that line in the front-end app. Do not merge the patient and the doctor.

**Hierarchy model** is the tree shape, designed in Business 360 Console. It has one top business entity, other entities under it, and hierarchy relationships that say who can be the parent. Organization on top and Person underneath is the course example. Your hospital tree is the same idea: the health system is the parent, Hackensack and Jersey City are children. Hospital North and Hospital South are not duplicates.

**Hierarchy instance** is one real tree of records, created in the front-end app (Customer 360), not on the designer canvas. The model says "a hospital can sit under a health system." The instance says "Hackensack sits under Meridian Practice Health." You do not create that instance in live Customer 360.

**Reference data** is a small allowed list, such as Female or Active. The course calls this the picklist. You do not merge that word. If the file says `F` and the list only has `Female`, the row fails validation. It is not a duplicate person. In Data Integration, the state lookup you already built turns `NJ` into `New Jersey` while the CSV is being written. That lookup does not create a golden person. Do not edit a shared list, such as `AccountStatus`, just to practice. People across the company already use those lists.

### Search, cleanse, and data leaving

Search does not walk every patient at the moment you type. It reads a copy called the **index**, built from fields marked Searchable. The copy can be late. The load can succeed, and for a short time search still does not show Ava.

| What you see | What it usually is | What you do not do |
|---|---|---|
| The job says 3 success, search says 0, and a reindex bar is still running | The index is late. The rows exist. | Do not import again. |
| The job succeeded yesterday, and search in Customer 360 says 0 | You are in the wrong door. Patient was never added to Customer 360. | Do not add Patient to Customer 360 to prove the row. |
| The job says 0 success | The file, the column mapping, or the source system on the job is wrong. | Do not restart the agent until the session log names the agent. |

**Ingress** means the file coming in. **Cleanse** means removing brackets from the phone and making the date one format. Cleanse does not merge people. **Egress** means sending data out. Send the golden Ava to billing, with one row and the winning phone. If you send only Friday's source row, Monday's lab will not go with her.

### How an admin reads a ticket

Follow this order. Do not jump to the match rule.

1. Did the job start? Check the Secure Agent, the connection, and, only if a process is involved, the process server. If the job did not start, the duplicates you see are old ones.
2. Did the rows come in? Read the success count. Zero means the file, the connection, or the mapping. It is not the match rule.
3. If match has not run yet, two Avas is normal. Write that in the ticket.
4. If match already ran and they are still two, compare the birth date and the name. An empty date is not equal to another empty date. Then ask which match population the rule uses.
5. One person, but the phone is wrong. Open both source rows. The rank, or a later load, picked the loser. Do not create a new key.
6. The log says the rows are there, but search says no. Then it is the index, or you are in the wrong application.

Three things you should not do.

- Do not restart the agent because a rule is wrong.
- Do not change the rule because the agent is down.
- Do not reload the file with a new key just to fix a phone.

| The user says | You check | Hand it to a developer when |
|---|---|---|
| I cannot open the MDM tile | My Services. A grey lock for every admin means the license. | The tile is open for you and locked for one user. That is a role or an assigned service. |
| I cannot create a Patient | Which application they opened. Patient is in `Test_Pract` only. | The right application is open and the Create page is not assigned to their role. |
| The load failed before any row | The session log, then the Business 360 connection test, then Data Integration Server. | The log names a column, a source primary key, or a validation error on the entity. |
| We have two Avas | Has match been run? Are both keys from North, or one from each hospital? | One North and one South, and match did not propose them. Two North keys means the source primary key was not mapped. |
| The phone on the golden record is wrong | Which source rank is published, and whether a later load overwrote it. | The rank itself is wrong. Do not reload. |
| Search is empty | Reindex still running, wrong application, or the success count is 0. | The field is not marked Searchable, or the search layout does not include it. |

### What you already built

You built the model only. An empty Patient form, plus Provider and Location, and three source system names. The CSV data is not inside those names. The Multidomain door is locked. Live Customer 360 was not changed.

Say this once, in your own words. A source system is a stamp, not the data. A model is the shape, not the data. A job brings the data. Load increases the count. Match proposes the pair. Merge keeps one person and both keys. Survivorship chooses which value to show.

That is MDM. As an admin, first ask, did the job run? Only then ask, is the rule wrong?

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
