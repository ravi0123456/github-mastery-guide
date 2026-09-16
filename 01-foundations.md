# Chapter 01 — Foundations

**Goal:** Feel comfortable on github.com — repos, files, commits, and the idea of branches.

**Time:** about 2–3 hours  
**Practice repo:** [ravi0123456/hello-world](https://github.com/ravi0123456/hello-world)

---

## 1. Your GitHub account

You already have an account:

- **Username:** `ravi0123456`
- **Profile:** https://github.com/ravi0123456

Your profile page shows:

- Your photo / avatar
- Repositories (projects)
- Activity (commits and other work over time)

Think of GitHub as a **safe online home for your project folders**, with a full history of every change.

### What is Git vs GitHub?

| Term | Simple meaning |
|------|----------------|
| **Git** | The tool on your computer that tracks file changes |
| **GitHub** | The website (and company) that stores Git projects online so teams can work together |

You will use both. This chapter focuses on the **website**. Chapter 02 adds Git on your Windows PC.

---

## 2. What is a repository (repo)?

A **repository** (short: **repo**) is one project — like a project folder with a memory of every change.

Examples on your account:

- `hello-world` — your practice project
- `github-mastery-guide` — this guide

Each repo has:

- Files and folders
- A history of **commits** (saved snapshots)
- Settings (who can see it, who can edit it)
- Often a **README.md** file that explains the project

### How to open a repo

1. Go to https://github.com/ravi0123456
2. Click the **Repositories** tab
3. Click **hello-world**

Or open it directly: https://github.com/ravi0123456/hello-world

---

## 3. Public vs Private

| Visibility | Who can see it | Typical use |
|------------|----------------|-------------|
| **Public** | Anyone on the internet | Learning, open source, portfolios, this guide |
| **Private** | Only you + people you invite | Company code, personal projects with secrets |

**Office note:** Most company work is **Private**. Your learning repos can stay **Public**.

You can change visibility later under:

**Repo → Settings → General → Danger Zone → Change repository visibility**

(Only change this when you understand what you are sharing.)

---

## 4. The README file

Almost every repo has a file named `README.md`.

- It is the **front page** of the repo
- GitHub shows it below the file list
- `.md` means **Markdown** — plain text with light formatting (`#` headings, lists, links)

A good README answers:

1. What is this project?
2. How do I run or use it?
3. Who is it for?

You do not need fancy writing. Clear and short is best.

---

## 5. Commits and history

A **commit** is a saved snapshot of your project with a short message explaining what changed.

Examples of good commit messages:

- `Add introduction paragraph to README`
- `Fix typo in contact email`
- `Create notes.txt for practice`

Bad messages (avoid):

- `update`
- `asdf`
- `changes`

### Viewing history on GitHub

1. Open https://github.com/ravi0123456/hello-world
2. Near the top of the file list, click **commits** (or the clock / history control showing commit count)
3. Click any commit to see **what files changed** and the **diff** (green = added, red = removed)

This history is why teams trust GitHub: you can always see who changed what, and when.

---

## 6. Editing a file on github.com

You can edit without installing anything. Perfect for Day 1.

### Steps

1. Open https://github.com/ravi0123456/hello-world
2. Click a file (for example `README.md`)
3. Click the **pencil** icon (Edit this file)
4. Change some text — for example add a line: `Practised editing on GitHub — Ravi`
5. Scroll down to **Commit changes**
6. Write a clear commit message, for example: `Add practice note to README`
7. Leave “Commit directly to the `main` branch” selected for now
8. Click **Commit changes**

You just made a **commit** on the **main** branch.

> Later (Chapter 02–03) you will learn to use branches and Pull Requests instead of committing straight to `main`. Offices prefer that workflow.

---

## 7. Creating a new file on github.com

1. Open the repo home: https://github.com/ravi0123456/hello-world
2. Click **Add file** → **Create new file**
3. Type a file name, for example: `practice/notes.txt`
   - The `/` creates a folder named `practice`
4. Type some content, for example:

```text
My first new file on GitHub
Date: practice day 1
```

5. Commit with a message like: `Add practice notes file`
6. Confirm the file appears in the repo

---

## 8. Branches — the big idea

A **branch** is a parallel line of work.

- **`main`** — the default, “official” line of the project (sometimes older repos use `master`)
- **Feature branches** — your temporary workspaces, for example `feature/add-notes`

Analogy:

- `main` is the clean notebook everyone trusts
- A branch is a photocopy where you try ideas
- When the idea is good, you merge the photocopy back into the notebook (via a Pull Request — Chapter 03)

For now, remember:

1. Almost every repo has a default branch called **`main`**
2. You *can* commit on `main` while learning
3. In offices, you usually **create a branch**, do work, then open a **Pull Request**

### Seeing the current branch

On the repo page, look for a branch dropdown (often shows `main`). Click it to see other branches if any exist.

---

## 9. Creating a branch on github.com (preview)

You will use this heavily from Chapter 02 onward. Try it once now:

1. Open https://github.com/ravi0123456/hello-world
2. Click the branch dropdown (`main`)
3. Type a new name: `practice/foundations`
4. Click **Create branch: practice/foundations**
5. You are now viewing that branch
6. Edit or add a small file on this branch and commit
7. Notice GitHub may show a banner offering to open a **Compare & pull request** — you can ignore it until Chapter 03, or click it for a preview

Switch back to `main` from the branch dropdown when you want the main view again.

---

## 10. Repo page tour (quick map)

On any repo page you will often see:

| Area | What it is |
|------|------------|
| **Code** tab | Files and folders |
| **Issues** | Task / bug / discussion list (Chapter 03) |
| **Pull requests** | Proposed changes waiting for review (Chapter 03) |
| **Actions** | Automated checks / CI (Chapter 04) |
| **Settings** | Visibility, collaborators, protection (careful here) |
| Green **Code** button | Clone URL for your computer (Chapter 02) |

You do not need every tab today. Knowing the map reduces fear.

---

## Practice tasks (Chapter 01)

Do these on **[hello-world](https://github.com/ravi0123456/hello-world)**.

**Practice 1 — Explore**
1. Open your profile and list your repositories.
2. Open `hello-world` and click through **Code**, then glance at **Issues** and **Pull requests** (they may be empty — that is fine).

**Practice 2 — Edit README**
1. Edit `README.md` on github.com.
2. Add one short line about yourself as a learner.
3. Commit with a clear message.

**Practice 3 — New file**
1. Create `practice/day1.md` with three bullet points: what you learned today.
2. Commit it.

**Practice 4 — History**
1. Open the commits list.
2. Open your latest commit and look at the green/red diff.

**Practice 5 — Branch**
1. Create branch `practice/foundations`.
2. Add or edit one small file on that branch.
3. Switch back to `main` and notice that file may not appear on `main` yet (because it lives on the other branch).

When finished, tick the Foundations items in [README.md](README.md).

---

## Checkpoint — you should now be able to

- Explain repo, README, commit, public/private, and `main` in your own words
- Edit and create files on github.com
- Read commit history
- Create a simple branch

**Next →** [Chapter 02 — Daily workflow](02-daily-workflow.md) (Git + VS Code on Windows)
