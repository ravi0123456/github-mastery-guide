# Session 14 — What this practice must never touch

**Time:** 20 minutes. **Requires:** you finished session 10. Sessions 12 and 13 can be incomplete.

## The idea in one minute

You now have two different "IDMC" pictures in your head. Keep them apart.

| Picture | Where it lives | What starts a deploy |
|---|---|---|
| Meridian practice | Project `MERIDIAN_PRACTICE` in a non-production org | You pressed Run. A disabled schedule. A file import. |
| Work CI/CD | The on-prem Git repo, `flo_<env>.yml`, Jenkins, tags | A **commit** of the YAML on the right branch |

The work path does not create projects or connections in the target org. It moves **tagged** assets into a project that already exists. Your practice project is not part of that path. Tagging it, or putting its name in a flow YAML, would try to ship a tutorial into SIT.

Read [06-idmc-cicd-picture.md](../06-idmc-cicd-picture.md) and then [07-idmc-dev-to-sit-practice.md](../07-idmc-dev-to-sit-practice.md) only after this page. Those chapters are the work picture. They are not homework for Ava.

## Do this

1. In Data Integration, open project `MERIDIAN_PRACTICE`. Confirm none of the assets have a tag you also use for work deploys. Remove any tag you added by experiment. Having **no** tag is the goal.
2. Confirm schedule `sch_meridian_practice_DISABLED` is disabled, or that you never left a schedule enabled. Check Monitor → Schedules if you are unsure.
3. Confirm you did not load a real extract. The only people in the practice MDM are the fake names from these files, plus `Test DeleteMe` if you did not delete it. If you see any other name, stop and tell the platform owner. Do not mass-delete.
4. Write the answers below in the session log. This is the whole practice.

## Answer, in your own words

1. Why did Ava become one master with two source rows?
2. Why did Noah's master keep the North phone while the South phone still exists?
3. What is a mapping, what is a mapping task, and what is a taskflow?
4. What is an ingress job versus an egress job?
5. Why is the work YAML file not how you run `mt_land_north`?
6. Why is Production the wrong org for session 1?

If any answer is still "because the document said so", reopen that session and look at the run id in your log. The run id is the part you did. The sentence is the part you understood.

## What good looks like

You can teach this to someone else with the CSV and the screen, without reading the file aloud:

- Land the file (job).
- Reject `NOT_A_DATE`.
- Count Ava's three encounters.
- Load North, then South.
- Match. One Ava.
- Rank sources. North phone wins.
- Do not schedule it, do not tag it, do not commit a flow YAML.

## Stop

You are done with the Meridian path. Next work skill is the real deploy practice in chapter 07, with a **different** tiny asset the pipeline owner agreed to, not this project.
