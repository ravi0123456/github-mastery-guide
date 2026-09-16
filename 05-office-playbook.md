# Chapter 05 — Office Playbook

**Goal:** A practical, reusable checklist for real office work — from starting a feature to hotfix and asking seniors well.

**Time:** 1–2 hours to learn; then reuse forever.

Print this page mentally. Or keep the file open beside VS Code.

---

## 1. Before you write code

1. Read the Issue / ticket carefully
2. Confirm acceptance criteria (“done when…”)
3. Ask early if the requirement is unclear — a 5-minute question saves a day
4. Pull latest `main`
5. Create a branch with a clear name

```text
git switch main
git pull
git switch -c feature/short-description
```

---

## 2. Branch naming (simple office style)

Pick one style and stay consistent:

| Type | Pattern | Example |
|------|---------|---------|
| Feature | `feature/…` | `feature/add-export-button` |
| Bug fix | `fix/…` | `fix/login-null-crash` |
| Hotfix | `hotfix/…` | `hotfix/payment-timeout` |
| Chore / docs | `chore/…` or `docs/…` | `docs/update-setup-readme` |

Optional: include ticket id if your company uses one:

```text
feature/JIRA-1234-add-export-button
```

Avoid: `ravi-temp`, `asdf`, `new`, `test1`.

---

## 3. Commit message style

Write messages for your teammates (and future you).

**Format that works well:**

```text
Short summary in present tense (max ~72 chars)

Optional body: why this change was needed.
```

**Examples:**

```text
Add export button to reports page
```

```text
Fix null crash when user email is missing

Guards the profile serializer before reading email.
```

**Avoid:**

```text
update
fix
WIP
final final 2
```

Small, focused commits are easier to review than one giant “all changes” commit — but do not create noise commits like “fix typo” twenty times if your team prefers squashing; follow team culture.

---

## 4. While developing

Daily loop:

1. Pull / rebase according to team rules if `main` moved (ask seniors which they prefer)
2. Make changes
3. Run the project / tests locally if they exist
4. Commit with a clear message
5. Push your branch often (backup + visible progress)

```text
git add .
git status
git commit -m "Describe the change"
git push
```

First push of a new branch:

```text
git push -u origin feature/short-description
```

---

## 5. PR template habits

Even if the repo has no formal template, write one.

```markdown
## Summary
- What problem this solves
- Key changes

## Test plan
- [ ] Steps you ran locally
- [ ] Screenshots if UI changed
- [ ] Edge cases checked

## Risk
- Low / Medium / High — and why

## Issue
Closes #123
```

UI tip: if your company uses PR templates, they appear automatically — fill every section honestly. “N/A” is better than silence when a section truly does not apply.

---

## 6. Opening the PR

1. Push branch
2. Open Compare & pull request
3. Check base branch is correct (`main` or `develop` — **ask** if unsure)
4. Self-review the **Files changed** tab before inviting anyone
5. Request reviewers
6. Link Issue
7. Share the PR link in your team chat if that is the culture

Self-review catches 50% of comments before seniors see them.

---

## 7. Responding to review

When comments arrive:

1. Read all comments once before changing code
2. Group easy fixes vs questions
3. Push new commits to the **same branch** (PR updates automatically)
4. Reply to each meaningful comment: “Fixed in latest commit” or “Good point — I kept X because …”
5. Re-request review if your team does that
6. Stay calm — review is normal, not a personal attack

If you disagree:

- Explain trade-offs politely
- Offer a follow-up Issue if the request is out of scope
- Accept the team decision after discussion

---

## 8. After merge

```text
git switch main
git pull
```

Then:

- Delete local branch if you like: `git branch -d feature/short-description`
- Confirm Issue closed
- Note anything for QA / product if needed
- Start the next ticket cleanly from updated `main`

---

## 9. Hotfixes (urgent production fixes)

Hotfixes are emergency repairs.

Typical pattern (companies vary — **follow your team**):

1. Confirm the bug and impact with a senior if possible
2. Branch from the correct production base (often `main`)

```text
git switch main
git pull
git switch -c hotfix/short-description
```

3. Make the **smallest** safe fix
4. Add/adjust a test if the project has tests
5. Open a PR with **Risk** and **Test plan** filled carefully
6. Ask for fast review in the agreed channel
7. Merge only through the approved process
8. Watch CI / deployment according to team runbooks
9. Write a short note: what broke, what fixed it, how to prevent repeat

Do not mix refactors into hotfixes.

---

## 10. What to ask seniors (good questions)

Good questions show you tried:

| Instead of… | Ask… |
|-------------|------|
| “Code not working” | “I cloned repo X, ran command Y, got error Z (screenshot). I tried A and B. What should I check next?” |
| “Which branch?” | “For ticket 123, should base be `main` or `develop`?” |
| “Is this OK?” | “I considered approach A vs B; I chose A because … Does that match our pattern?” |
| “Merge conflict help” | “Conflict in `path/file.js` between my change and recent PR #45. Expected behaviour is … Can you confirm which side we keep?” |

Bring:

- Ticket link
- PR link
- Error text / screenshot
- What you already tried

---

## 11. Daily office checklist (copy/reuse)

### Start of day
- [ ] Check team chat / board for blockers
- [ ] `git switch main` && `git pull`
- [ ] Pick one Issue you own
- [ ] Create/continue feature branch

### During work
- [ ] Commit in logical chunks with clear messages
- [ ] Push branch regularly
- [ ] Keep PR description updated
- [ ] Do not commit secrets / `.env`

### Before asking for review
- [ ] Self-review Files changed
- [ ] Test plan filled
- [ ] CI green (or explained)
- [ ] Issue linked

### End of day
- [ ] Push your branch (never leave work only on one PC if avoidable)
- [ ] Leave a short status note if your team expects it
- [ ] List blockers for tomorrow morning

---

## 12. End-to-end practice (simulate an office feature)

Do this once on [hello-world](https://github.com/ravi0123456/hello-world):

**Practice — Full playbook drill**

1. Create Issue: “Add office playbook reflection notes”
2. Branch: `feature/playbook-reflection`
3. Add `practice/office-drill.md` with:
   - Branch name you used
   - Commit message you used
   - What you would ask a senior if stuck
4. Push and open PR with Summary + Test plan + `Closes #N`
5. Self-review; merge; pull `main`; delete branch
6. Tick remaining boxes in [README.md](README.md)

---

## 13. You are office-ready when…

You can, without panic:

- Clone a repo and run basic Git daily commands
- Create branches with clear names
- Write useful commits and PRs
- Respond to review comments professionally
- Avoid committing secrets
- Explain Issues, PRs, CI, and merge to a teammate
- Ask seniors sharp questions with evidence

Keep practising on `hello-world`. Real fluency comes from repetition, not from reading once.

---

## Related chapters

- Foundations: [01-foundations.md](01-foundations.md)
- Daily Git: [02-daily-workflow.md](02-daily-workflow.md)
- Team skills: [03-office-team.md](03-office-team.md)
- Pro tools: [04-pro-tools.md](04-pro-tools.md)
- Words: [glossary.md](glossary.md)

**Well done for reaching the playbook.** Use it on every feature.
