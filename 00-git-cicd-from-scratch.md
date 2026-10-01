# From scratch: Git, GitHub, YAML, Groovy, and CI/CD

This chapter assumes you have never used Git. Read it before the IDMC chapters.

You are an Informatica IDMC admin. You do not need to become a programmer. You only need to know what each file is, who runs it, and what happens when you save a change.

## The kitchen picture

Think of a restaurant kitchen.

| Kitchen thing | Tech thing | In our IDMC world |
|---|---|---|
| Recipe book on a shelf | Git repo | `AIP_DEV_IICS_CODE.git` on the on-prem server |
| One copy of the recipe for a shift | Branch | `dev`, `sit`, `uat`, `master` |
| One saved edit in the book | Commit | Changing `tagName` and saving `flo_dev.yml` |
| The restaurant brand website | GitHub.com | Your practice repos only. Work Git is on-prem, not this site |
| A waiter who takes the order slip | YAML file | `flo_dev.yml` tells Jenkins what to deploy |
| The cook who follows a written method | Groovy / Jenkins pipeline | The hidden job that calls Informatica CLI |
| The kitchen robot that cooks when a new slip arrives | CI/CD | Tag assets, commit YAML, Jenkins deploys DEV to SIT |

You write the order slip. You do not write the cook's method on day one.

## Git, from zero

Git is software that remembers every saved version of a folder.

Without Git, you email `mapping_final_v3_REAL.xlsx`. With Git, every save has a person, a time, and a short message. You can go back.

Three words only:

- **Repo.** The folder plus its history. Ours at work is `AIP_DEV_IICS_CODE.git`.
- **Commit.** One snapshot. "I changed the tag to admin_test_001."
- **Branch.** A named line of commits. `dev` is the line used to build from DEV and send to SIT.

A **clone** is a copy of the repo on your computer. A **push** sends your commits to the server. A **pull** brings other people's commits down.

You can also edit a file on the website and click Commit. That is still Git. The website is only a window.

## GitHub is not the same as Git

**Git** is the history tool. It can live on any server.

**GitHub.com** is one company's website that hosts Git repos. Your learning repos `github-mastery-guide` and `hello-world` live there.

Work deployments use Git on an on-prem server. The IDMC screen said Platform = On-Premise. The URL starts with your company host, not github.com. A bookmark named GitHub can still open that internal site. The engine is Git. The website brand can differ.

IDMC Source Control is a third thing. It is only a link from an IDMC org to a Git repo. In your screenshots, push from IDMC is off. So IDMC is not saving commits. Jenkins is.

## YAML, from zero

YAML is a list of settings. It is not a program. It is a form in text.

Rules you will see:

- `key: value` means name on the left, answer on the right
- Quotes are often used around words, like `'IICS'`
- Spaces matter. Do not use Tab
- `#` starts a comment. The robot ignores the rest of that line

A tiny example that matches our pipeline:

```yaml
deploy_override: 'IICS'
tagName: 'admin_test_001'
release: build_deploy
publishEnabled: 'false'
notification_users:
  - your.name@company.com
```

What this says in plain English:

- This run is an IICS / IDMC deploy, not a database script deploy
- Deploy only assets that have the tag `admin_test_001`
- Build the zip and deploy it
- Do not publish a taskflow
- Mail this person when it finishes

If `tagName` does not match the tag in IDMC, Jenkins still runs. It just has nothing useful to pick up, or it fails. Spelling must be exact.

`flo_dev.yml` is the order slip for DEV to SIT. `flo_master.yml` is the order slip for production. Do not edit master for a first test.

## Groovy and Jenkins, from zero

**Jenkins** is a website that runs jobs. Your bookmark "Jenkins - endusers" is that site.

A Jenkins job is a recipe. In many companies that recipe is written in **Groovy**. Groovy looks like Java. You do not write it for a first deploy.

The split is:

| File | Who edits it | What it does |
|---|---|---|
| `flo_dev.yml` | Application team or admin, with permission | Says *what* to deploy this time |
| Jenkins Groovy / Jenkinsfile | DevOps, rarely changed | Says *how* to log in to IDMC, call the CLI, export, import, mail |

When you commit the YAML, Jenkins notices the new commit. The Groovy recipe reads the YAML. Then it calls Informatica's command line tool. That tool talks to IDMC. You do not type those commands.

CI means Continuous Integration: the robot checks or builds when someone saves. CD means Continuous Delivery or Deployment: the robot also moves the result to the next environment. Together people say CI/CD.

Our CD step is: take tagged assets from one IDMC org and put them in the next org, same project name.

## How the whole machine works together

```
You tag a mapping in the DEV org
        |
        v
You change tagName in flo_dev.yml
        |
        v
You commit that file on the dev branch
        |
        v
Git stores the new snapshot on the on-prem server
        |
        v
Jenkins sees the new commit
        |
        v
Groovy job reads the YAML
        |
        v
Informatica CLI exports the tagged assets
        |
        v
CLI imports them into the SIT org, same project
        |
        v
Mail goes to notification_users
```

Nothing in this path creates a new project or a new connection. Those must already exist in SIT.

## What you must never put in Git

Passwords, Key Vault secret values, client secrets, and tokens. Git keeps history. Deleting a line later does not erase the old snapshot.

Put only names: tag name, project name, connection name, your work email.

## Tiny practice on github.com, no Jenkins

Open [hello-world](https://github.com/ravi0123456/hello-world).

1. Create a branch named `learn-yaml`.
2. Add a file `practice/flo_practice.yml` with only fake values, like `tagName: 'learn_001'`.
3. Commit on that branch.
4. Open a pull request into `main`.

That teaches the buttons. It does not touch IDMC.

When you are ready for the real path, read [06-idmc-cicd-picture.md](06-idmc-cicd-picture.md) and then [07-idmc-dev-to-sit-practice.md](07-idmc-dev-to-sit-practice.md).
