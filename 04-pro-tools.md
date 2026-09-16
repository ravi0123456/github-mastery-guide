# Chapter 04 — Pro Tools

**Goal:** Understand CI (Actions), Releases, security basics, Projects, and useful Settings — enough to sound and work office-ready.

**Time:** about 2 hours  
**Note:** You do not need to master every button. Learn the ideas and where they live.

---

## 1. GitHub Actions — what CI is

**CI** means **Continuous Integration**.

Simple meaning: when someone pushes code or opens a PR, robots automatically run checks — for example:

- Install dependencies
- Run tests
- Run a linter (style/quality tool)
- Build the app

On GitHub, those robots are often powered by **GitHub Actions**.

Why offices love CI:

- Catches broken code early
- Gives reviewers confidence (“checks are green”)
- Makes “it works on my machine” less common

You will see CI results on a PR as **checks** near the bottom / Conversation tab.

---

## 2. Simple workflow explanation

Actions are configured by YAML files in:

```text
.github/workflows/
```

Example idea (do **not** treat this as something you must paste blindly — understand the shape):

```yaml
name: CI
on:
  pull_request:
  push:
    branches: [ main ]

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: Run a simple check
        run: echo "CI ran successfully"
```

What this means in plain English:

1. **When** a PR opens or someone pushes to `main`
2. GitHub starts a clean temporary computer (`ubuntu-latest`)
3. It checks out your code
4. It runs your steps (here, just a message)

In real projects, steps run tests or builds instead of `echo`.

### Where to look in the UI

- Repo tab **Actions** — past workflow runs
- Inside a PR — check status (pending / success / failure)

If a check fails: open the failed run → read the log → fix the code or the workflow → push again.

**Practice (optional):** On `hello-world`, look at the **Actions** tab. It may be empty. That is fine — you now know what it is for.

---

## 3. Releases and tags

A **tag** is a named bookmark on a commit, often a version like `v1.0.0`.

A **Release** is a GitHub page built around a tag — notes for users, and sometimes downloadable files.

Typical use:

- Open source libraries publish versions
- Apps ship installers
- Teams mark “what went to production”

### View / create (awareness)

1. Repo → right side **Releases** (or **Create a new release**)
2. Choose a tag like `v0.1.0`
3. Write release notes (what changed)
4. Publish

Version style many teams use (**SemVer** idea):

- `v1.2.3` → major . minor . patch
- Bug fix → patch (`1.2.3` → `1.2.4`)
- New feature, compatible → minor (`1.2.4` → `1.3.0`)
- Breaking change → major (`1.3.0` → `2.0.0`)

For learning, one practice release on `hello-world` is enough to demystify the page.

---

## 4. Security habits that matter at work

### 4.1 Turn on 2FA (two-factor authentication)

2FA means password + a second proof (authenticator app / security key).

Path: GitHub profile **Settings → Password and authentication → Two-factor authentication**

Offices often **require** 2FA. Enable it on your personal account early.

### 4.2 Tokens (Personal Access Tokens)

A **token** is a special password for tools/scripts. Rules:

- Create tokens only when a trusted official tool needs them
- Give the **minimum** permissions needed
- Store them in a password manager or OS credential helper — **not** in source code
- Never commit tokens, never paste them into READMEs, Issues, or chat screenshots
- If leaked: **revoke immediately** (Settings → Developer settings → Personal access tokens)

This guide will not ask you to generate a token and put it anywhere. Prefer browser / VS Code sign-in flows.

### 4.3 Never commit secrets

Secrets include:

- API keys
- Database passwords
- Private certificates
- `.env` files with real values
- Access tokens

Prevention:

- Add `.env` to `.gitignore` **before** creating the file
- Use GitHub **Secrets** (repo Settings → Secrets and variables → Actions) for CI — values are masked
- If you commit a secret by mistake: revoke/rotate the secret first, then clean history with senior help — assume the secret is burned once it was pushed

### 4.4 Dependabot / security alerts (awareness)

GitHub may warn about vulnerable dependencies. In offices, someone (often you) opens or reviews PRs that bump packages. Read the PR diff; do not blindly merge if you do not understand the change — ask a senior.

---

## 5. Projects board (brief)

**GitHub Projects** is a board (To do / In progress / Done) linked to Issues and PRs.

Useful when:

- A small team wants a shared kanban board
- You want to see workload at a glance

Path ideas:

- Repo → **Projects** tab, or
- User/org level Projects

You do not need a fancy board to be productive. Issues + PRs already carry most teams. Use Projects when your office does.

---

## 6. Useful Settings (map, not a tour of every toggle)

On a repo: **Settings**

| Area | Why it matters |
|------|----------------|
| **General** | Default branch name, visibility, features (Wiki, Issues) |
| **Collaborators** | Who can push |
| **Branches** | Protection rules for `main` |
| **Secrets and variables** | Safe values for Actions |
| **Actions** | Permission for workflows |
| **Pages** | Host a simple website from the repo (optional learning later) |

On your **profile** Settings:

| Area | Why it matters |
|------|----------------|
| **Emails** | Commit email matching |
| **Password and authentication** | 2FA |
| **SSH and GPG keys** | Optional advanced login methods |
| **Developer settings** | Tokens — handle with care |

Do not change Danger Zone options casually (delete repo, transfer ownership).

---

## 7. README quality as a pro skill

Pros leave the next person a clear README:

1. What the project does
2. Prerequisites
3. Setup steps (Windows notes help a lot in India/office mixed environments)
4. How to run tests
5. How to contribute (branch + PR expectations)

Even internal private repos benefit. Future-you is also “the next person”.

---

## Practice tasks (Chapter 04)

**Practice 1 — Actions awareness**
1. Open the **Actions** tab on `hello-world` and on this guide repo.
2. Write 3 bullets in `practice/day4.md` explaining CI in your own words.

**Practice 2 — Security checklist**
1. Confirm whether 2FA is enabled on your account (Settings).
2. Confirm `.gitignore` ignores `.env`.
3. Skim Developer settings so you know where token revoke lives — do not create unused tokens “for fun”.

**Practice 3 — Release awareness**
1. Open **Releases** on `hello-world`.
2. Optionally create a learning release `v0.1.0` with one sentence of notes.

**Practice 4 — Settings map**
1. Open repo Settings and locate: General, Collaborators, Branches, Secrets.
2. Do not enable destructive options.

Commit your `practice/day4.md` through a branch + PR (keep practising the office path).

Tick Pro tools items in [README.md](README.md).

---

## Checkpoint

You should now be able to:

- Explain CI / Actions in one minute
- Describe Releases & tags at a high level
- Follow strong security habits (2FA, no secrets in git, revoke tokens)
- Know where Projects and key Settings live

**Next →** [Chapter 05 — Office playbook](05-office-playbook.md) (reuse every workday)
