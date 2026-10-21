# Chapter 14 — Glossary: CI/CD, Ansible, Jenkins, IDMC

Simple one-line meanings for the words in chapters 08 to 13. The basic Git and GitHub words are in [glossary.md](glossary.md).

## YAML

| Term | Meaning |
|---|---|
| **YAML** | Plain text settings file (`.yml` / `.yaml`) that tools read. |
| **Key / value** | A label and its value, written `key: value`. |
| **Indentation** | Spaces at the start of a line that show what belongs to what. No tabs. |
| **List** | Items that start with `- `. |
| **yamllint** | A tool that checks YAML files for mistakes. |

## Ansible and AWX

| Term | Meaning |
|---|---|
| **Ansible** | Tool that runs steps on servers for you, from YAML files. |
| **Playbook** | YAML file with the steps (plays and tasks). |
| **Play** | One block in a playbook: which hosts and which tasks. |
| **Task** | One step in a play. |
| **Module** | The built-in tool a task uses (`copy`, `file`, `service`...). |
| **Handler** | A task that runs only when another task says something changed. |
| **Inventory** | The list of servers, grouped (e.g. `app_servers`). |
| **Host** | One server in the inventory. |
| **Variables / extra_vars** | Values given to a playbook; `extra_vars` are sent at launch time. |
| **Ansible Vault** | Way to encrypt secrets inside an Ansible repo. |
| **AWX** | Website and API for running Ansible (open-source Ansible Tower). |
| **Project (AWX)** | AWX's link to a Git repo with playbooks. |
| **Credential (AWX)** | Safely stored password, SSH key or token in AWX. |
| **Job template** | Saved "run this playbook on this inventory with these credentials". |
| **Job** | One run of a job template, with a log. |

## Jenkins

| Term | Meaning |
|---|---|
| **Jenkins** | Automation server that runs pipelines. |
| **Pipeline** | The automated set of stages Jenkins runs. |
| **Jenkinsfile** | File in Git that describes the pipeline. |
| **Declarative pipeline** | Simple, form-like Jenkinsfile style starting with `pipeline { }`. |
| **Groovy** | Programming language Jenkinsfiles are written in. |
| **Agent** | Machine that runs the pipeline steps. |
| **Stage** | Named group of steps (Checkout, Validate, Deploy). |
| **Step** | One action, like `sh` or `echo`. |
| **Build** | One run of a pipeline, numbered. |
| **Parameter** | A value you choose when you click **Build with Parameters**. |
| **Credential (Jenkins)** | Secret saved in Jenkins, used in a Jenkinsfile by its ID. |
| **withCredentials** | Jenkinsfile block that safely puts a secret into a variable. |
| **Webhook** | Message GitHub sends to another tool when something happens. |
| **Multibranch pipeline** | Jenkins job that builds every branch and PR automatically. |
| **input** | Jenkinsfile step that waits for a person to approve. |

## APIs

| Term | Meaning |
|---|---|
| **API** | A way for one program to talk to another. |
| **REST API** | API over web addresses (URLs) using GET, POST, etc. |
| **Endpoint** | One API address, e.g. `/api/v2/jobs/15/`. |
| **GET / POST** | Read something / ask to create or start something. |
| **JSON** | Text data format with `{ "key": "value" }`. |
| **curl** | Command-line tool to call URLs and APIs. |
| **jq** | Command-line tool to read values out of JSON. |
| **Bearer token** | A token sent in the `Authorization` header to prove who you are. |
| **HTTP 401 / 403** | Not logged in / logged in but not allowed. |

## Informatica IDMC

| Term | Meaning |
|---|---|
| **IDMC** | Informatica Intelligent Data Management Cloud (older name IICS). |
| **Org** | One IDMC tenant; often one per environment. |
| **Sub-org** | An org under a parent org. |
| **Pod / region** | The Informatica data centre your org lives on (e.g. `usw3`, `na2`). |
| **Asset** | A thing you build in IDMC (mapping, task, taskflow...). |
| **Source control (IDMC)** | Feature that saves IDMC assets in a Git repo. |
| **Check in** | Save asset versions to Git as a commit. |
| **Check out** | Lock an asset for editing under source control. |
| **Pull (IDMC)** | Load assets from a chosen Git commit into an org. |
| **Commit hash** | Unique ID of a commit; used to promote exact versions. |
| **Promotion** | Moving tested assets to the next environment (Dev → QA → Prod). |
| **Export / Import** | Move assets between orgs using a ZIP file. |
| **Runtime environment** | Where jobs run: Secure Agent group or serverless. |
| **Secure Agent** | Informatica program on your server that runs jobs. |
| **Connection** | Saved details for reaching a source or target system. |
| **Session ID** | Temporary login ID the IDMC REST API returns; sent as `INFA-SESSION-ID`. |
| **Service user** | A non-person account used by automation like Jenkins. |

---

Back to the guide home: [README.md](README.md)
