# Lab 2.1: Resolving a Merge Conflict in Git

In this lab, you'll learn how to handle a **merge conflict** in Git. Merge conflicts occur when two branches have made changes to the same part of a file, and Git is unable to automatically merge them. You'll go through the process of creating a repository, working with branches, and resolving conflicts step by step.

---

## Table of Contents
1. **Introduction**
2. **Step-by-Step Instructions**
    1. Create a Directory and Initialize a Git Repository
    2. Create and Commit to the Main Branch
    3. Create New Branches
    4. Make Changes in Different Branches
    5. Merge the Branches and Resolve the Conflict
3. **Conclusion**

---

## Introduction

A **merge conflict** happens when changes are made to the same part of a file in two branches, and Git cannot determine which change to use. In this lab, you'll create two branches, make conflicting changes to the same file, and resolve the conflict.

---

## Step-by-Step Instructions

### Step 1: Create a Directory and Initialize a Git Repository

Start by creating a new directory for the project and initializing a Git repository.

#### 1.1 Create a New Directory

```bash
cd ~
mkdir merge-conflict
cd merge-conflict
```

#### 1.2 Initialize a Git Repository

Inside the `merge-conflict` directory, initialize a Git repository:

```bash
git init
```

You now have an empty Git repository where you can start tracking changes.

---

### Step 2: Create and Commit to the Main Branch

#### 2.1 Create a `README.md` File

Create a simple `README.md` file:

```bash
vim README.md
```

Add the following content to the file:

```plaintext
This line is created by the main branch.
```

#### 2.2 Commit the Changes

Add and commit the file to the repository:

```bash
git status
git add README.md
git commit -m "Create README.md"
```

---

### Step 3: Create New Branches

Now, you’ll create two branches to simulate different changes.

#### 3.1 Create Two New Branches

Create two branches named `feature/a` and `feature/b`:

```bash
git branch feature/a
git branch feature/b
```

#### 3.2 Verify All Branches

Check the branches you have created:

```bash
git branch
```

You should see the following branches:

```
* main
  feature/a
  feature/b
```

---

### Step 4: Make Changes in Different Branches

Next, you’ll switch to each branch and make conflicting changes to the `README.md` file.

#### 4.1 Update `README.md` from `feature/a`

Switch to `feature/a` and modify the `README.md` file:

```bash
git switch feature/a
vim README.md
```

Change the content to:

```plaintext
This line is created by the feature/a branch.
```

#### 4.2 Commit the Changes

Commit the changes to the `feature/a` branch:

```bash
git status
git add README.md
git commit -m "Update README.md"
```

#### 4.3 Update `README.md` from `feature/b`

Switch to `feature/b` and modify the `README.md` file:

```bash
git switch feature/b
vim README.md
```

Change the content to:

```plaintext
This line is created by the feature/b branch.
```

#### 4.4 Commit the Changes

Commit the changes to the `feature/b` branch:

```bash
git status
git add README.md
git commit -m "Update README.md"
```

---

### Step 5: Merge the Branches and Resolve the Conflict

#### 5.1 Merge `feature/a` into `main`

Switch back to the `main` branch and merge `feature/a`:

```bash
git switch main
git merge --no-ff feature/a
```

The merge will be successful because no conflicts exist at this point.

#### 5.2 Merge `feature/b` into `main`

Next, merge `feature/b` into `main`:

```bash
git merge --no-ff feature/b
```

At this point, you will encounter a **merge conflict** because both `feature/a` and `feature/b` made changes to the same line in `README.md`.

#### 5.3 Resolve the Merge Conflict

Git will mark the conflicting parts of the `README.md` file. Open the file to resolve the conflict:

```bash
vim README.md
```

You’ll see something like this:

```plaintext
<<<<<<< HEAD
This line is created by the feature/a branch.
=======
This line is created by the feature/b branch.
>>>>>>> feature/b
```

Modify the file to resolve the conflict:

```plaintext
This line is created by the feature/a and feature/b branches.
```

Save the file and exit.

#### 5.4 Commit the Resolved Conflict

After resolving the conflict, add and commit the changes:

```bash
git status
git add README.md
git commit -m "Fix Merge Conflict"
```

---

### Step 6: View Commit Log

You can check the commit history to verify that the merge was completed successfully:

```bash
git log --oneline
```

The output will show the sequence of commits, including the conflict resolution.

---

## Conclusion

In this lab, you successfully learned how to:
1. Create and manage branches in Git.
2. Modify files in different branches.
3. Resolve a **merge conflict** by manually editing the conflicting file.
4. Use Git to merge changes from multiple branches.

### Example Output

```bash
$ git merge feature/b
Auto-merging README.md
CONFLICT (content): Merge conflict in README.md
Automatic merge failed; fix conflicts and then commit the result.

$ git log --oneline
3d2c1a2 Fix Merge Conflict
1f2b3c4 Merge feature/a into main
1a2b3c4 Create README.md file
```

Merge conflicts are common in collaborative projects, and mastering the resolution process is an essential skill for any developer using Git.
