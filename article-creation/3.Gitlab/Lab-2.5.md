# Lab 2.5: Integrating GitLab CI/CD with Vercel

In this lab, you will learn how to integrate **GitLab CI/CD** with **Vercel** to automate the deployment of a simple web application. You will use **GitLab Runner** to manage the deployment and Vercel to host the application. The following steps guide you through setting up a project in GitLab, configuring a GitLab Runner, and deploying the project to Vercel.

---

## Table of Contents
1. **Introduction**
2. **Step-by-Step Instructions**
    1. Create a New Project on GitLab
    2. Clone the Project
    3. Register GitLab Runner Executor with Docker
    4. Install Vercel CLI
    5. Configure Vercel and GitLab CI/CD
    6. Create Web Application Files
    7. Push to GitLab
    8. Monitor the Pipeline and Verify the Deployment
3. **Conclusion**

---

## Introduction

In this lab, you will:
- Create a GitLab project and set up GitLab Runner.
- Use Docker as the executor for GitLab CI/CD pipelines.
- Integrate **Vercel**, a cloud platform, for hosting and deploying your web application.
- Automate the deployment process using GitLab CI/CD and Vercel CLI.

---

## Step-by-Step Instructions

### Step 1: Create a New Project on GitLab

#### 1.1 Create a GitLab Project

1. Open GitLab in your browser and create a new project.
   - **Project Name**: `ci-cd-username` (replace `username` with your GitLab username).
   - **Visibility Level**: `Public`.

This will create a new project called **ci-cd-username** in your GitLab repository.

---

### Step 2: Clone the Project

#### 2.1 Clone the GitLab Project Locally

Now, clone the project to your local environment:

```bash
cd ~
git clone https://gitlab.adinusa.id/USERNAME_GITLAB/ci-cd-username.git
cd ci-cd-username
```

Replace `USERNAME_GITLAB` with your actual GitLab username.

---

### Step 3: Register GitLab Runner Executor with Docker

#### 3.1 Get the Registration Token

1. Go to your GitLab repository in the browser: `https://gitlab.adinusa.id/USERNAME_GITLAB/ci-cd-username`.
2. Navigate to **Settings > CI/CD > Runners > Setup a specific Runner manually**.
3. Copy the **Registration Token**.

#### 3.2 Register the GitLab Runner

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

#### 3.3 Verify GitLab Runner

To verify that the GitLab Runner is registered correctly, run:

```bash
sudo gitlab-runner verify
```

---

### Step 4: Install Vercel CLI

#### 4.1 Install Node Version Manager (NVM) and Vercel CLI

Start by installing **nvm** (Node Version Manager) and **Vercel CLI**:

```bash
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/master/install.sh | bash
source ~/.bashrc
nvm install v16
node --version
npm i -g vercel --allow-root
```

#### 4.2 Log In to Vercel

1. Make sure you have a **Vercel account**. If not, [create one here](https://vercel.com/signup).
2. Inside the `ci-cd-username` folder, run the following command to log in to Vercel:

```bash
vercel login
```

Choose **Continue with Email**, and follow the steps.

#### 4.3 Link the Project to Vercel

Run the following command to link your GitLab project with Vercel:

```bash
vercel link
```

Leave everything as default when prompted.

---

### Step 5: Configure Vercel and GitLab CI/CD

#### 5.1 Set Environment Variables in GitLab

Go to **Settings > CI/CD > Variables** in your GitLab project and add the following variables:

- **VERCEL_TOKEN**: Your Vercel access token (which you can obtain [here](https://vercel.com/account/tokens)).
- **VERCEL_ORG_ID**: Found in `.vercel/project.json` after running `vercel link`.
- **VERCEL_PROJECT_ID**: Found in `.vercel/project.json`.

#### 5.2 Create GitLab CI/CD Configuration

Create a `.gitlab-ci.yml` file to automate the deployment process:

```bash
vim .gitlab-ci.yml
```

Add the following content:

```yaml
image: registry.adinusa.id/btacademy/node:16.16.0

stages:
  - deploy

deploy_preview:
  stage: deploy
  except:
    - main
  script:
    - npm install --global vercel
    - vercel pull --yes --environment=preview --token=$VERCEL_TOKEN
    - vercel build --token=$VERCEL_TOKEN
    - vercel deploy --prebuilt --token=$VERCEL_TOKEN

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

### Step 6: Create Web Application Files

#### 6.1 Create an `index.html` File

```bash
vim index.html
```

Add the following content:

```html
<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8">
    <title>Simple DOM example</title>
  </head>
  <body>
      Angka 1: <input type="text" id="angka1" /><br />
      Angka 2: <input type="text" id="angka2" /><br />
      Operasi: 
      <input type="radio" checked value="penjumlahan" name="operasi" /> Penjumlahan 
      <input type="radio" value="pengurangan" name="operasi" /> Pengurangan 
      <input type="radio" value="perkalian" name="operasi" /> Perkalian
      <input type="radio" value="pembagian" name="operasi" /> Pembagian
      <br />
      <button id="hitung">Hitung</button>
      <h1 style="text-align: center; color: red">Hasil penjumlahan: 200</h1>
      <script type="text/javascript" src="index.js"></script>
      <p align="center">Copyright [username]</p>
    </body>
</html>
```

#### 6.2 Create `package.json`

Create a `package.json` file:

```bash
vim package.json
```

Add the following content:

```json
{
  "name": "vuedidot",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "start": "http-server -a localhost --port=7200"
  },
  "dependencies": {
    "http-server": "^0.11.1",
    "webpack": "^4.16.3",
    "webpack-dev-server": "^3.1.5"
  }
}
```

#### 6.3 Create `index.js`

```bash
vim index.js
```

Add the following JavaScript code:

```javascript
var btn = document.getElementById('hitung');

btn.addEventListener('click', function(evt) {
    var input1 = document.getElementById('angka1');
    var input2 = document.getElementById('angka2');

    var angka1 = parseInt(input1.value);
    var angka2 = parseInt(input2.value);

    var operasi = document.querySelector('input[name=operasi]:checked').value;

    var result = 0;
    switch (operasi) {
        case 'penjumlahan':
            result = angka1 + angka2;
            break;
        case 'pengurangan':
            result = angka1 - angka2;
            break;
        case 'perkalian':
            result = angka1 * angka2;
            break;
        case 'pembagian':
            result = angka1 / angka2;
            break;
    }

    document.querySelector('h1').innerHTML = "Hasil " + operasi + ": " + result;
});
```

---

### Step 7: Push to GitLab

#### 7.1 Push the Changes to GitLab

Now that you've created the required files, add, commit, and push the changes to GitLab:

```bash
git add .
git commit -m "Initial commit"
git push origin main
```

---

### Step 8: Monitor the Pipeline and Verify the Deployment

#### 8.1 Monitor the GitLab Pipeline

Once the code is

 pushed, monitor the GitLab pipeline:

- Access: `https://gitlab.com/[USERNAME_GITLAB]/ci-cd-username/-/pipelines`.

Ensure the pipeline runs successfully and shows a **"passed"** status.

#### 8.2 Verify the Deployment on Vercel

Check your Vercel dashboard to verify the deployment results. You should be able to see the project deployed and accessible on the Vercel URL.

---

## Conclusion

In this lab, you learned how to:
- Set up a **GitLab CI/CD** pipeline to deploy a web application.
- Use **Docker** as the executor for GitLab Runner.
- Integrate **Vercel** for automatic deployments.
- Deploy a simple web application using **GitLab CI/CD** and **Vercel**.

This setup enables continuous deployment and automates the process from code changes to production deployment.

Congratulations on completing **Lab 2.5: Integrating GitLab CI/CD with Vercel**!
