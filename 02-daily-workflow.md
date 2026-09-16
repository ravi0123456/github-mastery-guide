# Chapter 02 — Daily Workflow

**Goal:** Use Git from your Windows 11 PC with VS Code — clone, commit, push, pull, and GitHub Flow.

**Time:** about 3–4 hours  
**You need:** Windows 11, VS Code installed, internet, your GitHub account `ravi0123456`

---

## 1. Install Git on Windows

Git is the program that talks to GitHub from your computer.

### Steps

1. Open a browser and go to: `https://git-scm.com/download/win`
2. Download the Windows installer (64-bit for almost all modern PCs)
3. Run the installer
4. Prefer defaults if you are unsure
   - Editor: you can keep the default, or choose **Visual Studio Code** if offered
   - PATH: choose the option that says Git from the command line / third-party tools (default is usually fine)
5. Finish the installer

### Verify installation

1. Open **PowerShell** or **Command Prompt**
2. Run:

```text
git --version
```

You should see a version number, for example `git version 2.x.x`.

If you see “not recognized”, close the terminal, open a **new** one, and try again. If it still fails, restart VS Code / PC, then ask your assistant with a screenshot.

### Tell Git who you are (once per PC)

In PowerShell:

```text
git config --global user.name "Ravi Kambalapally"
git config --global user.email "YOUR_GITHUB_EMAIL@example.com"
```

Use the **same email** you use for GitHub (or the one shown in your GitHub email settings). This is not a password. It only labels your commits.

Check:

```text
git config --global --list
```

---

## 2. VS Code + Git — the daily desk

VS Code has a built-in **Source Control** panel (branch icon in the left activity bar).

You will mostly:

1. Edit files in VS Code
2. Stage and commit in Source Control (or terminal)
3. Push to GitHub

Terminal tip: in VS Code use **Terminal → New Terminal**. PowerShell is fine.

---

## 3. Clone a repository

**Clone** means: copy a GitHub repo onto your PC so you can work offline-ish and push changes later.

### Get the clone URL

1. Open https://github.com/ravi0123456/hello-world
2. Click the green **Code** button
3. Choose **HTTPS**
4. Copy the URL — it looks like:

```text
https://github.com/ravi0123456/hello-world.git
```

### Clone with VS Code

**Option A — VS Code UI**

1. Open VS Code
2. **View → Command Palette** (Ctrl+Shift+P)
3. Type `Git: Clone` and select it
4. Paste the HTTPS URL
5. Choose a folder on your PC, for example `Documents\GitHub`
6. When asked, open the cloned folder

**Option B — Terminal**

```text
cd Documents\GitHub
git clone https://github.com/ravi0123456/hello-world.git
cd hello-world
code .
```

(`code .` opens the folder in VS Code if the `code` command is installed.)

You now have a local copy. The GitHub copy is called the **remote** (usually nicknamed **`origin`**).

---

## 4. HTTPS and sign-in (token reminder)

When you first **push** (upload), Windows / Git may ask you to sign in to GitHub.

Important security rules:

- Prefer signing in through the **browser / GitHub login prompt** that VS Code or Git Credential Manager shows
- GitHub no longer accepts your account **password** for Git over HTTPS — if a personal access **token** is required, create it only from GitHub Settings when prompted by official tooling
- **Never** paste a token into a chat, README, screenshot, or random website
- **Never** commit a token into a file in the repo
- If a token leaks, **revoke** it immediately in GitHub → Settings → Developer settings → Personal access tokens

This guide will not walk you through pasting tokens into files. Use the normal VS Code / Git Credential Manager sign-in flow.

---

## 5. The core loop: status → add → commit → push

Open the cloned `hello-world` folder in VS Code. Make a small edit to any file (or create `practice/day2.txt`).

### In the terminal (inside the repo folder)

```text
git status
```

Shows what changed.

```text
git add .
```

Stages all changes (prepares them for the snapshot). To stage one file:

```text
git add practice/day2.txt
```

```text
git commit -m "Add day2 practice note"
```

Saves a snapshot **on your PC only**.

```text
git push
```

or, the first time on a new branch:

```text
git push -u origin BRANCH_NAME
```

Uploads commits to GitHub (`origin`).

### Same idea in VS Code UI

1. Open **Source Control**
2. Review changed files
3. Click **+** to stage
4. Type a commit message
5. Click **Commit**
6. Click **Sync** / **Push**

### Pull — get latest from GitHub

If you (or someone else) changed the repo on github.com:

```text
git pull
```

Habit: **pull before you start new work**, especially on shared branches.

---

## 6. Handy commands cheat sheet

| Command | Meaning |
|---------|---------|
| `git status` | What changed? What’s staged? |
| `git add .` | Stage everything |
| `git commit -m "message"` | Save snapshot locally |
| `git push` | Upload to GitHub |
| `git pull` | Download latest from GitHub |
| `git log --oneline` | Short history |
| `git branch` | List local branches |
| `git checkout -b name` | Create and switch to new branch |
| `git switch main` | Go back to main |

You do not need to memorise all of these on Day 1. Keep this table open.

---

## 7. .gitignore — what not to upload

A file named `.gitignore` tells Git which files to skip.

Common ignores:

- `node_modules/` (huge dependency folders)
- `.env` (environment secrets)
- `*.log`
- OS junk like `Thumbs.db` or `.DS_Store`
- Build output folders

Example `.gitignore` content:

```text
# secrets — never commit
.env
.env.*

# dependencies
node_modules/

# logs
*.log

# Windows
Thumbs.db
Desktop.ini
```

**Office rule:** if a file has passwords, API keys, or tokens — it belongs in `.gitignore`, not in the repo.

---

## 8. Sensible project folder structure

A simple beginner-friendly layout:

```text
my-project/
  README.md
  .gitignore
  src/           # your code
  docs/          # notes / design
  practice/      # learning files (optional)
```

Rules of thumb:

- Keep the root tidy
- Put code in clear folders
- Always have a README
- Always have a `.gitignore` before you add secrets by mistake

---

## 9. GitHub Flow (the office pattern)

**GitHub Flow** is the standard daily pattern:

1. Start from up-to-date `main`
2. Create a **branch** for your work
3. Make commits on that branch
4. **Push** the branch to GitHub
5. Open a **Pull Request (PR)**
6. Get review / fix comments
7. **Merge** into `main`
8. Delete the branch (optional cleanup)

### Try it on hello-world

In the terminal, inside your local clone:

```text
git switch main
git pull
git switch -c feature/add-day2-note
```

Edit or create a file, then:

```text
git add .
git commit -m "Add day2 workflow practice note"
git push -u origin feature/add-day2-note
```

On GitHub, open https://github.com/ravi0123456/hello-world — you should see a banner to **Compare & pull request**. Click it, write a short title, and create the PR.

You will practise reviewing and merging properly in Chapter 03. For now, creating the PR is the win.

---

## 10. Common errors and fixes

### `git: command not found` / not recognized

- Git not installed, or terminal opened before install finished
- Fix: reinstall Git, open a **new** terminal, run `git --version`

### `Please tell me who you are`

- You have not set `user.name` / `user.email`
- Fix: run the `git config --global` commands from Section 1

### Authentication failed / access denied on push

- Sign-in expired or wrong account
- Fix: sign in again via Git Credential Manager / VS Code GitHub login
- Do not paste tokens into the repo

### `Updates were rejected` / failed push because remote has new commits

- GitHub has commits you do not have locally
- Fix:

```text
git pull
```

Resolve any conflicts if asked (Chapter 03 covers conflicts). Then:

```text
git push
```

### You committed to `main` by mistake (learning only)

While learning on your own practice repo, this is not a disaster. In offices, avoid it — use a branch.

### Detached HEAD (rare for beginners)

Usually means you checked out a commit instead of a branch. Switch back:

```text
git switch main
```

### Merge conflict messages

Git found overlapping edits. Do not panic. Open the listed files, look for conflict markers `<<<<<<<`, fix the content, then add and commit. Details in Chapter 03.

---

## Practice tasks (Chapter 02)

**Practice 1 — Install & verify**
1. Confirm `git --version` works.
2. Set `user.name` and `user.email`.

**Practice 2 — Clone**
1. Clone https://github.com/ravi0123456/hello-world.git into a clear folder.
2. Open it in VS Code.

**Practice 3 — Local commit + push**
1. Create `practice/day2.md` with notes from this chapter.
2. `status` → `add` → `commit` → `push` to GitHub.
3. Refresh the repo page and confirm the file is online.

**Practice 4 — Branch + PR**
1. Create branch `feature/add-day2-note`.
2. Make one small change, commit, push.
3. Open a Pull Request on GitHub.

**Practice 5 — .gitignore**
1. Add a `.gitignore` that ignores `.env` and `*.log`.
2. Commit and push it.

**Practice 6 — Pull**
1. Edit a file on github.com (website).
2. Run `git pull` locally and confirm the change arrives.

Tick the Daily workflow items in [README.md](README.md).

---

## Checkpoint

You should now be able to:

- Install and verify Git on Windows
- Clone with the green **Code** button (HTTPS)
- Use status / add / commit / push / pull
- Create a feature branch and open a PR
- Explain why `.gitignore` and tokens matter

**Next →** [Chapter 03 — Office & team](03-office-team.md)
