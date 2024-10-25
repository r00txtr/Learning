# Lab 2.4: Running a Container with GitLab Runner

In this lab, you will learn how to set up a simple web application using **GitLab Runner** and **Docker**. The application will be deployed using a CI/CD pipeline in **GitLab**, running on Docker. We'll also use **Docker Compose** to manage the container and define the deployment steps in a **.gitlab-ci.yml** file.

---

## Table of Contents
1. **Introduction**
2. **Step-by-Step Instructions**
    1. Create a New Project on GitLab
    2. Clone the Project Repository
    3. Set Up the Application Files
        - Dockerfile
        - Docker Compose
        - GitLab CI/CD
        - HTML File
    4. Push the Code to GitLab
    5. Configure GitLab Runner
    6. Register the GitLab Runner Executor
    7. Verify the Runner and Pipeline
    8. Testing and Verifying the Container
3. **Conclusion**

---

## Introduction

This lab guides you through the process of:
- Creating a GitLab project and setting up a pipeline for Dockerized web applications.
- Using **GitLab Runner** to deploy a containerized application.
- Running the pipeline with a **GitLab CI/CD** configuration.
- Deploying a basic HTML application using Docker.

---

## Step-by-Step Instructions

### Step 1: Create a New Project on GitLab

#### 1.1 Create a Project on GitLab

1. Navigate to your GitLab instance and create a new project.
   - **Project Name**: `apps`
   - **Visibility Level**: `Public`

This will create a new project called **apps** in your GitLab repository.

---

### Step 2: Clone the Project Repository

#### 2.1 Clone the Project Locally

Now, clone the project to your local environment:

```bash
cd ~/
git clone https://gitlab.adinusa.id/USERNAME_GITLAB/apps.git
cd apps
```

Replace `USERNAME_GITLAB` with your actual GitLab username.

---

### Step 3: Set Up the Application Files

#### 3.1 Create a Dockerfile

The Dockerfile specifies the image for the web server (in this case, Apache) and adds the HTML content to it:

```bash
vim Dockerfile
```

Add the following content:

```dockerfile
FROM registry.adinusa.id/btacademy/httpd:latest
ADD . /usr/local/apache2/htdocs/
```

This will create a container with the Apache web server and serve the content from the current directory.

#### 3.2 Create Docker Compose Configuration

Next, create a `docker-compose.yml` file to define how Docker will run the container:

```bash
vim docker-compose.yml
```

Add the following content:

```yaml
version: '2'
services: 
  app:
    container_name: app
    build:
      context: .
      dockerfile: Dockerfile
    image: app:1
    network_mode: bridge
    restart: always
    ports:
      - "8070:80"
```

This file defines a service named `app`, built from the Dockerfile, and exposes port **8070** on the host.

#### 3.3 Create GitLab CI/CD Configuration

Create a `.gitlab-ci.yml` file to automate the deployment process in GitLab:

```bash
vim .gitlab-ci.yml
```

Add the following content:

```yaml
stages:
  - deploy

docker:
  stage: deploy
  script:
    - docker-compose down --rmi all
    - docker-compose up -d --build
  only:
    - main
  tags:
    - apps
```

This file defines a **deploy** stage that will run the Docker Compose commands to build and deploy the application.

#### 3.4 Create the HTML File

Create an `index.html` file that will be served by the Apache server:

```bash
vim index.html
```

Add the following content:

```html
!! Hello World !!
```

---

### Step 4: Push the Code to GitLab

#### 4.1 Push the Changes to GitLab

Add all the changes to Git, commit, and push to your GitLab repository:

```bash
git add .
git commit -m "Initial commit"
git push
```

---

### Step 5: Configure GitLab Runner

#### 5.1 Disable Shared Runner and Get Registration Token

1. Access your GitLab repository: `https://gitlab.adinusa.id/USERNAME_GITLAB/apps`.
2. Disable **Shared Runners** by navigating to **Settings > CI/CD > Runners > Shared Runners**.
   - Click **Disable shared Runners**.
3. Go to **Settings > CI/CD > Runners > Setup a specific Runner manually**.
   - Copy the **registration token**.

---

### Step 6: Register the GitLab Runner Executor

#### 6.1 Register GitLab Runner

Run the following command to register the GitLab Runner:

```bash
sudo gitlab-runner register -n \
  --url https://gitlab.adinusa.id/ \
  --registration-token REGISTRATION_TOKEN \
  --executor shell \
  --description "apps" \
  --tag-list "apps"
```

Replace `REGISTRATION_TOKEN` with the token you copied earlier.

---

### Step 7: Verify the Runner and Pipeline

#### 7.1 Verify the GitLab Runner

Verify that the GitLab Runner is properly registered:

```bash
sudo gitlab-runner verify
sudo gitlab-runner run --debug
```

You should see output confirming the runner's registration.

#### 7.2 Verify the Status of Runners

1. Navigate to `https://gitlab.adinusa.id/USERNAME_GITLAB/apps/settings/ci_cd`.
2. Click **Expand** on **Runners**, then find **Available specific runners**.

---

### Step 8: Testing and Verifying the Container

#### 8.1 Run the Pipeline

1. Access your GitLab repository's pipeline page: `https://gitlab.adinusa.id/USERNAME_GITLAB/apps/-/pipelines`.
2. Run the pipeline and verify the status of the jobs at: `https://gitlab.adinusa.id/USERNAME_GITLAB/apps/-/jobs`.

If any jobs fail with an error like "ERROR: Job failed (system failure): prepare environment ...", try searching for the solution online.

#### 8.2 Check the Running Container

Once the pipeline runs successfully, check if the container is running:

```bash
docker container ls
```

Verify the output of the container by accessing it via `curl`:

```bash
curl -i http://localhost:8070 && echo ""
```

---

### Step 9: Add and Push a New File

#### 9.1 Create a New HTML File

Create a new HTML file named `ok.html`:

```bash
cd ~/apps
vim ok.html
```

Add the following content:

```html
Hello username
```

#### 9.2 Push the New File to GitLab

Add, commit, and push the new file to your GitLab repository:

```bash
git add ok.html
git commit -m "Add ok.html"
git push origin main
```

#### 9.3 Verify the New File

Check if the new file is served by the container:

```bash
curl -i http://localhost:8070 && echo ""
curl -i http://localhost:8070/ok.html && echo ""
```

---

## Conclusion

In this lab, you learned how to:
1. Set up a GitLab project and configure a GitLab Runner.
2. Use Docker and Docker Compose to run a containerized web application.
3. Automate the deployment process using a **GitLab CI/CD pipeline**.

This setup allows you to deploy applications in a fully automated manner using GitLab's powerful CI/CD features.

### Example Output:

```bash
HTTP/1.1 200 OK
Content-Type: text/html; charset=UTF-8
Content-Length: 14
Hello username
```

Congratulations on completing **Lab 2.4: Running a Container with GitLab Runner**! You now have the skills to deploy applications using GitLab Runner and Docker.

Happy Automating!
