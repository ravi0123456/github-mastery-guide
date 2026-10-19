# Meridian practice path

Study the Hackensack Meridian Health story by **doing one session in an IDMC org**, then stopping. Do not read ahead and do not run two sessions in one sitting.

The session files are the buttons. [15-concepts-admin-developer.md](15-concepts-admin-developer.md) is the meaning: what each object is, why a ticket fails, and what an administrator checks before a developer changes a mapping. Read the matching chapter in that file before you treat a session as understood.

You have admin access. Sessions 1–7 stay in the org where you already created them, even if that org has no Business 360. Sessions 8–11 start in the other org, the one whose service list shows **Business 360**. Do not export the CDI project into that org.

The full labs live here:

https://github.com/ravi0123456/github-mastery-guide/tree/main/idmc-meridian

## Do them in this order

| Order | File | One sitting | You stop when |
|---|---|---|---|
| 0 | [00-start-here.md](00-start-here.md) | 30 min | You picked a **non-production** org and saved the five practice files |
| 1 | [01-admin-runtime-connection.md](01-admin-runtime-connection.md) | 45 min | Flat file connection test is **Successful** |
| 2 | [02-first-job.md](02-first-job.md) | 60 min | Monitor shows a **Success** job and a target file with rows |
| 3 | [03-cleanse-and-bad-rows.md](03-cleanse-and-bad-rows.md) | 70 min | Bad date row is in the error file, good rows are cleaned |
| 4 | [04-lookup-joiner-aggregator.md](04-lookup-joiner-aggregator.md) | 80 min | Ava Shah shows **3** encounters across **2** hospitals |
| 5 | [05-taskflow-schedule-the-job.md](05-taskflow-schedule-the-job.md) | 60 min | One taskflow run is green, schedule is **not** left running |
| 6 | [06-parameters.md](06-parameters.md) | 45 min | Same mapping writes NJ rows or NY rows based on a parameter |
| 7 | [07-data-quality.md](07-data-quality.md) | 60 min | You have a profile (or you wrote NOT LICENSED and moved on) |
| 8 | [08-mdm-model.md](08-mdm-model.md) | 70 min | Patient, Provider, and Location exist in `Test_Pract`. Customer 360 was not changed. Reference 360 was closed because Explore has no **New**. |
| 9 | [09-mdm-load-and-search.md](09-mdm-load-and-search.md) | closed | Multidomain MDM is locked. Do not start. |
| 10 | [10-match-merge-survivorship.md](10-match-merge-survivorship.md) | closed | Same license gap. Read the concept. Do not click. |
| 11 | [11-hierarchy-relationship.md](11-hierarchy-relationship.md) | closed | Same license gap. Read the concept. Do not click. |
| 12 | [12-ingress-egress-jobs.md](12-ingress-egress-jobs.md) | closed | Same license gap. Read the concept. Do not click. |
| 13 | [13-other-idmc-services.md](13-other-idmc-services.md) | one service only | You practiced **one** extra service, or skipped it on purpose |
| 14 | [14-do-not-mix-with-work-cicd.md](14-do-not-mix-with-work-cicd.md) | 20 min | You can say what this practice is **not** allowed to touch |
| 15 | [15-concepts-admin-developer.md](15-concepts-admin-developer.md) | read with each session | You can explain the object in the ticket without opening the designer |

## Rules

1. One file per sitting. If a job is still running, wait. Do not start the next file.
2. CDI org project: `MERIDIAN_PRACTICE`. MDM org project: `Test_Pract`. Do not create a second project in the MDM org.
3. Sessions 1–7: the org that already has the jobs. Sessions 8–11: the org that has **Business 360**. Never Production. Do not copy the jobs across.
4. Data: only the fake rows in session 00. No real patient, member, or employee data.
5. Do not tag these assets. Do not edit `flo_dev.yml` or any work YAML.
6. If Business 360 is missing in the job org, do not write `NOT LICENSED` and skip sessions 8–11. Log into the org that has it and start [08-mdm-model.md](08-mdm-model.md). Skip a session only when **no** org you admin has that service.
7. Button names move between releases. Each step says the **goal** first. If the button is not where the text says, use the top search and type the asset name.

## Where this sits next to the other notes

- Git and the work deploy path: [06-idmc-cicd-picture.md](../06-idmc-cicd-picture.md) then [07-idmc-dev-to-sit-practice.md](../07-idmc-dev-to-sit-practice.md). Do those **after** session 14, not before.
- The admin prompt in [hello-world/idmc](https://github.com/ravi0123456/hello-world/tree/main/idmc) is for reconstructing the **work** platform. It is not this practice.
