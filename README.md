Vishwa — I’ll **walk through every scenario slowly, step by step**, explaining **what each person does**, **why the conflict happens**, **what Git is thinking internally**, and **exactly how to resolve it**.

Think of this as a **merge-conflict lab manual**, not just commands.

---

# 🧩 FIRST: How Git Thinks (Very Important)

Before scenarios, understand this **once**:

Git always compares **three versions**:

1. **Base** – the common ancestor commit
2. **HEAD** – current branch (usually `main`)
3. **Incoming** – branch you are merging

If Git **cannot auto-merge the differences**, it stops and asks **YOU** to decide.

---

# SCENARIO 1 — SAME LINE EDITED BY TWO PEOPLE

🔥 *Most common conflict in real teams*

---

## 🧪 What We Are Intentionally Creating

* Both people change **the exact same line**
* Git sees **two different truths**
* Git cannot choose → conflict

---

## STEP 0 — Initial State (Shared History)

Both P1 and P2 start from **same commit**:

`README.md`

```txt
Welcome to Git Merge Conflict Lab
```

This is the **BASE** version.

---

## STEP 1 — Branch Creation (No Conflict Yet)

### Why branches?

Each person works **independently**.

### P1

```bash
git checkout -b feature/p1-update
```

### P2

```bash
git checkout -b feature/p2-update
```

Both branches point to the **same base commit**.

---

## STEP 2 — Conflicting Changes Created

### P1 edits the line

```txt
Welcome to Git Merge Conflict Lab - Created by Person One
```

Git records:

* Base: original line
* P1 change: modified line

```bash
git commit -m "P1 updates welcome line"
```

---

### P2 edits the SAME line differently

```txt
Welcome to Git Merge Conflict Lab - Updated by Person Two
```

Git records:

* Base: original line
* P2 change: modified line (different content)

```bash
git commit -m "P2 updates welcome line"
```

---

## STEP 3 — P1 Merges First (No Conflict)

### Why no conflict?

Because `main` has **not changed yet**.

```bash
git checkout main
git merge feature/p1-update
```

Git sees:

* Base → P1 change
* Clean merge

✅ Success

---

## STEP 4 — P2 Merges After (💥 Conflict)

```bash
git checkout main
git pull origin main
git merge feature/p2-update
```

### Git compares:

* Base: original line
* HEAD: P1’s version
* Incoming: P2’s version

⚠️ **Both changed the same line differently**

Git stops.

---

## STEP 5 — Conflict Markers Explained

```txt
<<<<<<< HEAD
Welcome to Git Merge Conflict Lab - Created by Person One
=======
Welcome to Git Merge Conflict Lab - Updated by Person Two
>>>>>>> feature/p2-update
```

### Meaning:

* `<<<<<<< HEAD` → current branch (`main`)
* `=======` → separator
* `>>>>>>> feature/p2-update` → incoming branch

---

## STEP 6 — Resolution (Human Decision)

You decide what the final truth should be.

### Best Practice → Combine intent

```txt
Welcome to Git Merge Conflict Lab - Created by Person One & Updated by Person Two
```

---

## STEP 7 — Tell Git “Conflict Is Resolved”

```bash
git add README.md
git commit -m "Resolve merge conflict in README"
```

✅ Conflict resolved.

---

# SCENARIO 2 — ONE DELETES, OTHER MODIFIES

🔥 *Very common during refactoring*

---

## 🧪 What We Are Creating

* P1 removes a line (cleanup)
* P2 edits the same line (feature update)

Git cannot decide:

> “Should this line exist or not?”

---

## STEP 1 — Base File

`app.txt`

```txt
Version 1
Deprecated Feature
Active Feature
```

---

## STEP 2 — P1 Deletes Line

P1 believes feature is obsolete.

```txt
Version 1
Active Feature
```

```bash
git commit -m "Remove deprecated feature"
```

---

## STEP 3 — P2 Modifies Deleted Line

P2 thinks feature is still needed.

```txt
Version 1
Deprecated Feature (Updated)
Active Feature
```

```bash
git commit -m "Update deprecated feature"
```

---

## STEP 4 — Merge → Conflict

Git sees:

* Base: line exists
* HEAD: line deleted
* Incoming: line modified

💥 Conflict

---

## STEP 5 — Resolution Strategy

This is **NOT technical**, it’s **logical**:

Ask:

* Is feature really deprecated?
* Was deletion premature?

### Possible resolutions:

1. Keep deletion
2. Restore updated line
3. Replace with comment or migration note

---

## STEP 6 — Resolve + Commit

Edit file → remove conflict markers → save.

```bash
git add app.txt
git commit -m "Resolve delete vs modify conflict"
```

---

# SCENARIO 3 — NEARBY LINES CONFLICT

😮 *Surprises beginners*

---

## Why This Happens

Git uses **context lines** to apply patches.

---

## Base Code

```js
function greet() {
  console.log("Hello");
  console.log("World");
}
```

---

## Changes

### P1 edits:

```js
console.log("Hello User");
```

### P2 edits:

```js
console.log("World!!!");
```

---

## Why conflict may occur

If:

* Lines are close
* File formatting changes
* Indentation shifts

Git loses exact context.

---

## Lesson

> Smaller functions = fewer conflicts
> Large functions = conflict magnets

---

# SCENARIO 4 — FILE RENAMED VS FILE EDITED

🔥 *Classic professional-level conflict*

---

## STEP 1 — P1 Renames File

```bash
git mv config.js settings.js
git commit -m "Rename config file"
```

---

## STEP 2 — P2 Edits Old File

```js
// modifies config.js
```

```bash
git commit -m "Update config values"
```

---

## STEP 3 — Merge Conflict

Git confusion:

* Is `config.js` deleted?
* Or renamed?
* Where should edits go?

---

## Resolution

1. Open `settings.js`
2. Manually copy P2’s changes
3. Delete `config.js`
4. Commit

---

# SCENARIO 5 — CONFLICT DURING `git pull`

---

## Important Truth

```txt
git pull = git fetch + git merge
```

So conflicts during pull = merge conflicts.

---

## What Happens

```bash
git pull origin main
```

Git tries to auto-merge fetched changes.

If conflict → stops mid-pull.

---

## Recovery Steps

```bash
git status
# resolve conflicts
git add .
git commit
```

---

# 🧩 UNIVERSAL CONFLICT RESOLUTION CHECKLIST

Always follow this order:

1️⃣ `git status`
2️⃣ Open conflicted files
3️⃣ Understand intent (not code first)
4️⃣ Edit & remove markers
5️⃣ `git add`
6️⃣ `git commit`

---

# 🧠 WHAT YOU HAVE LEARNED (Deep Level)

After all scenarios, you now understand:

* Git uses **3-way merge**
* Conflicts are **logical, not errors**
* Git never guesses business intent
* Humans must resolve ambiguity

---

If you want next **advanced labs**:

* Rebase conflicts (hard mode)
* Cherry-pick conflicts
* package.json dependency conflicts
* VS Code conflict UI walkthrough
* How Git stores conflicts internally (index stages)

Say **which one** and I’ll build it step-by-step 🔥
