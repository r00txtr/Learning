# Lab 3.1: Integrating GitLab CI/CD with Vercel (Part II)

In this lab, you will learn how to integrate **GitLab CI/CD** with **Vercel** to deploy a **Vue.js** project. The project will be deployed using **GitLab Runner** and managed through **Vercel**. By the end of this lab, you will have automated the deployment process of a **Vue.js** application to Vercel using GitLab CI/CD.

---

## Table of Contents
1. **Introduction**
2. **Step-by-Step Instructions**
    1. Create a New Project on GitLab
    2. Register GitLab Runner Executor with Docker
    3. Set Up a Vue.js Project
    4. Configure Vercel and GitLab CI/CD
    5. Push the Code to GitLab
    6. Verify the Deployment
3. **Conclusion**

---

## Introduction

This lab will guide you through:
- Creating a **Vue.js** project with **Vue CLI**.
- Setting up **GitLab Runner** with **Docker** as an executor.
- Using **Vercel** for deploying the Vue.js project.
- Automating the deployment process using **GitLab CI/CD**.

---

## Step-by-Step Instructions

### Step 1: Create a New Project on GitLab

#### 1.1 Create a New GitLab Project

1. Open GitLab in your browser and create a new project.
   - **Project Name**: `vuejs-vercel-username` (replace `username` with your GitLab username).
   - **Visibility Level**: `Public`.

---

### Step 2: Register GitLab Runner Executor with Docker

#### 2.1 Get the Registration Token

1. Go to your GitLab repository in the browser: `https://gitlab.adinusa.id/USERNAME_GITLAB/vuejs-vercel-username`.
2. Navigate to **Settings > CI/CD > Runners > Setup a specific Runner manually**.
3. Copy the **Registration Token**.

#### 2.2 Register the GitLab Runner

Run the following command to register the GitLab Runner:

```bash
sudo gitlab-runner register -n \
  --url https://gitlab.adinusa.id \
  --registration-token REGISTRATION_TOKEN \
  --executor docker \
  --description "Docker Runner" \
  --docker-image "registry.adinusa.id/btacademy/node:16.16.0" \
  --docker-volumes /var/run/docker.sock:/var/run/docker.sock
```

Replace `REGISTRATION_TOKEN` with the token you copied earlier.

#### 2.3 Verify the GitLab Runner

Start and verify the runner:

```bash
sudo gitlab-runner start
sudo gitlab-runner verify
```

---

### Step 3: Set Up a Vue.js Project

#### 3.1 Clone the GitLab Repository

```bash
cd ~
git clone https://gitlab.adinusa.id/USERNAME_GITLAB/vuejs-vercel-username.git
cd vuejs-vercel-username
```

Replace `USERNAME_GITLAB` with your actual GitLab username.

#### 3.2 Set Up Vue CLI and Initialize the Project

Install **Vue CLI** and initialize a new Vue.js project using the **webpack** template:

```bash
npm install --global vue-cli
vue init webpack .
```

When prompted, answer the following:

```bash
? Project name 'username-app'
? Project description 'A Vue.js project'
? Author 'Administrator <admin@example.com>'
? Vue build 'standalone'
? Install vue-router? 'Yes'
? Use ESLint to lint your code? 'Yes'
? Pick an ESLint preset 'Standard'
? Set up unit tests 'No'
? Setup e2e tests with Nightwatch? 'No'
? Should we run `npm install` for you after the project has been created? '(recommended) npm'
```

This will initialize your Vue.js project.

#### 3.3 Install Express

Install the **express** package and remove `node_modules`:

```bash
npm install express --save
rm -rf node_modules
```

#### 3.4 Modify the `.gitignore` File

Remove the `/dist/` line from the `.gitignore` file:

```bash
vim .gitignore
# Remove line: /dist/
```

#### 3.5 Modify the `package.json`

Change the `start` script in `package.json`:

```bash
vim package.json
```

Modify the `"start"` script to:

```json
"start": "node server.js"
```

---

### Step 4: Create Supporting Files

#### 4.1 Create `server.js` File

Create a `server.js` file to serve the app using **Express**:

```bash
vim server.js
```

Add the following content:

```javascript
var express = require('express');
var path = require('path');
var serveStatic = require('serve-static');
app = express();
app.use(serveStatic(__dirname + "/dist"));
var port = process.env.PORT || 3000;
app.listen(port);
console.log('server started ' + port);
```

#### 4.2 Modify `HelloWorld.vue`

Change the welcome message in the **HelloWorld.vue** component to include your username:

```bash
vim src/components/HelloWorld.vue
```

Find and replace **"Welcome to Your Vue.js"** with **"Welcome to Your Vue.js username"**.

---

### Step 5: Set Up Vercel

#### 5.1 Log In to Vercel

Log in to **Vercel** using the following commands:

```bash
vercel login
```

Follow the instructions and log in using your email.

#### 5.2 Link the Project to Vercel

Link your GitLab project to **Vercel**:

```bash
vercel link
```

Leave the default values when prompted.

#### 5.3 Configure GitLab Variables for Vercel

In GitLab, navigate to **Settings > CI/CD > Variables** and add the following:

- **VERCEL_TOKEN**: Your Vercel access token.
- **VERCEL_ORG_ID**: Found in `.vercel/project.json`.
- **VERCEL_PROJECT_ID**: Found in `.vercel/project.json`.

#### 5.4 Configure Vercel for Vue.js

Go to the **Vercel dashboard**, select your project (`vuejs-vercel-username`), and:
- Go to **Settings > Build & Development Settings**.
- Change **Framework Preset** to **Vue.js**.
- Save the settings.

---

### Step 6: Configure GitLab CI/CD

#### 6.1 Create `.gitlab-ci.yml`

Create a `.gitlab-ci.yml` file to automate the deployment process:

```bash
vim .gitlab-ci.yml
```

Add the following content:

```yaml
image: registry.adinusa.id/btacademy/node:16.16.0

stages:
  - deploy

deploy_production:
  stage: deploy
  only:
    - main
  script:
    - npm install --global vercel
    - vercel pull --yes --environment=production --token=$VERCEL_TOKEN
    - vercel build --prod --token=$VERCEL_TOKEN
    - vercel deploy --prebuilt --prod --token=$VERCEL_TOKEN
```

---

### Step 7: Push the Code to GitLab

#### 7.1 Push the Changes to GitLab

Now that you've created the required files, add, commit, and push the changes to GitLab:

```bash
git add .
git commit -m "Initial commit"
git push origin main
```

---

### Step 8: Verify the Deployment

#### 8.1 Monitor the GitLab Pipeline

Once the code is pushed, monitor the GitLab pipeline:

- Access: `https://gitlab.adinusa.id/USERNAME_GITLAB/vuejs-vercel-username/-/jobs`.
- Ensure the pipeline runs successfully and shows a **"passed"** status.

#### 8.2 Verify the Deployment on Vercel

Click the domain address of your deployment to see the results. Your deployment should be available at:

```bash
https://vuejs-vercel-username.vercel.app/
```

---

## Conclusion

In this lab, you learned how to:
- Set up a **Vue.js** project and integrate it with **GitLab CI/CD**.
- Use **Vercel** for deploying the Vue.js project.
- Automate the deployment process using GitLab CI/CD and Vercel.

This setup allows you to continuously deploy Vue.js applications to Vercel using GitLab's CI/CD features.

Congratulations on completing **Lab 3.1: Integrating GitLab CI/CD with Vercel (Part II)**!
