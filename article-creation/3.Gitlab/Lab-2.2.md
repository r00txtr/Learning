# Lab 2.2: Reset Commit in Git

In this lab, you'll learn how to reset a commit in Git, undo changes, and force push updates to GitLab. This is useful when you want to undo a previous commit while keeping or modifying certain files. 

---

## Table of Contents
1. **Introduction**
2. **Step-by-Step Instructions**
    1. Create a New Project in GitLab
    2. Create a Local Repository
    3. Add Remote Repository
    4. Create and Commit Files
    5. Modify Files and Push Changes
    6. Reset to a Previous Commit
    7. Force Push Changes to GitLab
3. **Conclusion**

---

## Introduction

When working on Git projects, sometimes you may need to undo changes or reset the repository to a previous state. In this lab, you'll create a project, make some changes, and then use the `git reset` command to undo the deletion of files. You'll also learn how to enable and use **force push** to GitLab to reflect these changes.

---

## Step-by-Step Instructions

### Step 1: Create a New Project in GitLab

#### 1.1 Create a Project

1. Log in to your **Adinusa GitLab** account.
2. Click on **Create a project**.
3. Set the following details:
   - **Project Name**: `reset-commit`
   - **Visibility Level**: Public

4. Click **Create project** to finalize the project creation.

---

### Step 2: Create a Local Repository

Once you have the project on GitLab, create a local repository for your work.

#### 2.1 Create and Initialize a Git Repository

In your terminal, run the following commands to create and initialize a local Git repository:

```bash
cd ~
mkdir reset-commit
cd reset-commit
git init
```

This creates a new directory called `reset-commit` and initializes it as a Git repository.

---

### Step 3: Add Remote Repository

Now, connect your local repository to the remote repository on GitLab.

#### 3.1 Add the Remote Repository

Replace **username** with your GitLab username and run the following command:

```bash
git remote add origin http://gitlab.adinusa.id/username/reset-commit.git
```

This links your local repository to the remote GitLab project.

---

### Step 4: Create and Commit Files

#### 4.1 Create Multiple Files

Create five text files in your repository:

```bash
touch file-{1..5}.txt
ls
```

This command creates `file-1.txt`, `file-2.txt`, `file-3.txt`, `file-4.txt`, and `file-5.txt`.

#### 4.2 Commit and Push the Files

Add and commit the files to the local repository, then push them to GitLab:

```bash
git status
git add .
git commit -m "Create 5 files"
git push origin main
```

This commits all five files and pushes them to the `main` branch of your GitLab project.

---

### Step 5: Modify Files and Push Changes

#### 5.1 Edit `file-1.txt` and `file-2.txt`

Edit the contents of `file-1.txt` and `file-2.txt`:

```bash
echo "This is file-1.txt" >> file-1.txt
echo "This is file-2.txt" >> file-2.txt
```

#### 5.2 Commit and Push the Changes

Commit and push the changes for each file:

For `file-1.txt`:

```bash
git status
git add file-1.txt
git commit -m "Edit file-1.txt"
git push origin main
```

For `file-2.txt`:

```bash
git add file-2.txt
git commit -m "Edit file-2.txt"
git push origin main
```

---

### Step 6: Delete Files and Push Changes

#### 6.1 Delete `file-3.txt`, `file-4.txt`, and `file-5.txt`

Remove the files:

```bash
rm file-{3..5}.txt
ls
```

#### 6.2 Commit and Push the Deletion

Commit and push the deletion to GitLab:

```bash
git status
git add .
git commit -m "Remove file-{3,4,5}.txt"
git push origin main
```

---

### Step 7: Reset to a Previous Commit

Now, you will reset the repository to a previous commit to undo the deletion of the files.

#### 7.1 Check the Commit Log

List all previous commits:

```bash
git log --oneline
```

Find the commit hash for the message **"Edit file-2.txt"**.

#### 7.2 Reset the Repository

Use the commit hash from the previous step to reset the repository:

```bash
git reset --mixed <hash>
```

The `--mixed` option keeps the changes from the reset commit in the working directory but unstages them.

---

### Step 8: Restore Files and Push Changes

#### 8.1 Restore `file-3.txt`

You can restore `file-3.txt` from the staging area:

```bash
git restore file-3.txt
```

#### 8.2 Edit `file-3.txt`

Add some content to `file-3.txt`:

```bash
echo "This is file-3.txt" >> file-3.txt
```

#### 8.3 Commit and Push the Changes

Commit and push the changes, but this time, you may encounter an error:

```bash
git status
git add file-3.txt
git commit -m "Update file-3.txt"
git push origin main
```

You will likely receive an error because you have reset the repository and the history no longer matches.

---

### Step 9: Enable Force Push on GitLab

To resolve the issue, you need to enable **force push** in GitLab.

#### 9.1 Allow Force Push

1. Go to your GitLab project.
2. Navigate to **Settings > Repository**.
3. Expand **Protected Branches**.
4. Enable **Allowed to force push** for the `main` branch.

---

### Step 10: Force Push to GitLab

Now, push the changes with the `--force` flag:

```bash
git push -f origin main
```

This forces the push, overriding the remote history.

---

### Step 11: Commit and Push Remaining Files

Finally, commit and push the remaining files (`file-4.txt` and `file-5.txt`):

```bash
git status
git add .
git commit -m "Remove file-{4,5}.txt"
git push -f origin main
```

---

### Step 12: Verify Commit History

Check the commit history in both your local repository and GitLab:

#### 12.1 Verify Locally

```bash
git log --oneline
```

#### 12.2 Verify on GitLab

Go to your GitLab repository and navigate to **Repository > Commits** to see the history.

---

## Conclusion

In this lab, you learned how to:
1. Create a GitLab project and link it to a local repository.
2. Make commits and push changes to the remote repository.
3. Reset to a previous commit and restore files.
4. Enable **force push** and push changes to GitLab.

### Example Output

```bash
$ git reset --mixed <hash>
$ git push -f origin main
Total 0 (delta 0), reused 0 (delta 0)
To http://gitlab.adinusa.id/username/reset-commit.git
 + e3b0c4a...a5d2b1c main -> main (forced update)
```

By using Git's powerful reset and force-push features, you can easily undo changes and maintain an accurate project history.
