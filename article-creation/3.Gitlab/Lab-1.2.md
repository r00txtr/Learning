# How to Create and Manage a GitLab Project

In this guide, you'll learn how to create and manage a GitLab project, configure Git for global use, and push your local repository to GitLab. If there’s a username mentioned, replace it with your own GitLab username. Similarly, replace the placeholder 'X' in IP addresses with your actual attendance number.

## Table of Contents
1. **Introduction**
2. **Step-by-Step Instructions**
    1. Create a Project on GitLab
    2. Set Up Git Globally
    3. Create a Local Repository
    4. Add a Remote Repository
    5. Create and Commit Files
    6. Push to GitLab
    7. Verify Changes in GitLab
3. **Conclusion**

---

## Introduction

GitLab is a powerful platform that allows developers to host code, collaborate on projects, and manage repositories. This tutorial covers the steps to:
- Create a project on GitLab.
- Set up Git on your local machine.
- Push your local repository to GitLab.

---

## Step-by-Step Instructions

### Step 1: Create a Project on GitLab

#### 1.1 Login to Your GitLab Account

First, log in to your **Adinusa GitLab** account:

- Navigate to the [Adinusa GitLab login page](http://gitlab.adinusa.id/users/sign_in).
- Enter your credentials and log in.

#### 1.2 Create a New Project

Once logged in, you need to create a new project. Follow these steps:

1. Click the **Create a project** button on the dashboard.

2. On the next page, choose **Create blank project**.

   ![Create Blank Project](https://example.com/create-blank-project.png) *(Example Screenshot)*

Alternatively, you can directly visit [GitLab’s New Project Page](http://gitlab.adinusa.id/projects/new).

#### 1.3 Set Up the Project

When setting up the project, fill in the following details:

- **Project Name**: `my-project-username` (replace **username** with your GitLab username)
- **Visibility Level**: Set to **Public**

Click **Create project** to finalize.

---

### Step 2: Set Up Git Globally

Before working with Git locally, you need to configure some basic information like your name and email.

#### 2.1 Configure Git Globally

Open your terminal and run the following commands:

```bash
git config --global user.name "your-username"
git config --global user.email "your-username@gitlab.adinusa.id"
git config --global init.defaultBranch main
git config --global --list
```

The above commands set your global Git configuration for all repositories, ensuring that commits show the correct user information.

---

### Step 3: Create a Local Repository

Next, you will create a local Git repository on your machine that will later be pushed to GitLab.

#### 3.1 Create and Initialize a Repository

In your terminal, run the following commands to create and initialize a local Git repository:

```bash
cd ~
mkdir my-project-username
cd my-project-username
git init
```

#### 3.2 Verify `.git` Directory

Run the following command to list hidden files and directories:

```bash
ls -a
```

Make sure you see the `.git` directory, which confirms that Git is initialized in your project folder.

---

### Step 4: Add a Remote Repository

To connect your local repository with the remote GitLab repository, you need to add a remote origin.

#### 4.1 Add Remote Repository

Run this command, replacing **username** with your actual GitLab username:

```bash
git remote add origin http://gitlab.adinusa.id/username/my-project-username.git
```

This links your local repository to the remote GitLab repository.

---

### Step 5: Create and Commit Files

#### 5.1 Create a README.md File

Create a simple README.md file with the following command:

```bash
echo "Hai-Adinusa" > README.md
```

This will create a README.md file with the content "Hai-Adinusa".

#### 5.2 Commit Changes to the Local Repository

To commit your changes to the local repository:

1. Check the status of your repository:

   ```bash
   git status
   ```

2. Add the README.md file to the staging area:

   ```bash
   git add README.md
   ```

3. Commit the changes with a message:

   ```bash
   git commit -m "Create README.md file"
   ```

---

### Step 6: Push to GitLab

Now that you’ve committed changes locally, it’s time to push the changes to your remote GitLab repository.

#### 6.1 Push the Changes

Run the following command to push your local repository to GitLab:

```bash
git push origin main
```

This pushes your local `main` branch to the remote `main` branch on GitLab.

---

### Step 7: Verify Changes in GitLab

To confirm that your changes were successfully pushed to GitLab:

1. Go to your project’s repository on GitLab by visiting:

   ```bash
   http://gitlab.adinusa.id/username/my-project-username
   ```

2. Verify that the `README.md` file appears in the repository.

   ![GitLab Repo](https://example.com/gitlab-repo.png) *(Example Screenshot)*

---

## Conclusion

In this guide, you successfully:
1. Created a new project on **GitLab**.
2. Configured **Git** globally on your system.
3. Created and committed files in a local Git repository.
4. Pushed your local repository to **GitLab**.

You now have the skills to manage repositories using Git and GitLab, and collaborate on projects efficiently.

### Example Output:

```bash
$ git push origin main
Enumerating objects: 3, done.
Counting objects: 100% (3/3), done.
Writing objects: 100% (3/3), 250 bytes | 250.00 KiB/s, done.
Total 3 (delta 0), reused 0 (delta 0)
To http://gitlab.adinusa.id/username/my-project-username.git
 * [new branch]      main -> main
```

Happy Coding!
