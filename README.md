# Deploying this to GitHub Pages

Plain HTML — no build step, no Jekyll, nothing to install. Five files go up:

```
index.html      course.html      practice.html      assets/site.css      README.md
```

**Two files must never go up.** They are already in `.gitignore` and Step 5 verifies it:

| File | Why |
|---|---|
| `PASSWORDS.txt` | opens every group's results |
| `student-reports.html` | the printable cards — every name and mark in cleartext |

---

# Part 1 · First time only

### 1 · Make the repository on GitHub

Go to **github.com → + (top right) → New repository**.

- **Name:** anything, e.g. `cse1121-1am`
- **Public** is fine. Results are AES-encrypted and no name, ID or mark appears in cleartext.
- **Do NOT** tick "Add a README", "Add .gitignore" or "Choose a license" — leave it completely empty,
  or step 6 will be rejected.
- Click **Create repository**, then leave that page open. You will need the URL.

### 2 · Open Terminal in the site folder

```bash
cd "/Users/abuumamah/Documents/IIUC_DSA_Track/Au26_CSE_1121_22/site"
```

### 3 · Tell git who you are (once per machine — skip if you have done it before)

```bash
git config --global user.name  "Jamil As-ad"
git config --global user.email "jamil@iiuc.ac.bd"
```

### 4 · Start the repository

```bash
git init
git branch -M main
```

### 5 · Stage the files, and CHECK before committing

```bash
git add -A
git status --short
```

**You must see exactly these six lines and nothing else:**

```
A  .gitignore
A  README.md
A  assets/site.css
A  course.html
A  index.html
A  practice.html
```

If `PASSWORDS.txt` or `student-reports.html` appears, stop and run
`git rm --cached PASSWORDS.txt student-reports.html` before going on.

### 6 · Commit and push

Replace `<username>` and `<repo>` with your own:

```bash
git commit -m "Quiz 1 and Lab Test 1 results"
git remote add origin https://github.com/<username>/<repo>.git
git push -u origin main
```

GitHub will ask for your username and password. **The password is not your GitHub password** —
it is a Personal Access Token:

> github.com → your avatar → **Settings** → **Developer settings** (very bottom of the left menu) →
> **Personal access tokens** → **Tokens (classic)** → **Generate new token (classic)** →
> tick **repo** → Generate → **copy it now, it is shown only once** → paste it as the password.

### 7 · Turn on Pages

In your repository: **Settings → Pages** (left menu).

- **Source:** Deploy from a branch
- **Branch:** `main`
- **Folder:** `/ (root)`
- **Save**

Wait about a minute, then reload that page. It will show a green tick and your address:

```
https://<username>.github.io/<repo>/
```

### 8 · Test it before you share it

1. Open the link on your phone, not just your laptop.
2. Enter a group password from `PASSWORDS.txt` — you should see only that group.
3. Enter the teacher password — you should see all seven groups and the filter chips.
4. Try a wrong password — it must be refused.
5. Tap **Copy** on one of the AI prompts and paste it somewhere to confirm it copied.

### 9 · Share it

Send the class the link. Send each group leader **only their own line** from `PASSWORDS.txt`,
privately — not into the class group.

---

# Part 2 · Every time after a new test

From the project folder, rebuild and then push:

```bash
cd "/Users/abuumamah/Documents/IIUC_DSA_Track/Au26_CSE_1121_22"
python3 marking/build.py
python3 marking/diagnose.py
python3 marking/compose.py
python3 marking/site.py

cd site
git add -A
git status --short          # check again — only the five files plus .gitignore
git commit -m "After Lab Test 2"
git push
```

Live again in under a minute. **The passwords do not change**, so there is nothing to hand out
again.

---

# If something is wrong

| What you see | What it is |
|---|---|
| **404 on the Pages link** | First deploy takes a few minutes. Check Settings → Pages shows a green tick, and that Branch is `main` and Folder is `/ (root)`. |
| **Page loads but has no styling** | `assets/site.css` was not committed. Run `git add assets && git commit -m css && git push`. |
| **A password is refused** | You pushed an older `index.html` than the passwords belong to. Rebuild with `python3 marking/site.py` and push again. The passwords themselves never change. |
| **`git push` rejected — "updates were rejected"** | The repository was not empty. `git pull --rebase origin main` then push again. |
| **"Support for password authentication was removed"** | You typed your GitHub password. Use a Personal Access Token — see step 6. |
| **You accidentally pushed PASSWORDS.txt** | Run `python3 marking/site.py --rotate` to issue new passwords, then push. Assume the old ones are public. |

---

# The one rule

Before **every** push, run `git status --short` and read it. If you only ever check one thing,
check that `PASSWORDS.txt` is not in that list.
