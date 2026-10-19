# Concepts — administrator and developer

Read the chapter that matches the session you just clicked. The session file is the buttons. This file is what those buttons mean, and what you check when a ticket arrives. You do not need another guide.

Informatica's own manuals are the source for a word this page does not cover. Use the current-version books, not a blog.

- Administrator: organization, users, licenses, runtime, connections. [Administrator](https://docs.informatica.com/cloud-common-services/administrator/current-version.html)
- Developer, batch: mappings, tasks, taskflows. [Cloud Data Integration](https://docs.informatica.com/integration-cloud/cloud-data-integration/current-version.html)
- Developer, rules: [Data Quality](https://docs.informatica.com/data-governance-and-quality-cloud/data-quality/current-version.html)
- Developer, master data: [Business 360 Console](https://docs.informatica.com/master-data-management-cloud/business-360-console/current-version.html)
- Proof a job ran: [Monitor](https://docs.informatica.com/cloud-common-services/monitor/current-version.html)

## Two jobs, one platform

An **administrator** decides who may enter, which product is licensed, where a job is allowed to run, and whether a connection can see a folder. An admin does not fix a wrong expression by guessing. An admin proves the runtime is up, the user has the service, and the connection test succeeds.

A **developer** draws the work: the mapping, the rule, the business entity, the match rule. A developer does not fix a stopped agent by editing the mapping. If the agent is down, every mapping on that agent fails the same way.

Most tickets are one of those two. Read the error before you pick a hat.

| The ticket says | Hat | First check |
|---|---|---|
| User cannot see a tile, or the tile is grey with a lock | Admin | License, then **Administrator → Users → Assign Services**, then roles |
| Job failed before any row was read | Admin | Runtime status, connection test, file path on the agent machine |
| Job succeeded and the rows are wrong | Developer | Mapping logic, parameters, lookup file, joiner keys |
| Job succeeded, MDM search is empty | Developer, then admin | Import really committed, then the search index, then you are in the right application |
| "It worked yesterday" | Admin first | Did the agent restart, did a connection password expire, did someone publish a new version |

**Save** stores your draft. **Publish** makes that version the one a job can run. A taskflow that is saved and not published does not run the picture you are looking at. The same is true of an Application Integration process.

## Words that appear in every ticket

| Word | Plain meaning |
|---|---|
| Org | One Informatica customer space. Licenses, users, and assets belong to an org. This practice used two orgs on purpose. Jobs were not copied between them. |
| Service | A product tile: Data Integration, Data Quality, Business 360, Customer 360, Reference 360. A license can exist and a user can still be locked out if the service is not assigned to that user. |
| Asset | Anything you can name and save: a mapping, a connection, a business entity, a code list. |
| Project | A folder of assets. CDI practice project: `MERIDIAN_PRACTICE`. MDM practice project: `Test_Pract`. |
| Runtime / Secure Agent | A small program on a server you control. Cloud Informatica sends the job there. The job reads files **on that server**, not on your laptop. |
| Connection | The named path the agent uses. A flat-file connection is a folder. A failed connection test means the agent cannot see that folder. |
| Mapping | A drawing. It does not run. |
| Mapping task | The job. It binds the drawing to one runtime and one connection. |
| Taskflow | A job made of jobs, with a success path and a fail path. |
| Schedule | A clock on a task or taskflow. Leave practice schedules off. |
| Monitor | The page that proves a run. Success, warning, and failed are different. Read the session log before you edit the mapping. |
| Source system | In MDM, the name of an upstream application, such as `EPIC_NORTH`. It is not a Secure Agent. |
| Business entity | The master-data shape, such as Patient. Fields live on the entity. |
| Master / golden record | The one trusted row MDM keeps after match. Source rows stay underneath it. |
| Cross-reference | The link from a source primary key (`N1001` in Epic North) to that master. |
| `mrn` | A hospital's own medical-record number. It is a field you created. It is not Informatica's internal id. |

## What you already built

| You created | What it actually is |
|---|---|
| Flat-file connection and a successful test | The agent can read the practice folder. Later file jobs depend on this, not on your laptop. |
| `m_land_north` and a mapping task | A drawing plus the job that runs it. Monitor is the proof. |
| Cleanse and an error file | Good rows go forward. `NOT_A_DATE` is rejected instead of becoming a patient. |
| Lookup, joiner, aggregator | Lookup adds a state name. Joiner puts encounters next to a patient. Aggregator counts them. Ava has 3 encounters in 2 hospitals. |
| Taskflow, then the schedule turned off | One run can call the job and branch on failure. A schedule left on would run it again tonight. |
| Parameters | One mapping, two runs. An input parameter is a value you pass in, such as `NJ`. An in-out parameter is a value the mapping can update and pass on. |
| Data quality rule and a profile | A reusable check and a picture of the data. A score is not the same as a reject file. |
| `EPIC_NORTH`, `EPIC_SOUTH`, `CRED` | Three source-system names. No patient rows were loaded. |
| Patient, Provider, Location | Three shapes. Patient has the fields a later import would fill. |
| Patient Create, Patient View, Patient Search | Screens. They do nothing until an application lists Patient. This org's only open application is Customer 360, which you correctly did not change. |

## Administrator, the parts a ticket will ask for

**Licenses.** A grey tile with no lock usually means the org has not bought that product. A grey tile with a lock can mean the org bought it and this user was not given the service or the role. You confirmed Multidomain MDM cannot be assigned. That is a missing product, not a mistake in the Patient entity. Do not click **Enable** or **Start trial**. That can bill the org.

**Users and roles.** A role is a bundle of privileges. Admin can design. A business user can search and edit rows if a page is assigned to that role. Assigning every role "so the button appears" is how a practice screen shows up for the whole company. You did not do that.

**Runtime.** In **Administrator → Runtime Environments**, open the agent. **Data Integration Server** must say **Up and Running** or mappings sit in the queue. **Process Server** is for Application Integration. **Restart Required** on Process Server does not stop Data Integration. Do not restart an agent because a mapping failed validation. Restart it when the service is down or Informatica told you a restart is required.

**Connections.** Test the connection from the agent, not from your laptop. If the test fails, the folder path is wrong, the agent service account cannot read it, or you picked a different runtime than the one that holds the files.

**Publishing and migration.** Assets move by export and import, or by source control, between orgs. Copying a project into a production org, or into the org that feeds a Jenkins pipeline, is out of scope for this practice. Session 14 says what not to touch.

**Logs.** The session log on the Monitor job page is the first artifact you attach to a ticket. A screenshot of the mapping canvas is not a log.

## Developer, the parts a ticket will ask for

**A mapping reads left to right.** Source, then transformations, then target. Each transformation has input fields and output fields. If a field is missing on the next box, it was not connected, or the name changed.

**Expression.** A formula per row. `UPPER(first_name)` does not look at other rows. A bad date fails here or in a later date port. Send the bad row to an error target. Do not delete it in silence.

**Lookup.** A small file or table used as a dictionary. The incoming row carries `NJ`. The lookup returns `New Jersey`. If the lookup file is not on the agent, the transformation warns or returns null. A null state name with a successful job usually means the lookup key did not match, not that the agent is down.

**Joiner.** Two streams become one. The join condition is the key, such as patient id. A normal join drops rows that exist on only one side. A master outer join keeps the patient even when no encounter matched. Duplicate rows after a joiner usually mean the key was not unique.

**Aggregator.** Collapses many rows into one, such as a count of encounters. Group by the patient. If you forget the group-by, you get one row for the whole file.

**Filter and router.** A filter keeps rows. A router sends rows down more than one path. Bad dates belong on the reject path.

**Parameters.** An **input** parameter is set by the task or the caller. The mapping only reads it. An **in-out** parameter can be changed by the mapping and read by the next step in a taskflow. If a task asks for a value and you leave it blank, the job fails at start or writes the wrong file. That failure is configuration, not a broken agent.

**Mapping task versus mapping.** Editing the mapping does not change a job that already ran. The next run uses the published mapping the task points at. If you saved and did not refresh the task, you can run the old picture.

**Data quality.** A rule specification is a named check, reusable in more than one mapping. A profile counts nulls, patterns, and values. It does not fix rows. A failed rule test is a rule problem. A profile that will not run is often a missing Data Quality license or a runtime that has no Data Integration Server.

## MDM, including the part you could not click

You built the model. You could not load rows because **Multidomain MDM** is not licensed, and **Customer 360** already holds Person, Policy, and Claim for this company. Adding Patient there would put a practice record in the live customer master. Do not do it. The ideas below are what sessions 9 through 12 would have shown.

**Source record and master.** Epic North sends Ava as `N1001`. Epic South sends the same person as `S2001` with the name `Ava S`. Until match runs, MDM treats those as two masters. That is correct. Match is a separate decision.

**Source primary key.** `N1001` is the key inside Epic North. Mapped to the source primary key, a second import updates that row. Mapped to an ordinary field, a second import creates another master. This is the most common "we loaded it twice" ticket.

**Match.** A match rule says which fields must agree. Same birth date plus a similar name can propose Ava North and Ava South as one person. A match **population** is the comparison method. A match **job** does the comparison. It does not by itself merge unless the rule says to auto-merge. A steward can review a pair first.

**Merge.** Merge collapses the pair into one master. Both source keys remain as cross-references. You can still see that Epic North and Epic South contributed. History did not disappear. That is the 6.5 million rows becoming 3.2 million masters.

**Survivorship.** After the merge, which phone number wins? A source-system rank can say Epic North beats Epic South, so Noah keeps `2015550102` and the South phone `9735550199` stays on the source row. A ticket that says "the golden record has the old phone" is usually a survivorship rank, not a failed load.

**Search.** Search uses an index of fields you marked **Searchable**. Turning that flag on starts a reindex job. Searching before it finishes returns nothing even though the row exists. Searching in the wrong application also returns nothing.

**Hierarchy.** A system node, then two hospitals under it. A hierarchy is "belongs to", not "is the same person". Do not use match to model a hospital tree.

**Relationship.** Patient–provider is a link between two masters, such as Ava and Dr Kim Alvarez. It is not a lookup in a CDI mapping, and it is not a hierarchy.

**Ingress and egress.** Ingress is data entering MDM. The small version is a file import. The production version is a Data Integration mapping, often started by an Application Integration process that passes a `jobInstanceId`. Egress is masters leaving MDM, for example a `patients_golden.csv` with one Ava. If the connector refuses to run without `jobInstanceId`, do not type a random id. The process is supposed to supply it.

**Business application.** Customer 360, Supplier 360, Product 360, and Multidomain MDM are applications on the same MDM base. An entity that is not added to an application has no **New** menu and no search for a business user. Pages and search layouts are assigned to roles. Customer 360 on this org is already in use. Leave it.

**Reference 360.** A code list is a governed dictionary, such as country codes. A reference data set is the folder that holds code lists. A crosswalk maps one list's codes onto another list. Explore on this org shows live lists (`AccountStatus`, `AddressType`, and the rest) and no **New** button. You can look. You cannot safely create. Close it. Those lists are not practice data.

## How to work a ticket

1. Write the org name, the asset name, and the job id. A screenshot without those three is not a ticket.
2. Decide the hat. Tile locked, agent down, connection failed: administrator. Wrong rows, wrong join, wrong survivorship: developer.
3. Open Monitor and read the session log to the first error. Fix that error. The later errors are often the same root.
4. Do not create a second asset with a near name (`Patient1`, `m_land_north_new`) to "try something". You will forget which one the task runs.
5. Do not test a fix in Production. Do not point a practice job at a shared drop zone.
6. After the fix, run once. Attach the new job id and the counts (success rows, rejected rows) to the ticket.

## What stays closed

Sessions 9, 10, 11, and 12 stay closed in this org. Read them here. Do not click them. Come back only in an org where **Multidomain MDM** is already orange, and still do not use a live Customer 360 application as the practice target.
