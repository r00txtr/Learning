# Lab 2.3: Setting Up GitLab Runner with Shell Executor

In this lab, you'll learn how to set up a GitLab Runner using the **Shell Executor**. You will create a Docker image using a simple HTML file, configure a pipeline with a `.gitlab-ci.yml` file, and register a specific runner for your GitLab project.

---

## Table of Contents
1. **Introduction**
2. **Step-by-Step Instructions**
    1. Create a GitLab Project
    2. Initialize a Local Git Repository
    3. Create Dockerfile and HTML File
    4. Configure GitLab CI
    5. Register a GitLab Runner with Shell Executor
    6. Verify Runner and Pipeline
3. **Conclusion**

---

## Introduction

**GitLab Runner** is a tool that executes jobs in GitLab CI pipelines. In this lab, we will create a simple project that builds a Docker image containing an HTML file. You will register a **Shell Executor** on your GitLab Runner, configure a pipeline using `.gitlab-ci.yml`, and push everything to GitLab.

---

## Step-by-Step Instructions

### Step 1: Create a GitLab Project

First, you need to create a new project on GitLab.

#### 1.1 Create a New Project

1. Open your browser and navigate to your **Adinusa GitLab** account.
2. Click on **Create a project**.
3. Set the following details:
   - **Project Name**: `build-image`
   - **Visibility Level**: Public

4. Click **Create project**.

#### 1.2 Copy the Remote Git URL

Once your repository is created, copy the **remote Git URL**. You will need this URL to add the remote repository in your local machine later.

---

### Step 2: Initialize a Local Git Repository

Now, you will create a local repository and link it to the GitLab remote repository.

#### 2.1 Create a New Directory

Run the following commands to create and navigate to a new directory:

```bash
cd ~
mkdir build-image
cd build-image
```

#### 2.2 Initialize a Git Repository

Initialize the local directory as a Git repository:

```bash
git init
```

#### 2.3 Add Remote Repository

Add the remote repository you copied from GitLab:

```bash
git remote add origin https://gitlab.adinusa.id/[USERNAME_GITLAB]/build-image.git
```

Make sure to replace `[USERNAME_GITLAB]` with your actual GitLab username.

---

### Step 3: Create Dockerfile and HTML File

Next, you will create an HTML file and a `Dockerfile` for the project.

#### 3.1 Create an `index.html` File

Create a simple `index.html` file:

```bash
vim index.html
```

Add the following content:

```html
Hi, I'm [your-username]. I'm learning GitLab.
```

Replace `[your-username]` with your Adinusa username.

#### 3.2 Create a `Dockerfile`

Now, create a `Dockerfile`:

```bash
vim Dockerfile
```

Add the following content to the `Dockerfile`:

```Dockerfile
FROM registry.adinusa.id/btacademy/nginx:latest
ADD index.html /usr/share/nginx/html/
```

This `Dockerfile` uses the NGINX image and adds the `index.html` file to the default directory served by NGINX.

---

### Step 4: Configure GitLab CI

Create a **GitLab CI script** to automate the Docker image build process.

#### 4.1 Create the `.gitlab-ci.yml` File

Run the following command to create the `.gitlab-ci.yml` file:

```bash
vim .gitlab-ci.yml
```

Add the following content to the file:

```yaml
stages:
  - build

build-docker-image:
  stage: build
  script:
    - docker build -t myimage:1.0 .
    - docker image ls
  only:
    - main
```

This pipeline will build the Docker image with the tag `myimage:1.0` and display the list of Docker images.

---

### Step 5: Commit and Push to GitLab

#### 5.1 Add and Commit the Files

Check the current status, add the files, and commit them:

```bash
git status
git add .
git commit -m "First Commit"
```

#### 5.2 Push the Changes to GitLab

Push your changes to the GitLab remote repository:

```bash
git push origin main
```

---

### Step 6: Register GitLab Runner with Shell Executor

To run the pipeline, you need to register a **GitLab Runner** with the **Shell Executor**.

#### 6.1 Access Runner Token

1. Go to your **build-image** repository:  
   `https://gitlab.adinusa.id/USERNAME_GITLAB/build-image`
2. Disable **Shared Runners**:
   - Go to **Settings > CI / CD > Runners**.
   - Disable **Shared Runners**.
3. Copy the **Registration Token** for the specific runner:
   - Go to **Settings > CI / CD > Runners > Setup a specific Runner manually**.
   - Copy the **Registration Token**.

#### 6.2 Register GitLab Runner Executor Shell

Use the following command to register the **GitLab Runner**. Replace `REGISTRATION_TOKEN` with the token you copied:

```bash
sudo gitlab-runner register -n \
  --url https://gitlab.adinusa.id/ \
  --registration-token REGISTRATION_TOKEN \
  --executor shell \
  --description "Shell Runner"
```

This command registers the runner using the **Shell Executor**.

#### 6.3 Verify the GitLab Runner

After registering the runner, verify its status:

```bash
sudo gitlab-runner verify
sudo gitlab-runner run --debug
```

---

### Step 7: Verify the Runner and Pipeline

#### 7.1 Check the Runner Status

Go to **Settings > CI / CD > Runners** in your GitLab project, and ensure the runner is available under **Available specific runners**.

#### 7.2 Verify the Pipeline

To check if the pipeline has run successfully:

1. Go to your **build-image** repository in GitLab.
2. Navigate to the **Pipelines** section:  
   `https://gitlab.adinusa.id/USERNAME_GITLAB/build-image/-/pipelines`
3. Ensure the pipeline status is **passed**.

#### 7.3 View the Job Logs

To see the details of the job:

1. Click on the **passed** status of the pipeline.
2. Go to the **Jobs** section and view the logs for the `build-docker-image` job.

---

## Conclusion

In this lab, you successfully set up a **GitLab Runner** with a **Shell Executor** and configured a CI/CD pipeline to build a Docker image. You learned how to:
1. Create a project on GitLab.
2. Configure GitLab Runner.
3. Write a `.gitlab-ci.yml` file to automate tasks.
4. Verify and manage runners and pipelines in GitLab.

### Example Output

```bash
$ gitlab-runner verify
Running in system-mode.

Verifying runner... is alive                        runner=abcd1234
```

This process can now be extended for more complex tasks, but this basic setup forms the foundation of using GitLab CI/CD pipelines with a Shell Executor.
