# Session 1 — Runtime and a flat file connection

**Time:** 45 minutes. **Requires:** session 0 files on disk. **Do not** build a mapping in this sitting.

## The idea in one minute

IDMC design lives in the cloud. The job runs on a **runtime environment**. In most orgs that is a Secure Agent: a small program on a server that can see your files and databases. A **connection** is the saved path to a system (a folder, a database, Salesforce). The mapping never contains the password. It points at the connection. The connection points at a secret or a folder.

For this practice the "source system" is a folder of CSV files. Epic, in the real story, would be a database or an API connection. The shape of the lesson is the same.

## Meridian example

Epic North is not a CSV in real life. It is a connection named something like the hospital's Clarity database, tied to a runtime that can reach that network, with the password in a vault. You are building the small version: `FF_MERIDIAN_PRACTICE` → a folder → the agent that is **Running**.

## Words

| Word | Meaning |
|---|---|
| Runtime environment | Where the job executes. |
| Secure Agent | The usual runtime. Installed on a machine. |
| Hosted / serverless runtime | Informatica runs it for you. Fine for some cloud targets. **Do not** pick it later for a Business 360 connection. |
| Flat file connection | A folder the agent can read. |
| Test | IDMC checks the agent can see the folder. It does not load patients. |

## Do this

**Goal: know the exact runtime name that is Running.**

1. Log in to the non-production org from session 0.
2. Open **My Services**.
3. Open **Administrator**.
4. Open **Runtime Environments**.
5. Find a row whose status is **Running** (or Up). Copy the name into your session log. If several are running, pick the one your team uses for DEV file jobs. If none are running, stop. Starting an agent is allowed for an admin, but do it only on the DEV agent the platform owner already installed. Do not install a new agent on a laptop for this practice unless that is already your team's pattern.

**Goal: the practice folder is on that agent machine, not only on your laptop.**

6. If the agent is on a server you can reach, copy the session 0 folder there. Example Windows path: `D:\meridian_practice`. Example Linux path: `/data/meridian_practice`.
7. If you cannot copy files onto the agent, stop and ask the platform owner for a DEV directory you may use. Do not point this connection at a production drop folder.

**Goal: a connection that tests clean.**

8. In Administrator, open **Connections**.
9. **New Connection**.
10. Type: **Flat File** (sometimes under Flat File or FTP-style file connectors — pick Flat File).
11. Name: `FF_MERIDIAN_PRACTICE`.
12. Runtime: the Running runtime from step 5. Do not leave the default if it is a different agent.
13. Directory: the folder path **on the agent**, not the path on your laptop if those are different machines.
14. Set the date format to `yyyy-MM-dd` if the dialog asks. That matches the CSV.
15. Code page: UTF-8 if asked.
16. **Test**. You want **Successful**.
17. Save.

**Goal: prove the agent sees the files.** Optional but worth it.

18. If the test dialog can browse, confirm you see `patients_epic_north.csv`. If it cannot browse, you will prove it in session 2 when the source preview shows 4 rows.

## What you should see

- Connection `FF_MERIDIAN_PRACTICE` exists.
- Test result is successful.
- Your log has the runtime name and the directory.

## If it fails

| What you see | What it usually means |
|---|---|
| Agent not running | Wrong runtime, or the agent service is down. Do not switch to Production's agent. |
| Directory not found | The path is your laptop path, or a typo. The path must exist on the agent host. |
| Permission denied | The agent OS user cannot read the folder. |
| No Flat File type | Your org may use a different file connector. Use the file connector the team already uses in DEV, same folder, same column names. Write the connector type in the log. |

## Write this in the session log

```text
Runtime:
Connection: FF_MERIDIAN_PRACTICE
Directory:
Test: Successful / failed
```

## Answer before session 2

1. Why does the mapping not store the folder path itself?
2. What is wrong with testing this connection against a Production agent?

## Stop

Next sitting creates the first job and runs it once: [02-first-job.md](02-first-job.md).
