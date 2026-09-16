# Glossary

Simple one-line definitions. Skim anytime something feels fuzzy.

| Term | Meaning |
|------|---------|
| **Git** | Software on your computer that tracks versions of files and project history. |
| **GitHub** | Website/service that hosts Git repositories online for collaboration. |
| **Repository (repo)** | One project’s files plus its full change history. |
| **README** | Front-page file (usually `README.md`) explaining the project. |
| **Markdown (.md)** | Plain text format with simple headings, lists, and links. |
| **Commit** | A saved snapshot of changes with a message. |
| **Commit message** | Short text explaining why/what you changed in a commit. |
| **History** | The list of past commits for a repo or file. |
| **Diff** | A view of what lines were added (green) or removed (red). |
| **Branch** | A parallel line of development; a movable pointer to commits. |
| **main** | The default primary branch in most modern repos. |
| **master** | Older default branch name; many teams renamed it to `main`. |
| **Feature branch** | Short-lived branch for one task or feature. |
| **Checkout / switch** | Move your working files to another branch (or commit). |
| **Clone** | Copy a remote repo onto your computer. |
| **Remote** | A GitHub (or other) copy of the repo your local Git talks to. |
| **origin** | The default name for the remote you cloned from. |
| **Push** | Upload your local commits to the remote (GitHub). |
| **Pull** | Download and integrate new commits from the remote. |
| **Fetch** | Download remote updates without merging them yet. |
| **Stage (add)** | Mark changes to include in the next commit (`git add`). |
| **Working tree** | Your actual files on disk as you edit them. |
| **.gitignore** | File listing paths Git should not track. |
| **HTTPS clone URL** | Repo address starting with `https://github.com/...` used to clone/push. |
| **SSH** | Alternate secure connection method using keys (optional/advanced). |
| **Token (PAT)** | Special access secret for tools; never commit or share it. |
| **2FA** | Two-factor authentication — password plus a second proof. |
| **Fork** | Your copy of someone else’s entire repository under your account. |
| **Pull Request (PR)** | A request to merge one branch into another, usually with review. |
| **Merge** | Combine one branch’s changes into another branch. |
| **Conflict** | Overlapping edits Git cannot auto-merge; you must choose the result. |
| **Issue** | A tracked task, bug, or discussion item in a repo. |
| **Label** | A tag on Issues/PRs used for filtering (bug, docs, etc.). |
| **Review** | Feedback on a PR before merge. |
| **Approve** | Reviewer decision that the PR is good to merge. |
| **Request changes** | Reviewer decision that something must be fixed first. |
| **Collaborator** | Person invited to work on a personal repository. |
| **Organization (org)** | Shared GitHub account for a company or group. |
| **Branch protection** | Rules that block direct pushes / require reviews on important branches. |
| **GitHub Flow** | Branch → commits → push → PR → review → merge into `main`. |
| **CI** | Continuous Integration — automatic checks on each push/PR. |
| **GitHub Actions** | GitHub’s built-in automation/CI system. |
| **Workflow** | An Actions automation defined in `.github/workflows/`. |
| **Check** | A CI status result shown on a commit or PR. |
| **Tag** | A named bookmark on a commit, often a version like `v1.0.0`. |
| **Release** | GitHub publication of a version (notes + optional files) based on a tag. |
| **SemVer** | Version style major.minor.patch (for example 1.4.2). |
| **Secret** | Sensitive value (password, key, token) that must not be committed. |
| **Projects** | GitHub boards for planning Issues/PRs (To do / Doing / Done). |
| **VS Code** | Popular code editor; works well with Git on Windows. |
| **Source Control** | VS Code panel for viewing changes, commits, and sync. |
| **Upstream** | The original repo you forked from (when contributing via fork). |
| **Base branch** | The branch a PR wants to merge **into** (often `main`). |
| **Compare branch** | The branch with your new commits in a PR. |
| **Hotfix** | Urgent fix for a serious production problem. |
| **Squash merge** | Merge method that combines PR commits into one commit on the base. |

---

## Quick memory aids

- **Git** = history tool on your PC  
- **GitHub** = team home for that history online  
- **Commit** = save snapshot  
- **Push** = share snapshot  
- **Pull** = receive snapshots  
- **Branch** = safe workspace  
- **PR** = “please review and merge”  
- **Issue** = “please track this work”

Back to the guide home: [README.md](README.md)
