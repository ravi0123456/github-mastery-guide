# Session 13 — The other IDMC services, one per sitting

**Time:** 45–60 minutes for **one** service, then stop. **Requires:** session 10. Session 12 is helpful but not required.

## The idea in one minute

IDMC is a shelf of services. The Meridian story used MDM as the system of record, CDI as the pipes, and DQ as the rules. The rest of the shelf is real, and it is how people get bored: they open five consoles and finish none.

Pick **one** row below whose service appears in **My Services**. Do that practice. Write `SKIP` next to the others for today. You can come back tomorrow for a different row. One service per sitting.

Never point these at Production. Never use a real member, patient, or employee file. Reuse `MERIDIAN_PRACTICE` and the fake CSVs.

## Pick list

| Service | What it is | Meridian practice (one sitting) | Done when |
|---|---|---|---|
| Application Integration (CAI) | A process that calls APIs and other jobs, including the process that passes `jobInstanceId` into an ingress taskflow | New process, name `prc_meridian_ping`. One assignment step that sets a field `note` to `meridian practice`. Save and **publish**. Run it once with no outbound call. Only if that feels too small: add a service connector later, not today. | Monitor or the process log shows one successful run |
| API Center (or API Manager) | Publish and govern APIs | Do **not** publish a patient API. Create an API from the ping process only if CAI already published `prc_meridian_ping`. Managed API name `api_meridian_ping`. Call it once if a URL is generated. If you have no process, skip API Center. | You can say who is allowed to call it, or you skipped |
| API Portal / API Gateway pieces inside API Center | The front door and the policies (auth, rate limit) | If you created `api_meridian_ping`, open its policies and confirm an auth policy exists. Do not turn auth off to "make the test easier". | You saw the policy page |
| Data Governance and Catalog / Metadata Command Center | Catalog of assets and lineage, the "15+ sources" part of the real story | If a scanner for IDMC / CDI exists, run it for project `MERIDIAN_PRACTICE` only, or search the catalog for `m_land_north`. | You opened one asset page, or you wrote `NOT LICENSED` |
| Integration Hub | Publish and subscribe so many apps get a copy without point-to-point jobs | Skip unless you already know a DEV hub. Do not create a topic that other teams consume. | `SKIP` is an acceptable result |
| Mass Ingestion | Land many files or tables in one job | Only if you can point it at the `meridian_practice` folder and a **new** target folder `meridian_practice_out`. Do not point it at a shared drop zone. | A job landed copies of your CSVs, or `SKIP` |
| B2B Gateway | EDI with external partners | Not this story. Write `OUT OF SCOPE`. | One sentence in the log: EDI is partner exchange, not patient match |
| Operational Insights | Health of jobs across the platform | Open it. Find any MDM or CDI metric. Do not change thresholds. | You found one chart and left it |
| Reference 360 | Governed code lists | Create code list `gender_practice` with values `F` and `M` only if you can keep it in a practice folder. Do not edit a shared country or gender list other projects use. | The list exists, or `SKIP` to avoid a shared list |
| Customer 360, Product 360, Supplier 360 | Prebuilt apps on MDM | Do not add Ava to a live customer app. Open the admin home, confirm it is a different application from Business 360, close it. | You can say it is a prebuilt domain app, not the CDI designer |
| Data Marketplace | Share data products with consumers | Skip. Write `OUT OF SCOPE` until a catalog exists. | `SKIP` |
| CLAIRE | Suggestions inside designers | While in a mapping you already built, notice if recommendations appear. Do not accept one you cannot explain. | One sentence: you accepted nothing blindly |
| Cloud Data Integration for PowerCenter | PowerCenter migration | Out of scope for Meridian. | `OUT OF SCOPE` |

## Application Integration, a little more detail (only if you picked it)

1. **My Services** → **Application Integration**.
2. **New** → **Process**. Name `prc_meridian_ping`. Location `MERIDIAN_PRACTICE` if offered.
3. Add an Assignment step. Field `note` = `meridian practice`.
4. Save. **Publish**. Unpublished processes do not run. This is the same trap as taskflows.
5. Run with an empty input. Success is the finish line.
6. Optional stretch, still the same sitting only if the first run took under 20 minutes: a second step is not required. Stop while it is green.

The production ingress path is a CAI process that starts the MDM job and passes `jobInstanceId` into the CDI taskflow. You do **not** have to build that to understand it. You have to see that CAI is the orchestrator for that id, and CDI is the mapping. If you already built `m_ingress_one` in session 12, you may look at how an MDM ingress wizard references a process — look, do not rewire Ava's data.

## Catalog, a little more detail (only if you picked it)

The real team catalogued 15+ sources, including Epic, so a steward could see where a field came from. Your version is one project.

1. Open the catalog or Metadata Command Center.
2. Do not run a scanner aimed at every production database.
3. If there is an Informatica cloud / IDMC scanner already configured for DEV, run it or open its last run and search `m_land_north` or `Patient`.
4. Open one asset. If lineage is empty, that is still a result: write `no lineage yet`. Empty lineage usually means the scanner has not loaded CDI assets, not that your mapping failed.

## What you should see

- Exactly one service practiced.
- A line in the log for every other row: `SKIP` or `OUT OF SCOPE` or `NOT LICENSED`.
- No new schedule, no Production connection, no real patient file.

## If it fails

Stop at the first error, write the error, and end the sitting. Do not switch to a second service to "get a win".

## Write this in the session log

```text
Service I practiced:
Done when (met?):
Everything else: SKIP / NOT LICENSED / OUT OF SCOPE
```

## Answer before session 14

1. Which service did you actually click, and what job or asset did you create?
2. Why is API Center a bad place to put Ava on day one?

## Stop

Last sitting is short, and it protects your work org: [14-do-not-mix-with-work-cicd.md](14-do-not-mix-with-work-cicd.md).
