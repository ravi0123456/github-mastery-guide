# Session 0 — The story, then stop and set up

**Time:** 30 minutes. **Next file is locked** until the checklist at the bottom is done.

## The idea in one minute

Hackensack Meridian Health grew by joining many hospitals. Each hospital kept its own electronic medical record (EMR), especially Epic. The same person existed more than once. A doctor in one hospital could not see the visit, the lab, and the follow-up that happened in another hospital.

They used Informatica master data management (MDM) to decide "this is the same person" and to keep one trusted record, the **master** (also called the golden record). Source rows stay underneath it. That is why 6.5 million patient rows became 3.2 million masters: duplicates collapsed, history did not disappear. They also catalogued 15+ sources so people could see where a field came from (lineage).

You will replay that story with **7 fake people, not 6.5 million**. If you can explain Ava Shah, you understand the real project.

## The cast (all fake)

| Who | What you will see | What it teaches |
|---|---|---|
| Ava Shah | In Epic North as `Ava` and Epic South as `Ava S`. Same date of birth, same address written two ways. | Match and merge |
| Noah Patel | In both Epics. North phone `201-555-0102`, South phone `9735550199`. | Survivorship: which value wins |
| Mia Chen | North only | A person who is not a duplicate |
| Leo Nguyen | South only | Same |
| Bad Date | North row with `NOT_A_DATE` | Reject bad data before it enters MDM |
| Dr Kim Alvarez, Dr Sam Ortiz | Providers | A second domain, and a relationship |
| Three locations | A system and two hospitals | Hierarchy |

The business question, in one line: **after the job runs, can you list every encounter Ava had, in every hospital, on one person?**

## How IDMC services split that question

IDMC is Informatica Intelligent Data Management Cloud. Older screens say IICS. Same platform. **My Services** is the list of products your org has licensed.

| Service | Job in this story | When you touch it |
|---|---|---|
| Administrator | Runtime and the folder the files live in | Session 1 |
| Data Integration (CDI) | Mappings and the jobs that move and shape files | Sessions 2–6 |
| Monitor | Proof the job worked | Every session after 2 |
| Data Quality (CDQ) | Reusable rules and a score, not only a one-off expression | Session 7 |
| MDM SaaS / Business 360 | Model, load, match, merge, survivorship, hierarchy | Sessions 8–12 |
| Application Integration, API Center, Catalog, and the rest | Real-time calls, APIs, lineage | Session 13, one at a time, only if licensed |

A **mapping** is a drawing of the data flow. It does not run. A **mapping task** is the job: it binds that drawing to a runtime and a connection. A **taskflow** is a job made of several jobs, with a success/fail branch. A **schedule** is the clock. An **ingress job** loads source rows into MDM. An **egress job** sends masters back out. You will create each of these, not only read the names.

## Which org

Use a DEV or training org where you are an admin. Write the org name in the log below.

Do **not** use Production. Do **not** use the org that feeds the work Jenkins pipeline for a first run. This project must never be tagged for deploy.

## Create the files on your computer

Make a folder you can copy onto the Secure Agent later, for example `C:\meridian_practice` (Windows) or a home folder if the agent is Linux. The agent must be able to read this path. You will confirm that in session 1.

Create these five files exactly. Header row included. No spaces after commas.

`patients_epic_north.csv`

```csv
source_system,source_pk,first_name,last_name,dob,gender,phone,address,city,state,zip,mrn
EPIC_NORTH,N1001,Ava,Shah,1984-03-12,F,(201) 555-0101,10 River Rd,Hackensack,NJ,07601,MRN1001
EPIC_NORTH,N1002,Noah,Patel,1979-11-02,M,201-555-0102,22 Oak St,Edison,NJ,08817,MRN1002
EPIC_NORTH,N1003,Mia,Chen,1991-07-19,F,2015550103,5 Bay Ave,Jersey City,NJ,07302,MRN1003
EPIC_NORTH,N1004,Bad,Date,NOT_A_DATE,X,555,??,Nowhere,ZZ,00000,MRN0000
```

`patients_epic_south.csv`

```csv
source_system,source_pk,first_name,last_name,dob,gender,phone,address,city,state,zip,mrn
EPIC_SOUTH,S2001,Ava S,Shah,1984-03-12,Female,2015550101,10 River Road,Hackensack,NJ,07601,MRN1001
EPIC_SOUTH,S2002,Noah,Patel,1979-11-02,Male,9735550199,22 Oak Street,Edison,NJ,08817,MRN1002
EPIC_SOUTH,S2003,Leo,Nguyen,1988-01-30,M,7325550144,80 Main St,New Brunswick,NJ,08901,MRN3003
```

`providers.csv`

```csv
source_system,source_pk,npi,full_name,specialty,hospital_code
CRED,P501,1000000001,Dr Kim Alvarez,Cardiology,HMH-HACK
CRED,P502,1000000002,Dr Sam Ortiz,Primary Care,HMH-JERSEY
```

`locations.csv`

```csv
hospital_code,hospital_name,city,state,parent_code
HMH-SYS,Meridian Practice Health,Edison,NJ,
HMH-HACK,Practice Hospital Hackensack,Hackensack,NJ,HMH-SYS
HMH-JERSEY,Practice Hospital Jersey City,Jersey City,NJ,HMH-SYS
```

`encounters.csv`

```csv
encounter_id,mrn,hospital_code,provider_npi,encounter_date,encounter_type
E1,MRN1001,HMH-HACK,1000000001,2026-01-10,Office visit
E2,MRN1001,HMH-JERSEY,1000000002,2026-02-02,Lab
E3,MRN1001,HMH-HACK,1000000001,2026-02-20,Follow-up
E4,MRN1002,HMH-HACK,1000000002,2026-01-15,Office visit
E5,MRN3003,HMH-JERSEY,1000000002,2026-03-01,Office visit
```

`state_codes.csv`

```csv
state_code,state_name
NJ,New Jersey
NY,New York
PA,Pennsylvania
```

Count rows before you continue: North 4 data rows, South 3, providers 2, locations 3, encounters 5, states 3.

## Session log (copy this into a note)

```text
Org (not Production):
Runtime name (fill in session 1):
Flat file directory on the agent:
Project: MERIDIAN_PRACTICE
Session 0 files saved: yes/no
```

## Answer in your own words before session 1

1. Why did the real project end with fewer patient records than it started with?
2. Which two rows are the same person written twice?
3. Which row must never become a master record?

## Stop

Do not open Data Integration yet. Next sitting is only the runtime and the connection: [01-admin-runtime-connection.md](01-admin-runtime-connection.md).
