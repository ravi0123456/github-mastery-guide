# Meridian practice path

Study the Hackensack Meridian Health story by **doing one session in an IDMC org**, then stopping. Do not read ahead and do not run two sessions in one sitting.

You have admin access. That is enough for every session in this folder. You do **not** need a new login, a new org, or the work CI/CD pipeline.

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
| 8 | [08-mdm-model.md](08-mdm-model.md) | 70 min | Patient, Provider, and Location exist and you can open a create page |
| 9 | [09-mdm-load-and-search.md](09-mdm-load-and-search.md) | 60 min | Search finds Ava from a file import |
| 10 | [10-match-merge-survivorship.md](10-match-merge-survivorship.md) | 80 min | Two Ava source rows, **one** master, North phone wins for Noah |
| 11 | [11-hierarchy-relationship.md](11-hierarchy-relationship.md) | 60 min | Two hospitals sit under one system node |
| 12 | [12-ingress-egress-jobs.md](12-ingress-egress-jobs.md) | 90 min | A golden-record file leaves MDM, or you stopped at the license gap |
| 13 | [13-other-idmc-services.md](13-other-idmc-services.md) | one service only | You practiced **one** extra service, or skipped it on purpose |
| 14 | [14-do-not-mix-with-work-cicd.md](14-do-not-mix-with-work-cicd.md) | 20 min | You can say what this practice is **not** allowed to touch |

## Rules

1. One file per sitting. If a job is still running, wait. Do not start the next file.
2. Project name in every session: `MERIDIAN_PRACTICE`.
3. Org: a DEV or training org only. Never Production.
4. Data: only the fake rows in session 00. No real patient, member, or employee data.
5. Do not tag these assets. Do not edit `flo_dev.yml` or any work YAML.
6. If a service is missing under **My Services**, write `NOT LICENSED` in your session log and go to the next **required** session. Sessions 1 through 6 and 8 through 11 are the core. 7, 12, and 13 can be skipped when the license is missing.
7. Button names move between releases. Each step says the **goal** first. If the button is not where the text says, use the top search and type the asset name.

## Where this sits next to the other notes

- Git and the work deploy path: [06-idmc-cicd-picture.md](../06-idmc-cicd-picture.md) then [07-idmc-dev-to-sit-practice.md](../07-idmc-dev-to-sit-practice.md). Do those **after** session 14, not before.
- The admin prompt in [hello-world/idmc](https://github.com/ravi0123456/hello-world/tree/main/idmc) is for reconstructing the **work** platform. It is not this practice.
