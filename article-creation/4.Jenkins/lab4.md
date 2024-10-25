# **Lab 2.1: Resolving a Merge Conflict in Git**

In this beginner-friendly guide, you will learn how to handle a **merge conflict** in Git. Merge conflicts occur when two branches modify the same part of a file, and Git is unsure how to merge the changes. By the end of this lab, you will understand how to create a Git repository, manage branches, and resolve conflicts when merging.

---

## **Table of Contents**

1. **Introduction**
2. **Step-by-Step Instructions**
   1. Create a Directory and Initialize a Git Repository
   2. Create and Commit to the Main Branch
   3. Create New Branches
   4. Make Changes in Different Branches
   5. Merge the Branches and Resolve the Conflict
3. **Conclusion**

---

## **Introduction**

A **merge conflict** occurs when two branches make changes to the same section of a file, and Git doesn’t know which version to keep. You must manually resolve these conflicts by editing the file. In this lab, we’ll simulate a merge conflict by creating two branches, making conflicting changes, and resolving the conflict.

---

## **Step-by-Step Instructions**

### **Step 1: Create a Directory and Initialize a Git Repository**

We will start by creating a new directory and initializing a Git repository.

#### **1.1 Create a New Directory**

In your terminal, run the following commands to create a directory for your project and navigate into it:

```bash
cd ~
mkdir merge-conflict
cd merge-conflict
```

#### **1.2 Initialize a Git Repository**

Initialize Git inside the directory:

```bash
git init
```

This creates an empty Git repository, allowing you to start tracking changes in your project.

---

### **Step 2: Create and Commit to the Main Branch**

Now, we’ll create a file and make the first commit on the `main` branch.

#### **2.1 Create a `README.md` File**

Create a `README.md` file with some initial content:

```bash
echo "This line is created by the main branch." > README.md
```

#### **2.2 Commit the Changes**

Add and commit the `README.md` file to the repository:

```bash
git add README.md
git commit -m "Create README.md file"
```

You’ve now committed the initial version of the `README.md` file on the `main` branch.

---

### **Step 3: Create New Branches**

Let’s create two branches that will each make changes to the same file.

#### **3.1 Create Two New Branches**

Create two branches called `feature/a` and `feature/b`:

```bash
git branch feature/a
git branch feature/b
```

#### **3.2 Verify Branches**

To list all branches and confirm their creation, run:

```bash
git branch
```

You should see:

```
* main
  feature/a
  feature/b
```

---

### **Step 4: Make Changes in Different Branches**

Now, we will switch to each branch and make changes to the `README.md` file.

#### **4.1 Update `README.md` in `feature/a`**

Switch to `feature/a` and modify the file:

```bash
git switch feature/a
echo "This line is created by the feature/a branch." > README.md
```

#### **4.2 Commit the Changes to `feature/a`**

Add and commit the changes:

```bash
git add README.md
git commit -m "Update README.md in feature/a"
```

#### **4.3 Update `README.md` in `feature/b`**

Now, switch to the `feature/b` branch and make a different change to the same file:

```bash
git switch feature/b
echo "This line is created by the feature/b branch." > README.md
```

#### **4.4 Commit the Changes to `feature/b`**

Add and commit the changes to `feature/b`:

```bash
git add README.md
git commit -m "Update README.md in feature/b"
```

---

### **Step 5: Merge the Branches and Resolve the Conflict**

#### **5.1 Merge `feature/a` into `main`**

Switch back to the `main` branch and merge the changes from `feature/a`:

```bash
git switch main
git merge --no-ff feature/a
```

This merge will succeed without conflicts because there are no conflicting changes yet.

#### **5.2 Merge `feature/b` into `main`**

Next, attempt to merge `feature/b` into `main`:

```bash
git merge --no-ff feature/b
```

At this point, Git will detect a **merge conflict** because both branches modified the same line in `README.md`.

#### **5.3 Resolve the Merge Conflict**

Git marks the conflicting section of the file for you to resolve. Open the file in your text editor:

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

Manually edit the file to resolve the conflict, perhaps by combining both changes:

```plaintext
This line is created by the feature/a and feature/b branches.
```

Save and close the file.

#### **5.4 Commit the Resolved Conflict**

After resolving the conflict, add the changes and commit:

```bash
git add README.md
git commit -m "Resolve merge conflict between feature/a and feature/b"
```

---

### **Step 6: View Commit Log**

Finally, verify that the merge was successful by checking the commit log:

```bash
git log --oneline
```

You should see a series of commits, including the conflict resolution:

```
d4f89d2 Resolve merge conflict between feature/a and feature/b
a8c43c3 Merge feature/a into main
b1d89f4 Create README.md file
```

---

## **Conclusion**

In this lab, you learned how to handle a **merge conflict** in Git by:

1. Creating and managing branches.
2. Making conflicting changes in different branches.
3. Manually resolving a conflict in a file.
4. Merging the branches successfully after resolving the conflict.

Merge conflicts are common in team-based projects, and understanding how to resolve them is essential for smooth collaboration.

### Example Output:

```bash
$ git merge feature/b
Auto-merging README.md
CONFLICT (content): Merge conflict in README.md
Automatic merge failed; fix conflicts and then commit the result.
```

By practicing how to resolve merge conflicts, you will be more confident when working in a collaborative development environment. Happy coding!
