# Session 11 — Hierarchy and relationships

**Time:** 60 minutes. **Requires:** session 10 proof (one Ava, two sources). Leave match rules alone.

## The idea in one minute

A master patient is still not the episode of care. The episode needs **places** and **people**.

A **hierarchy** is a tree of the same kind of thing. Locations: a health system contains hospitals. Parent `HMH-SYS`, children `HMH-HACK` and `HMH-JERSEY`. You do not model this by stuffing `parent_code` only into a text field and hoping. The text field is how the file **says** the parent. The hierarchy is how MDM **stores** the tree so a search for the system can show both hospitals.

A **relationship** links **different** entities. Patient —treated by→ Provider. That is not a hierarchy. A hierarchy of patients would mean "this patient contains that patient", which is nonsense here.

The real story's line was: for one patient, list the doctors, the labs, and the follow-ups across the network. You already counted Ava's three encounters in CDI (session 4) **inside one EMR file**. MDM is what makes her one person across EMRs. This sitting adds the network structure those encounters point at. You will not rebuild the encounter aggregator inside MDM today.

## Meridian example

```text
Meridian Practice Health (HMH-SYS)
  ├── Practice Hospital Hackensack (HMH-HACK)   Dr Kim Alvarez
  └── Practice Hospital Jersey City (HMH-JERSEY) Dr Sam Ortiz
```

Ava's visits used both hospitals. The tree is how you show they are one network, not two unrelated sites.

## Do this

**Goal: load locations and providers as their own records.**

1. File import entity `Location`, source system `CRED` (or create a source system `HOSP` if you would rather not reuse the credentialing system — if you create `HOSP`, write it in the log and use it here only).

`locations_for_mdm.csv`

```csv
source_pk,hospital_code,hospital_name,city,state,parent_code
LOC-SYS,HMH-SYS,Meridian Practice Health,Edison,NJ,
LOC-HACK,HMH-HACK,Practice Hospital Hackensack,Hackensack,NJ,HMH-SYS
LOC-JERSEY,HMH-JERSEY,Practice Hospital Jersey City,Jersey City,NJ,HMH-SYS
```

Map `source_pk` to the source primary key. Map `parent_code` to your field. Import. Success, 3 rows.

2. File import entity `Provider`, source system `CRED`.

```csv
source_pk,npi,full_name,specialty,hospital_code
P501,1000000001,Dr Kim Alvarez,Cardiology,HMH-HACK
P502,1000000002,Dr Sam Ortiz,Primary Care,HMH-JERSEY
```

Success, 2 rows. Search `Alvarez` and open the record.

**Goal: a hierarchy, not just a parent text field.**

3. Find **Hierarchies** in Business 360. New hierarchy. Name: `hh_meridian_locations`. Entity: `Location`.
4. If it asks for the parent relationship or parent field, point it at `parent_code` / the parent location. Releases differ: some want you to add the parent in the UI by dragging, some create the tree from a parent field during a hierarchy job.
5. Use the UI's add-node path if there is no automatic build:
   - Root: Meridian Practice Health
   - Child: Hackensack
   - Child: Jersey City
6. Open the hierarchy and confirm both hospitals sit under the system. A flat list of three with no parent is not done.

**Goal: one relationship type, and one link you create by hand.**

7. Find **Relationships**. New relationship. Name: `rel_patient_provider`. From `Patient` to `Provider`. Label: `treated by`. Direction: patient to provider.
8. Save. Open Ava. Add a relationship `treated by` Dr Kim Alvarez. Save.
9. Open Dr Kim Alvarez and confirm the related patient is Ava, if the UI shows the reverse. If the relationship is one-way, say so in the log. One direction is enough.
10. Do not try to load all five encounters as relationships in this sitting.

## What you should see

- 3 locations, 2 providers, searchable.
- A tree: system, then two hospitals.
- Ava linked to Dr Kim Alvarez.

## If it fails

| What you see | What it usually means |
|---|---|
| No Hierarchies menu | License or privilege. You still have `parent_code` on the records. Write `HIERARCHY UI NOT AVAILABLE`, keep the relationship if you can, and do not block session 12. |
| Import rejected locations | Source primary key not mapped, or `hospital_code` marked unique and you imported twice. |
| Relationship will not save | The relationship is not on the application page layout. Add it the same way you added fields in session 8. |

## Write this in the session log

```text
Location import job:
Provider import job:
Hierarchy shows 2 children: yes/no
Ava treated by: Dr Kim Alvarez yes/no
```

## Answer before session 12

1. Why is a hospital tree a hierarchy, not a relationship?
2. What did session 4 prove that this session does not yet prove?

## Stop

Next sitting is the automated job in and out of MDM: [12-ingress-egress-jobs.md](12-ingress-egress-jobs.md).
