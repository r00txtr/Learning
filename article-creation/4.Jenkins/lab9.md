# **Lab 3.1: Integrating Jenkins CI/CD with Vercel (Part II)**

In this lab, you will learn how to integrate **Jenkins CI/CD** with **Vercel** to deploy a **Vue.js** project. By the end of this lab, you will have automated the deployment process of a **Vue.js** application to **Vercel** using Jenkins. We will walk through setting up Jenkins, creating a Vue.js project, configuring Vercel, and automating deployments with a Jenkins pipeline.

---

## **Table of Contents**

1. **Introduction**
2. **Step-by-Step Instructions**
    1. Set Up Jenkins Job
    2. Set Up a Vue.js Project
    3. Install Vercel CLI and Configure Environment
    4. Create Jenkins Pipeline Script
    5. Push Code and Trigger Jenkins Build
    6. Verify the Deployment on Vercel
3. **Conclusion**

---

## **Introduction**

In this lab, we will guide you through:
- Setting up a Jenkins pipeline for a **Vue.js** project.
- Using **Vercel** for deploying the Vue.js project.
- Automating the deployment process using Jenkins and Vercel.

---

## **Step-by-Step Instructions**

### **Step 1: Set Up Jenkins Job**

#### **1.1 Log in to Jenkins**

Open Jenkins in your browser:

```
http://your-server-ip:8080
```

Enter your Jenkins credentials to log in.

#### **1.2 Create a New Pipeline Job**

1. On the Jenkins dashboard, click on **New Item**.
2. Enter a project name (e.g., `VueJS-Vercel-Deployment`).
3. Select **Pipeline** and click **OK**.

![Jenkins Create Pipeline Job](https://jenkins.io/doc/book/resources/screenshots/setup/jenkins-create-pipeline-job.png)  
*(Example Screenshot)*

#### **1.3 Configure the Pipeline**

In the project configuration page, scroll down to the **Pipeline** section.

1. Set the **Definition** field to **Pipeline script from SCM**.
2. Select **Git** as the **SCM**, and enter the repository URL (e.g., `https://github.com/your-username/vuejs-vercel-project.git`).
3. In the **Script Path** field, enter `Jenkinsfile`.

---

### **Step 2: Set Up a Vue.js Project**

#### **2.1 Clone Your Repository**

First, clone your GitHub or GitLab repository locally:

```bash
cd ~
git clone https://github.com/your-username/vuejs-vercel-project.git
cd vuejs-vercel-project
```

Replace `your-username` with your actual GitHub or GitLab username.

#### **2.2 Install Vue CLI and Initialize the Project**

To create a Vue.js project, first install **Vue CLI**:

```bash
npm install -g @vue/cli
```

Now, initialize the project using the Vue CLI:

```bash
vue create .
```

Choose the default preset and follow the prompts.

#### **2.3 Modify the Vue.js Project**

Edit the **HelloWorld.vue** component to display a customized message with your username:

```bash
vim src/components/HelloWorld.vue
```

Replace the default welcome message with:

```html
<h1>Welcome to Your Vue.js Project, [your-username]!</h1>
```

---

### **Step 3: Install Vercel CLI and Configure Environment**

#### **3.1 Install Vercel CLI**

To deploy the project to Vercel, install the **Vercel CLI**:

```bash
npm install -g vercel
```

#### **3.2 Log in to Vercel**

Log in to your Vercel account using the CLI:

```bash
vercel login
```

Follow the instructions to authenticate with your Vercel account.

#### **3.3 Link the Project to Vercel**

Link your project to Vercel:

```bash
vercel link
```

This will connect your local project with a Vercel project, allowing for seamless deployment.

#### **3.4 Add Environment Variables to Jenkins**

In Jenkins, go to **Manage Jenkins > Manage Credentials**, and add the following credentials:

1. **VERCEL_TOKEN**: Your Vercel access token (get it from the [Vercel dashboard](https://vercel.com/account/tokens)).
2. **VERCEL_ORG_ID**: Found in `.vercel/project.json` after running `vercel link`.
3. **VERCEL_PROJECT_ID**: Found in `.vercel/project.json`.

---

### **Step 4: Create Jenkins Pipeline Script**

#### **4.1 Create a Jenkinsfile**

In your project directory, create a **Jenkinsfile** to define the pipeline:

```bash
vim Jenkinsfile
```

Add the following content:

```groovy
pipeline {
    agent any
    environment {
        VERCEL_TOKEN = credentials('vercel_token')
    }
    stages {
        stage('Install Dependencies') {
            steps {
                sh 'npm install'
            }
        }
        stage('Build') {
            steps {
                sh 'npm run build'
            }
        }
        stage('Deploy to Vercel') {
            steps {
                sh 'vercel --prod --token=$VERCEL_TOKEN'
            }
        }
    }
}
```

- The **VERCEL_TOKEN** environment variable is used to authenticate with Vercel.
- The pipeline installs the project dependencies, builds the project, and deploys it to Vercel.

---

### **Step 5: Push Code and Trigger Jenkins Build**

#### **5.1 Push the Code to Your Repository**

Once your Jenkinsfile and Vue.js project are ready, add the changes, commit, and push them to your repository:

```bash
git add .
git commit -m "Initial Vue.js setup with Jenkins pipeline"
git push origin main
```

#### **5.2 Trigger the Build in Jenkins**

Go to your Jenkins dashboard and trigger the pipeline:

1. Go to your **VueJS-Vercel-Deployment** project.
2. Click **Build Now** to start the Jenkins pipeline.

Jenkins will pull the code, execute the pipeline, and deploy the application to Vercel.

#### **5.3 Check the Jenkins Console Output**

To view the build progress and logs:

1. Click on the build number (e.g., `#1`).
2. Select **Console Output** to see the details of the build and deployment process.

![Jenkins Console Output](https://jenkins.io/doc/book/resources/screenshots/setup/jenkins-console-output.png)  
*(Example Screenshot)*

---

### **Step 6: Verify the Deployment on Vercel**

#### **6.1 Check Vercel Dashboard**

Once the build is successful, check your Vercel dashboard to verify the deployment:

1. Go to the [Vercel dashboard](https://vercel.com/dashboard).
2. You should see your Vue.js project listed with a unique URL.

#### **6.2 Access the Deployed Application**

Open your browser and navigate to the generated Vercel URL to see your deployed Vue.js application.

```bash
https://vuejs-vercel-your-username.vercel.app/
```

You should see the customized message, confirming that the deployment was successful.

---

## **Conclusion**

In this lab, you successfully integrated Jenkins with Vercel to automate the deployment of a Vue.js application. You learned how to:

1. Set up a Jenkins pipeline for a Vue.js project.
2. Use **Vercel** to host and deploy the application.
3. Automate the entire build and deployment process using Jenkins.

This setup provides you with a scalable and automated way to deploy Vue.js applications using Jenkins CI/CD.

Congratulations on completing **Lab 3.1: Integrating Jenkins CI/CD with Vercel (Part II)**!

Happy Deploying!
