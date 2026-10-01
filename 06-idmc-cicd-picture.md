# IDMC and Git: the picture in our setup

This chapter is for an Informatica IDMC admin who is new to Git. It explains what the screenshots show, in simple English.

IDMC means Informatica Intelligent Data Management Cloud. Older screens still say IICS. Same platform.

Git is a history book for files. A repo is that book. A branch is a named copy of the book, such as `dev`, `sit`, `uat`, or `master`. A commit is one saved page in the history.

Our deployment Git is **not** github.com. The IDMC screen says Platform = On-Premise. The repo URL in the screenshots is:

`https://nausp-aapp0001.aceins.com/AIP/AIP_DEV_IICS_CODE.git`

The YAML screenshots are from a project view named `AIP-EU-COG-WORKVIEW`, file `flo_dev.yml`. That view is the place application teams edit to start a deploy. Jenkins reads that file. Jenkins is the robot that runs the deploy. The bookmark "Jenkins - endusers" is that robot's website.

## Two different Git uses. Do not mix them up.

| Use | Who drives it | What it is for |
|---|---|---|
| IDMC Source Control | The IDMC org settings screen | Lets an org keep a copy of assets in Git. In the screenshots, push from IDMC is off. |
| CI/CD pipeline | Jenkins, started by a commit of `flo_<env>.yml` | Moves tagged assets from one org to the next. This is the real DEV to SIT path. |

If Source Control is off in an org, that org is not using IDMC's own Git button. CI/CD can still deploy into that org, because Jenkins calls Informatica's command line tool, not the Source Control checkbox.

## What the org screenshots show

Same repo URL on the lower orgs. Platform On-Premise. "Allow Push to Git Repository" is unchecked. "Allow OAuth access to Git" is unchecked. "Enable Project Level Source Control" is unchecked, so the repo is org-wide, not one repo per project.

| Screen | What is visible | Git URL | Branch field | Runtime |
|---|---|---|---|---|
| First org screen (called DEV) | Source control enabled | `AIP_DEV_IICS_CODE.git` | `sit` | `NA_DataLake` |
| Chubb SIT on `usw3` | Source control enabled | same URL | `sit` | `NA_Data_Warehouse` |
| Next lower org screen | Source control enabled, push off | same URL | `sit` | `NA_Data_Warehouse` |
| `na2` Chubb screen | CLAIRE settings only. No source-control values in that shot | not shown | not shown | not shown |

The pod in the lower-org address bar is `usw3.dm-us.informaticacloud.com`. The later Chubb screen is `na2.dm-us.informaticacloud.com`. Different pod usually means a different org, often a higher environment.

### Why PROD source control can be empty

This is normal in this design, not a broken screen.

- Lower orgs keep a Git link so assets can be versioned and so the pipeline can export from a known place.
- PROD is the place code lands. The production steps in the PDF do not start from a PROD Git branch inside IDMC. They start from a pre-prod staging project, a tag, a YAML file, a Jira approval, and a merge by a tech lead.
- "Allow Push to Git" is already off in lower orgs. PROD being unconfigured means nobody is committing from Production back into Git by mistake.
- Jenkins does not need PROD source control to be filled in. It deploys with the Informatica CLI into a project that already exists.

Confirm with the platform owner before changing PROD. Do not turn on "Allow Push to Git Repository" in PROD just to make the screen look like DEV.

### Why the branch field says `sit` even on the DEV screen

The IDMC field "Global Git Branch Name" is only the branch that this org reads or writes if IDMC itself talks to Git. In these screenshots it is `sit`, and push is off.

The promotion branches are a separate rule, written in the deployment PDF:

1. `dev` branch: build code from DEV and deploy to SIT.
2. `sit` branch: build code from SIT and deploy to UAT.
3. `uat` branch: build the UAT code.
4. `master` branch: deploy to Production. The PDF says code can come from SIT or UAT based on a build number. Village page `DOC-569710` has the longer production steps.

So the word `sit` on the IDMC screen is not the same thing as "this deploy is going to SIT". The YAML file and the branch you commit to decide the direction.

## How a lower-environment deploy actually moves

```
Developer tags assets in the source IDMC org
        |
        v
Someone edits flo_<env>.yml in the on-prem Git repo
        |
        v
Commit on the right branch (dev for DEV to SIT)
        |
        v
Jenkins starts by itself
        |
        v
Informatica CLI exports the tagged assets, including dependent objects
        |
        v
CLI imports them into the same project name in the next org
        |
        v
Mail goes to the people listed in the YAML
```

The PDF says the pipeline does a 1-to-1 merge into the **same project**. It does **not** create new projects or new connections. Those must already exist in the target org, and the code must already point at the target connection names.

Dependent objects travel with the tagged object. Tag a taskflow, and the related mapping tasks and mappings go too.

## What must already exist in SIT before a DEV to SIT deploy

- The runtime environment.
- The connections, with the names the mapping expects.
- The project, with the same name.
- The project must be onboarded to the SIT org. The PDF says the pipeline works only if the project is onboarded to the new SIT and UAT orgs.

## The YAML file is the trigger, not a design document

Sample fields from the PDF:

- `deploy_override: 'IICS'` means this run is an IICS/IDMC deploy, not a database script deploy.
- `tagName` must match the tag you put on the assets in IDMC. Example from the guide: `DEMO_CICD`.
- `release: build_deploy` means build the zip and deploy it. `build` means only make the zip.
- `folderPath` is the folder path the pipeline should pick up.
- `notification_users` is who gets the status mail. At least one mail is required.
- `publishEnabled` and `publishFilePath` are only for publishing a taskflow in the same run.

After the YAML is committed, the PDF says the deployment starts by itself and a status mail is sent.

Taskflow publish is optional. If you need it:

- Put the taskflow path in `dynafloPublishSeq.txt`. The file name is case sensitive.
- Example line: `Explore/UDP_CI_PROD/CIDA_Major_Accounts/tf_Major_Accounts_Village_Dynamics.TASKFLOW`
- Set `publishEnabled: 'true'` and set `publishFilePath` to that file.
- The publishing user must already be on the taskflow, in Start, Allowed Users. The name is case sensitive. Example shown: `Ingest_fwk_iics_user_prd`. A wrong name means the published taskflow will not run. The pipeline cannot be run only to publish.

## Production is a longer path. Do not use it for a first test.

From the SQLDW and IICS production PDF:

1. Developers copy code from the development project to the UAT project.
2. An approved application lead exports from UAT and imports into a pre-prod staging project in the IICS exploration environment. The project name must match production, for example `UDP_PRS_PROD`. During import they change target project, target connections, and target runtime.
3. They may export a zip to Git as their own backup.
4. They tag the staged assets with a meaningful name, for example `aip_<servicenow_change_number>`.
5. If a taskflow must be published, they add the publishing user during this staging edit.
6. They list taskflows to publish, or set publish to false.
7. A flow YAML commit creates one Jira approval for the AIP approver team. Deploy starts only after approval.
8. During build, the pipeline exports the pre-stage code to Nexus for backup. It reads the flow YAML from the SIT Git branch.
9. After a good build, someone updates `flo_master.yml` with the build number and tag, on the master branch. That opens a pull request. A tech lead must approve and merge.
10. The application team gets a completion mail.

Nobody should run jobs in the Pre-Prod_Staging folder. The CLI deploys dependent objects, so the staging folder should stay in sync with production for objects that are not changing.

## What you can safely try first

Read [07-idmc-dev-to-sit-practice.md](07-idmc-dev-to-sit-practice.md). Use a tiny test asset in DEV, tag it, and only then touch `flo_dev.yml` on the `dev` branch. Do not commit to `master` for a first test.
