# Lab 3.2: Integrate GitLab with Docker I

In this lab, we will integrate **GitLab CI/CD** with **Docker** to automate the deployment of a **Canvas Tetris** game. By the end of this lab, you will have the **Canvas Tetris** game running inside a Docker container, deployed via **GitLab CI/CD** pipelines. You will learn how to configure a Dockerfile, set up a GitLab Runner, and monitor the deployment pipeline.

---

## Table of Contents
1. **Introduction**
2. **Step-by-Step Instructions**
    1. Cloning the Canvas Tetris Repository
    2. Create a New GitLab Project
    3. Configure GitLab Remote and Branch
    4. Organize Project Files and Create Dockerfile
    5. Use a Docker Hub Registry Project
    6. Set Up Docker Compose
    7. Configure GitLab CI/CD
    8. Register GitLab Runner
    9. Commit and Push Changes
    10. Monitor Pipeline and Access Deployed Game
3. **Conclusion**

---

## Introduction

In this lab, you will:
- Clone a **Canvas Tetris** repository from GitHub.
- Set up a new GitLab project and configure GitLab CI/CD pipelines.
- Use **Docker** and **Docker Compose** to containerize the Tetris game.
- Integrate with **Docker Hub Registry** for managing Docker images.
- Use **GitLab Runner** to automate the build and deployment processes.

---

## Step-by-Step Instructions

### Step 1: Cloning the Canvas Tetris Repository

#### 1.1 Clone the Canvas Tetris Repository

First, clone the **Canvas Tetris** repository from GitHub to your local environment:

```bash
cd ~
git clone https://github.com/dionyziz/canvas-tetris.git canvas-tetris-username
```

This will create a local copy of the Tetris game in a directory named `canvas-tetris-username`.

---

### Step 2: Create a New GitLab Project

#### 2.1 Create a New Project on GitLab

1. Open GitLab in your browser and create a new project.
   - **Project Name**: `canvas-tetris-username` (replace `username` with your GitLab username).
   - **Visibility Level**: `Public`.

---

### Step 3: Configure GitLab Remote and Branch

#### 3.1 Set GitLab Remote URL

Now, set the remote URL from GitHub to your GitLab repository:

```bash
cd ~/canvas-tetris-username
git remote set-url origin https://gitlab.adinusa.id/USERNAME_GITLAB/canvas-tetris-username.git
git remote get-url origin
```

Replace `USERNAME_GITLAB` with your GitLab username.

#### 3.2 Create a New Branch

Create and switch to the `main` branch:

```bash
git branch main
git switch main
```

---

### Step 4: Organize Project Files and Create Dockerfile

#### 4.1 Organize Tetris Game Files

Create a new directory to store the Tetris game files:

```bash
mkdir tetris
mv index.html js sound style.css ~/canvas-tetris-username/tetris/
```

#### 4.2 Create a Dockerfile

Create a `Dockerfile` to containerize the Tetris game:

```bash
vim Dockerfile
```

Add the following content to the Dockerfile:

```dockerfile
FROM registry.adinusa.id/btacademy/nginx:latest

ADD ./tetris /usr/share/nginx/html/
```

---

### Step 5: Configure GitLab Variables for Docker Hub

In GitLab, navigate to **Settings > CI/CD > Variables** and add the following:

- **DOCKER_HUB_USERNAME**: Your Username in Docker Hub.
- **DOCKER_HUB_PASSWORD**: Your Password in Docker Hub.

---

### Step 6: Set Up Docker Compose

#### 6.1 Create a `docker-compose.yml` File

Create a `docker-compose.yml` file to manage the deployment of the Tetris game container:

```bash
vim docker-compose.yml
```

Add the following content:

```yaml
version: '3'
services:
  tetris-game:
    image: USERNAME_DOCKERHUB/canvas-tetris-username:1.0
    ports:
      - "7071:80"
```

---

### Step 7: Configure GitLab CI/CD

#### 7.1 Create `.gitlab-ci.yml` File

Create a `.gitlab-ci.yml` file to define the CI/CD pipeline:

```bash
vim .gitlab-ci.yml
```

Add the following content:

```yaml
stages:
  - build
  - deploy

variables:
  DOCKER_IMAGE: USERNAME_DOCKERHUB/canvas-tetris-username

before_script:
  - docker login -u "$DOCKER_HUB_USERNAME" -p "$DOCKER_HUB_PASSWORD"

build-image:
  stage: build
  image: docker:latest
  services:
    - docker:dind
  script:
    - docker build -t $DOCKER_IMAGE:1.0 .
    - docker push $DOCKER_IMAGE:1.0
  only:
    - main

deploy-game:
  stage: deploy
  script:
    - docker-compose down --rmi all
    - docker-compose up -d
    - docker container ls
  only:
    - main
  tags:
    - tetris
```

---

### Step 8: Register GitLab Runner

#### 8.1 Disable Shared Runners

1. Go to your GitLab project: `https://gitlab.adinusa.id/USERNAME_GITLAB/canvas-tetris-username`.
2. Go to **Settings > CI/CD > Runners > Shared Runners** and **disable shared runners**.

#### 8.2 Copy the Registration Token

Go to **Settings > CI/CD > Runners > Setup a specific Runner manually** and copy the **registration token**.

#### 8.3 Register the GitLab Runner

- **Register Docker Runner**:

```bash
sudo gitlab-runner register -n \
  --url https://gitlab.adinusa.id \
  --registration-token REGISTRATION_TOKEN \
  --executor docker \
  --description "Docker Runner" \
  --docker-image "registry.adinusa.id/btacademy/docker:latest" \
  --docker-volumes /var/run/docker.sock:/var/run/docker.sock
```

- **Register Shell Runner**:

```bash
sudo gitlab-runner register -n \
  --url https://gitlab.adinusa.id \
  --registration-token REGISTRATION_TOKEN \
  --executor shell \
  --description "Shell Runner" \
  --tag-list tetris
```

---

### Step 9: Commit and Push Changes

#### 9.1 Commit Changes to the Local Repository

Check the status of your changes, then add and commit them:

```bash
git status
git add .
git commit -m "Initial Commit"
git status
```

#### 9.2 Push Changes to the Remote Repository

Push the committed changes to GitLab:

```bash
git push origin main
```

---

### Step 10: Monitor Pipeline and Access Deployed Game

#### 10.1 Monitor the GitLab Pipeline

Once the code is pushed, monitor the GitLab pipeline:

- Access: `https://gitlab.com/[USERNAME_GITLAB]/canvas-tetris-username/-/pipelines/`.
- Ensure that the pipeline runs successfully and shows a **"passed"** status.

#### 10.2 Access the Deployed Tetris Game

Once the pipeline has successfully deployed the game, you can access the Tetris game at the following URL:

```bash
http://192.168.0.X:7071/
```

Replace `192.168.0.X` with the IP address of your machine.

---

## Conclusion

In this lab, you learned how to:
- Clone a project from **GitHub** and set it up in **GitLab**.
- Configure **Docker** and **Docker Compose** to containerize the project.
- Use **GitLab CI/CD** to automate the build and deployment of a project.
- Set up **Docker Hub Registry** for managing Docker images.

This setup allows you to automate the deployment of the **Canvas Tetris** game using **GitLab CI/CD** and **Docker**.

Congratulations on completing **Lab 3.2: Integrating GitLab with Docker**!
