# Practice: deploy a few assets from DEV to SIT

Do this only after you have read [06-idmc-cicd-picture.md](06-idmc-cicd-picture.md). This is a learning run, not a production change.

You are an IDMC admin. You do not need to be a Git expert for this first run. You do need a guide from the team that owns the pipeline, because a commit on the `dev` branch starts Jenkins.

## Words you will see

| Word | Plain meaning |
|---|---|
| Repo | The Git book. Ours is `AIP_DEV_IICS_CODE.git` on the on-prem server. |
| Branch | A named line of history. `dev` is the line used to build from DEV and deploy to SIT. |
| Commit | A saved change. Committing the YAML is what starts the job. |
| Tag | A label on IDMC assets. The pipeline deploys what carries this label. |
| YAML | A settings file. `flo_dev.yml` tells Jenkins what to deploy. |
| CLI | Informatica's command tool. Jenkins uses it. You do not run it by hand for this path. |

## Before you touch Git

1. Pick one small, harmless asset in the DEV org. A test mapping is better than a real taskflow.
2. Write down the project name. SIT must already have that same project.
3. In SIT, confirm the connection names and the runtime already exist. The pipeline will not create them.
4. Confirm the project is onboarded to the SIT org. If it is not, stop and ask the platform team.
5. Pick a new tag name that has not been used. Example: `admin_test_001`. Do not reuse `DEMO_CICD` or a ServiceNow change tag.
6. Ask the pipeline owner which branch you may commit to, and which file. The PDF says the DEV branch plus `flo_dev.yml` is the DEV to SIT path. Confirm that before you edit.

## In the DEV org

1. Open the project that already exists in SIT with the same name.
2. Select the test asset.
3. Use the action menu, then Tags.
4. Add your new tag and save.
5. If you tagged a taskflow, expect mappings and mapping tasks to travel with it. That is what the PDF says the CLI does.

Do not change connections during this test.

## In Git

The file from the screenshot lives in the project view `AIP-EU-COG-WORKVIEW`, file `flo_dev.yml`.

1. Open that file on the `dev` branch. If you cannot see the branch, stop. You are in the wrong place.
2. Copy the file to a note before you edit it, so you can put it back.
3. Change only these fields:
   - `deploy_override` stays `IICS`
   - `tagName` becomes your new tag, in quotes, exactly the same spelling
   - `release` stays `build_deploy` only if the owner says a real deploy is allowed. If they want a zip only, use `build`
   - `publishEnabled` stays `false` for this test
   - `notification_users` includes your own work email, so you get the status mail
4. Commit with a clear message, for example `Test deploy tag admin_test_001 from DEV to SIT`.
5. The PDF says the job starts on that commit. Watch Jenkins and your mail. Do not commit again while it is running.

## How to know it worked

- Jenkins is green, or the mail says success.
- In the SIT org, the same project shows the asset.
- The asset points at the SIT connection that already existed, not a new one.
- No new project was created. If the project was missing, the job should fail. That is expected.

## If it fails

| What you see | What it usually means |
|---|---|
| Project missing in SIT | Onboard or create the project first. The pipeline will not create it. |
| Connection missing | Create the same connection name in SIT before you retry. |
| Wrong tag | `tagName` and the IDMC tag do not match. Fix the spelling and commit again only after the owner agrees. |
| Job did not start | Commit was on the wrong branch, or the YAML was not the trigger file. |
| Taskflow will not run | Publishing user name is wrong or missing. Names are case sensitive. |

## What not to do on the first day

- Do not enable source control in PROD to "match" the other orgs.
- Do not tick "Allow Push to Git Repository" unless the platform owner asks for it.
- Do not commit `flo_master.yml`. That path is for production and needs a tech-lead merge.
- Do not put passwords in the YAML or in Git.
- Do not publish a taskflow in the same test. Leave `publishEnabled` false.

## A tiny Git practice on github.com, separate from work

Your practice repo is [hello-world](https://github.com/ravi0123456/hello-world). Use it to learn commit and branch with no Jenkins behind it.

1. On github.com, open `hello-world`.
2. Create a branch named `dev`.
3. Add a file `notes/idmc-test-plan.md` with the asset name and tag you plan to use.
4. Commit on that branch.
5. Open a pull request into `main`.

That practice does not deploy IDMC. It only teaches the buttons you will see on the work Git server.
