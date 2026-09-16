# Chapter 03 — Office & Team

**Goal:** Work like a teammate — Issues, Pull Requests, reviews, merge, and “done” habits.

**Time:** about 2–3 hours  
**Practice repo:** [ravi0123456/hello-world](https://github.com/ravi0123456/hello-world)

---

## 1. Why teams use Issues and Pull Requests

In an office, people rarely change `main` directly.

Typical path:

1. Someone opens an **Issue** (“what needs doing”)
2. You create a **branch** and do the work
3. You open a **Pull Request** (“please review my solution”)
4. Teammates review
5. You fix comments
6. Someone **merges** into `main`

This keeps history clear and reduces accidents.

---

## 2. Issues — track the work

An **Issue** is a ticket: bug, task, idea, or question.

### Create an Issue

1. Open https://github.com/ravi0123456/hello-world
2. Click the **Issues** tab
3. Click **New issue**
4. Title: clear and specific, for example `Add a CONTRIBUTING tip to README`
5. Description: what / why / how you’ll know it’s done
6. Click **Submit new issue**

### Good Issue titles

| Weak | Strong |
|------|--------|
| help | Login button does nothing on Edge |
| update readme | README: add setup steps for Windows 11 |
| bug | Fix crash when saving empty form |

### Issue body template (copy/adapt)

```markdown
## What
Short description of the task or bug.

## Why
Why this matters (user impact / learning goal).

## Done when
- [ ] Concrete check 1
- [ ] Concrete check 2
```

---

## 3. Labels

**Labels** are coloured tags that sort work: `bug`, `enhancement`, `documentation`, `good first issue`.

On an Issue page:

1. Look at the right sidebar → **Labels**
2. Add something like `documentation` or `enhancement`

In bigger teams, labels help managers and developers filter work. On your practice repo, use 1–2 simple labels so the habit feels natural.

---

## 4. Pull Requests (PRs)

A **Pull Request** asks: “Please pull my branch’s changes into another branch (usually `main`).”

### Create a PR (full path)

1. Make sure your branch is pushed (Chapter 02)
2. Open the repo on GitHub
3. Click **Compare & pull request**, or go to **Pull requests → New pull request**
4. Set base branch: `main`
5. Set compare branch: your feature branch
6. Title: match the work, for example `Add CONTRIBUTING tip to README`
7. Description: what changed and how to test
8. Link the Issue if any: write `Closes #1` (use your real issue number)
9. Click **Create pull request**

### Good PR description habit

```markdown
## Summary
- What I changed in plain language

## Test plan
- [ ] Steps I tried
- [ ] What a reviewer should check

## Notes
Anything risky or unfinished
```

---

## 5. Code review etiquette

Review is about **the code and the goal**, not the person.

### If you are the author (you opened the PR)

- Keep the PR focused — one purpose is better than ten mixed changes
- Write a clear description
- Respond to comments politely and specifically
- Push new commits to the **same branch** to update the PR automatically
- Say thank you; ask clarifying questions when needed

### If you are the reviewer

- Be kind and precise: “Consider renaming X because…” beats “This is wrong”
- Separate **must-fix** from **nice-to-have**
- Approve when the change is safe and meets the Issue
- Request changes when something important is broken or unclear

### Phrases that work well

- “Suggestion: …”
- “Could we add a short comment here for future readers?”
- “I tested steps 1–3; looks good to merge.”
- “Blocking: this breaks the build because …”

---

## 6. Requesting reviewers

On the PR page, right sidebar → **Reviewers**.

- In a company org, you pick teammates or a team name
- On your solo practice repo, you may have no reviewers — that is fine
- Still practise writing the PR as if a senior will read it tomorrow

Office tip: if your team has a rule like “at least one approval”, follow it even when you feel rushed.

---

## 7. Merge

When the PR is approved (and checks are green, if any):

1. Open the PR
2. Click **Merge pull request** (or the merge method your team prefers)
3. Confirm
4. Optionally **Delete branch** to keep the repo tidy

### Merge styles (concepts only)

| Method | Idea |
|--------|------|
| **Create a merge commit** | Keeps PR history clearly |
| **Squash and merge** | Combines your commits into one tidy commit on main |
| **Rebase and merge** | Replays commits linearly |

Beginners: use whatever the green button offers on your repo. In offices, follow the team default (often **squash**).

After merge, update your local PC:

```text
git switch main
git pull
```

---

## 8. Conflicts (calm version)

A **merge conflict** means Git needs your help choosing between two edits to the same lines.

You may see conflict markers in a file:

```text
<<<<<<< HEAD
your current branch version
=======
incoming version
>>>>>>> branch-name
```

What to do:

1. Open the file in VS Code
2. Decide the correct final text (keep one side, or combine)
3. Remove the marker lines completely
4. `git add` the fixed file
5. `git commit` (or complete the merge / continue the PR update)
6. `git push`

If you feel stuck, screenshot the conflicted file and ask your assistant — do not randomly delete large chunks.

---

## 9. Branch protection (concept)

**Branch protection** is a Settings rule that can require:

- Pull Requests before merging to `main`
- At least one approving review
- Passing status checks (CI)

Path (for awareness): **Repo → Settings → Branches → Branch protection rules**

On a tiny personal practice repo you may not enable this. In offices, `main` is often protected — that is why everyone uses PRs.

---

## 10. Collaborators vs organizations

| Concept | Meaning |
|---------|---------|
| **Collaborator** | A person you invite to your personal repo |
| **Organization (org)** | A company/team account that owns many repos |
| **Team** | A group inside an org with shared permissions |

Invite path on a personal repo: **Settings → Collaborators**.

In jobs, you usually join the **company org** and get access to private repos — you do not “own” those repos personally.

---

## 11. Forks vs branches

| | **Branch** | **Fork** |
|---|------------|----------|
| Where it lives | Inside the **same** repo | A **copy of the whole repo** under your account |
| Typical use | Daily feature work with teammates | Contributing to someone else’s project you cannot push to |
| Example | `feature/login` on company repo | You fork `some-org/tool` → `ravi0123456/tool`, then open a PR upstream |

For office work on repos you already can push to, you usually need **branches**, not forks.

---

## 12. What “done” looks like in a team PR

Your PR is truly done when:

- [ ] It solves the Issue / request (not half of a different topic)
- [ ] Description explains summary + how to test
- [ ] Linked Issue is referenced if one exists
- [ ] CI checks are green (if the repo has them)
- [ ] Review comments are addressed or discussed
- [ ] Approval received (if required)
- [ ] Merged to the correct base branch
- [ ] Local `main` updated with `git pull`
- [ ] Feature branch deleted (if team prefers cleanup)
- [ ] Issue closed (auto-close with `Closes #n` or manual)

Print this mental checklist — seniors notice people who finish cleanly.

---

## Practice tasks (Chapter 03)

Use [hello-world](https://github.com/ravi0123456/hello-world).

**Practice 1 — Issue**
1. Create an Issue describing a small README improvement.
2. Add a label.

**Practice 2 — Branch + PR**
1. Create a branch named like `feature/issue-1-readme` (use your issue number).
2. Make the change locally, commit, push.
3. Open a PR that says `Closes #N` in the description.

**Practice 3 — Self-review**
1. Read your own PR Files changed tab.
2. Leave one comment on your PR as if advising a teammate (yes, you can comment on your own PR for practice).

**Practice 4 — Merge**
1. Merge the PR.
2. Delete the branch on GitHub.
3. Locally: `git switch main` then `git pull`.
4. Confirm the Issue closed.

**Practice 5 — Reflection**
1. In `practice/day3.md`, write five lines: what felt confusing, what felt clear.
2. Commit via a new short-lived branch + PR (extra reps build muscle memory).

Tick the Office & team items in [README.md](README.md).

---

## Checkpoint

You should now be able to:

- Open clear Issues with labels
- Open a PR linked to an Issue
- Behave well in review (author or reviewer)
- Merge and clean up
- Explain collaborators, orgs, forks vs branches
- Describe what “done” means for a team PR

**Next →** [Chapter 04 — Pro tools](04-pro-tools.md)
